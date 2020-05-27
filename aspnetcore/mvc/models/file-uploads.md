---
<span data-ttu-id="0f2a8-101">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0f2a8-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0f2a8-102">'Blazor'</span></span>
- <span data-ttu-id="0f2a8-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0f2a8-103">'Identity'</span></span>
- <span data-ttu-id="0f2a8-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0f2a8-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0f2a8-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0f2a8-105">'Razor'</span></span>
- <span data-ttu-id="0f2a8-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0f2a8-106">'SignalR' uid:</span></span> 

---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="0f2a8-107">在 ASP.NET Core 中上传文件</span><span class="sxs-lookup"><span data-stu-id="0f2a8-107">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="0f2a8-108">作者： [Steve Smith](https://ardalis.com/)和[Rutger 风暴](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="0f2a8-108">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f2a8-109">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-109">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="0f2a8-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="0f2a8-111">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="0f2a8-111">Security considerations</span></span>

<span data-ttu-id="0f2a8-112">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-112">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="0f2a8-113">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-113">Attackers may attempt to:</span></span>

* <span data-ttu-id="0f2a8-114">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-114">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="0f2a8-115">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-115">Upload viruses or malware.</span></span>
* <span data-ttu-id="0f2a8-116">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-116">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="0f2a8-117">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-117">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="0f2a8-118">将文件上传到专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-118">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="0f2a8-119">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-119">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="0f2a8-120">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="0f2a8-120">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="0f2a8-121">请勿将上传的文件保存在与应用相同的目录树中\*\*\*\*。&dagger;</span><span class="sxs-lookup"><span data-stu-id="0f2a8-121">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="0f2a8-122">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-122">Use a safe file name determined by the app.</span></span> <span data-ttu-id="0f2a8-123">请勿使用用户提供的文件名或上载文件的不受信任的文件名。 &dagger;显示时，HTML 对不受信任的文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-123">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="0f2a8-124">例如，记录文件名或在 UI 中显示（自动对 Razor 输出进行 HTML 编码）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-124">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="0f2a8-125">仅允许应用设计规范的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="0f2a8-125">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="0f2a8-126">验证是否在服务器上执行了客户端检查。 &dagger;客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-126">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="0f2a8-127">检查已上传文件的大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-127">Check the size of an uploaded file.</span></span> <span data-ttu-id="0f2a8-128">设置大小上限以防止上传大型文件。&dagger;</span><span class="sxs-lookup"><span data-stu-id="0f2a8-128">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="0f2a8-129">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-129">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="0f2a8-130">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="0f2a8-130">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="0f2a8-131">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-131">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="0f2a8-132">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-132">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="0f2a8-133">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-133">Completely gain control of a system.</span></span>
> * <span data-ttu-id="0f2a8-134">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-134">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="0f2a8-135">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-135">Compromise user or system data.</span></span>
> * <span data-ttu-id="0f2a8-136">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-136">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="0f2a8-137">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-137">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="0f2a8-138">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-138">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="0f2a8-139">Azure 安全性：确保在接受用户文件时采取适当的控制措施</span><span class="sxs-lookup"><span data-stu-id="0f2a8-139">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="0f2a8-140">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-140">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="0f2a8-141">存储方案</span><span class="sxs-lookup"><span data-stu-id="0f2a8-141">Storage scenarios</span></span>

<span data-ttu-id="0f2a8-142">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-142">Common storage options for files include:</span></span>

* <span data-ttu-id="0f2a8-143">数据库</span><span class="sxs-lookup"><span data-stu-id="0f2a8-143">Database</span></span>

  * <span data-ttu-id="0f2a8-144">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-144">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="0f2a8-145">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-145">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="0f2a8-146">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-146">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="0f2a8-147">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-147">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="0f2a8-148">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-148">For large file uploads:</span></span>
    * <span data-ttu-id="0f2a8-149">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-149">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="0f2a8-150">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-150">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="0f2a8-151">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-151">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="0f2a8-152">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-152">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="0f2a8-153">切勿授予执行权限。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-153">**Never grant execute permission.**</span></span>

* <span data-ttu-id="0f2a8-154">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-154">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="0f2a8-155">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-155">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="0f2a8-156">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-156">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="0f2a8-157">有关详细信息，请参阅[快速入门：使用 .net 在对象存储中创建 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-157">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="0f2a8-158">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="0f2a8-158">File upload scenarios</span></span>

<span data-ttu-id="0f2a8-159">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-159">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="0f2a8-160">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="0f2a8-160">**Buffering**</span></span>

<span data-ttu-id="0f2a8-161">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-161">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="0f2a8-162">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-162">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="0f2a8-163">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-163">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="0f2a8-164">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-164">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="0f2a8-165">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-165">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="0f2a8-166">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-166">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="0f2a8-167">物理存储</span><span class="sxs-lookup"><span data-stu-id="0f2a8-167">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="0f2a8-168">Database</span><span class="sxs-lookup"><span data-stu-id="0f2a8-168">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="0f2a8-169">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="0f2a8-169">**Streaming**</span></span>

<span data-ttu-id="0f2a8-170">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-170">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="0f2a8-171">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-171">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="0f2a8-172">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-172">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="0f2a8-173">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-173">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="0f2a8-174">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="0f2a8-174">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="0f2a8-175">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-175">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="0f2a8-176">下面的示例演示 Razor 如何使用页面窗体上传一个文件（示例应用中的*Pages/BufferedSingleFileUploadPhysical* ）：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-176">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="0f2a8-177">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-177">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="0f2a8-178">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-178">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="0f2a8-179">无验证。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-179">There's no validation.</span></span>

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

<span data-ttu-id="0f2a8-180">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-180">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="0f2a8-181">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-181">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="0f2a8-182">改用 `XMLHttpRequest`</span><span class="sxs-lookup"><span data-stu-id="0f2a8-182">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="0f2a8-183">例如：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-183">For example:</span></span>

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

<span data-ttu-id="0f2a8-184">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-184">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="0f2a8-185">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-185">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="0f2a8-186">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-186">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="0f2a8-187">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-187">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="0f2a8-188">除了显示和日志记录用途外，请勿使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-188">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="0f2a8-189">显示或日志记录时，HTML 对文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-189">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="0f2a8-190">攻击者可以提供恶意文件名，包括完整路径或相对路径。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-190">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="0f2a8-191">应用程序应：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-191">Applications should:</span></span>
>
> * <span data-ttu-id="0f2a8-192">从用户提供的文件名中删除路径。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-192">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="0f2a8-193">为 UI 或日志记录保存经 HTML 编码、已删除路径的文件名。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-193">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="0f2a8-194">生成新的随机文件名进行存储。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-194">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="0f2a8-195">以下代码可从文件名中删除路径：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-195">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="0f2a8-196">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-196">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="0f2a8-197">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-197">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="0f2a8-198">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="0f2a8-198">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="0f2a8-199">验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-199">Validation</span></span>](#validation)

<span data-ttu-id="0f2a8-200">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-200">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="0f2a8-201">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-201">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="0f2a8-202">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-202">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="0f2a8-203">[成员列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="0f2a8-203">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="0f2a8-204">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-204">Binding matches form files by name.</span></span> <span data-ttu-id="0f2a8-205">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-205">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="0f2a8-206">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-206">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="0f2a8-207">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-207">The following example:</span></span>

* <span data-ttu-id="0f2a8-208">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-208">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="0f2a8-209">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-209">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="0f2a8-210">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-210">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="0f2a8-211">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-211">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="0f2a8-212">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-212">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="0f2a8-213">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-213">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="0f2a8-214">传递到  的路径必须包含文件名<xref:System.IO.FileStream> \*\*。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-214">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="0f2a8-215">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-215">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="0f2a8-216">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-216">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="0f2a8-217">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-217">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="0f2a8-218">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-218">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="0f2a8-219">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-219">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="0f2a8-220">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="0f2a8-221">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-221">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="0f2a8-222">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-222">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="0f2a8-223">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="0f2a8-223">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="0f2a8-224">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="0f2a8-224">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="0f2a8-225">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-225">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="0f2a8-226">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-226">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="0f2a8-227"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-227"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="0f2a8-228">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-228">The prior example uses a bound model property.</span></span>

<span data-ttu-id="0f2a8-229">`FileUpload`在 Razor 页面窗体中使用：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-229">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="0f2a8-230">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-230">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="0f2a8-231">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-231">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="0f2a8-232">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-232">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="0f2a8-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="0f2a8-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="0f2a8-235">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-235">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="0f2a8-236">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-236">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="0f2a8-237">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-237">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="0f2a8-238">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-238">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="0f2a8-239">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-239">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="0f2a8-240">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="0f2a8-240">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="0f2a8-241">验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-241">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="0f2a8-242">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="0f2a8-242">Upload large files with streaming</span></span>

<span data-ttu-id="0f2a8-243">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-243">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="0f2a8-244">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-244">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="0f2a8-245">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-245">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="0f2a8-246">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-246">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="0f2a8-247">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-247">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="0f2a8-248">初始页响应加载窗体并将防伪令牌保存在 Cookie 中（通过 `GenerateAntiforgeryTokenCookieAttribute` 属性）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-248">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="0f2a8-249">该属性使用 ASP.NET Core 的内置[防伪支持](xref:security/anti-request-forgery)来设置带有请求令牌的 cookie：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-249">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="0f2a8-250">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-250">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="0f2a8-251">在示例应用中， `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 页面约定](xref:razor-pages/razor-pages-conventions)作为筛选器应用于和的页面应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-251">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="0f2a8-252">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-252">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="0f2a8-253">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-253">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="0f2a8-254">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-254">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="0f2a8-255">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-255">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="0f2a8-256">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-256">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="0f2a8-257">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-257">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="0f2a8-258">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)：\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="0f2a8-259">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-259">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="0f2a8-260">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-260">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="0f2a8-261">验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-261">Validation</span></span>

<span data-ttu-id="0f2a8-262">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-262">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="0f2a8-263">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-263">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="0f2a8-264">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-264">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="0f2a8-265">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-265">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="0f2a8-266">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-266">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="0f2a8-267">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-267">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="0f2a8-268">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-268">Fully understand the implementation.</span></span>
> * <span data-ttu-id="0f2a8-269">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-269">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="0f2a8-270">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="0f2a8-270">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="0f2a8-271">内容验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-271">Content validation</span></span>

<span data-ttu-id="0f2a8-272">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-272">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="0f2a8-273">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-273">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="0f2a8-274">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-274">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="0f2a8-275">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-275">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="0f2a8-276">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-276">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="0f2a8-277">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-277">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="0f2a8-278">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-278">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="0f2a8-279">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-279">File extension validation</span></span>

<span data-ttu-id="0f2a8-280">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-280">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="0f2a8-281">例如：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-281">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="0f2a8-282">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-282">File signature validation</span></span>

<span data-ttu-id="0f2a8-283">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-283">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="0f2a8-284">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-284">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="0f2a8-285">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-285">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="0f2a8-286">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-286">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="0f2a8-287">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-287">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="0f2a8-288">文件名安全</span><span class="sxs-lookup"><span data-stu-id="0f2a8-288">File name security</span></span>

<span data-ttu-id="0f2a8-289">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-289">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="0f2a8-290">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-290">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="0f2a8-291">自动对属性值进行 HTML 编码以便显示。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-291"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="0f2a8-292">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-292">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="0f2a8-293">在之外 Razor ，始终 <xref:System.Net.WebUtility.HtmlEncode*> 根据用户的请求来命名内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-293">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="0f2a8-294">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-294">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="0f2a8-295">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-295">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="0f2a8-296">大小验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-296">Size validation</span></span>

<span data-ttu-id="0f2a8-297">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-297">Limit the size of uploaded files.</span></span>

<span data-ttu-id="0f2a8-298">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-298">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="0f2a8-299">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-299">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="0f2a8-300">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-300">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="0f2a8-301">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-301">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="0f2a8-302">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="0f2a8-302">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="0f2a8-303">在 Razor 发布窗体数据或直接使用 JavaScript 的非窗体中 `FormData` ，在窗体的元素中指定的名称或 `FormData` 必须与控制器的操作中参数的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-303">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="0f2a8-304">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-304">In the following example:</span></span>

* <span data-ttu-id="0f2a8-305">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-305">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="0f2a8-306">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-306">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="0f2a8-307">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-307">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="0f2a8-308">对于名为的页 Razor 页面处理程序方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-308">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="0f2a8-309">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-309">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="0f2a8-310">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="0f2a8-310">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="0f2a8-311">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="0f2a8-311">Multipart body length limit</span></span>

<span data-ttu-id="0f2a8-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="0f2a8-313">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-313">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="0f2a8-314">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-314">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="0f2a8-315">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-315">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="0f2a8-316">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="0f2a8-317">在 Razor 页面应用中，将筛选器应用于中的[约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-317">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="0f2a8-318">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-318">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="0f2a8-319">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="0f2a8-319">Kestrel maximum request body size</span></span>

<span data-ttu-id="0f2a8-320">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-320">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="0f2a8-321">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-321">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="0f2a8-322">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="0f2a8-323">在 Razor 页面应用中，将筛选器应用于中的[约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-323">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="0f2a8-324">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-324">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="0f2a8-325">`RequestSizeLimitAttribute`还可以使用 [`@attribute`](xref:mvc/views/razor#attribute) Razor 指令应用：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-325">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="0f2a8-326">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="0f2a8-326">Other Kestrel limits</span></span>

<span data-ttu-id="0f2a8-327">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-327">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="0f2a8-328">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="0f2a8-328">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="0f2a8-329">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="0f2a8-329">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="0f2a8-330">IIS 内容长度限制</span><span class="sxs-lookup"><span data-stu-id="0f2a8-330">IIS content length limit</span></span>

<span data-ttu-id="0f2a8-331">默认的请求限制 (`maxAllowedContentLength`) 为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-331">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="0f2a8-332">请在 web.config 文件中自定义此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-332">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="0f2a8-333">此设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-333">This setting only applies to IIS.</span></span> <span data-ttu-id="0f2a8-334">在 Kestrel 上托管时，默认情况下不会出现此行为。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-334">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="0f2a8-335">有关详细信息，请参阅[请求限制 \< requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-335">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="0f2a8-336">ASP.NET Core 模块中的限制或 IIS 请求筛选模块的存在可能会将上传限制在 2 或 4 GB。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-336">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="0f2a8-337">有关详细信息，请参阅[无法上传大小超出 2 GB 的文件 (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-337">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0f2a8-338">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0f2a8-338">Troubleshoot</span></span>

<span data-ttu-id="0f2a8-339">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-339">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="0f2a8-340">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="0f2a8-340">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="0f2a8-341">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-341">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="0f2a8-342">有关提高此限制的详细信息，请参阅 [IIS 内容长度限制](#iis-content-length-limit)部分。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-342">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="0f2a8-343">连接失败</span><span class="sxs-lookup"><span data-stu-id="0f2a8-343">Connection failure</span></span>

<span data-ttu-id="0f2a8-344">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-344">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="0f2a8-345">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-345">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="0f2a8-346">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-346">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="0f2a8-347">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="0f2a8-347">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="0f2a8-348">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-348">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="0f2a8-349">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-349">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="0f2a8-350">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-350">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="0f2a8-351">数据流太长</span><span class="sxs-lookup"><span data-stu-id="0f2a8-351">Stream was too long</span></span>

<span data-ttu-id="0f2a8-352">本主题中的示例依赖于 <xref:System.IO.MemoryStream> 来保存已上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-352">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="0f2a8-353">`MemoryStream` 的大小限制为 `int.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-353">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="0f2a8-354">如果应用的文件上传方案要求保存大于 50 MB 的文件内容，请使用另一种方法，该方法不依赖单个 `MemoryStream` 来保存已上传文件的内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-354">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0f2a8-355">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-355">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="0f2a8-356">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="0f2a8-357">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="0f2a8-357">Security considerations</span></span>

<span data-ttu-id="0f2a8-358">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-358">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="0f2a8-359">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-359">Attackers may attempt to:</span></span>

* <span data-ttu-id="0f2a8-360">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-360">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="0f2a8-361">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-361">Upload viruses or malware.</span></span>
* <span data-ttu-id="0f2a8-362">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-362">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="0f2a8-363">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-363">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="0f2a8-364">将文件上传到专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-364">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="0f2a8-365">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-365">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="0f2a8-366">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="0f2a8-366">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="0f2a8-367">请勿将上传的文件保存在与应用相同的目录树中\*\*\*\*。&dagger;</span><span class="sxs-lookup"><span data-stu-id="0f2a8-367">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="0f2a8-368">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-368">Use a safe file name determined by the app.</span></span> <span data-ttu-id="0f2a8-369">请勿使用用户提供的文件名或上载文件的不受信任的文件名。 &dagger;显示时，HTML 对不受信任的文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-369">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="0f2a8-370">例如，记录文件名或在 UI 中显示（自动对 Razor 输出进行 HTML 编码）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-370">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="0f2a8-371">仅允许应用设计规范的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="0f2a8-371">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="0f2a8-372">验证是否在服务器上执行了客户端检查。 &dagger;客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-372">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="0f2a8-373">检查已上传文件的大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-373">Check the size of an uploaded file.</span></span> <span data-ttu-id="0f2a8-374">设置大小上限以防止上传大型文件。&dagger;</span><span class="sxs-lookup"><span data-stu-id="0f2a8-374">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="0f2a8-375">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-375">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="0f2a8-376">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="0f2a8-376">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="0f2a8-377">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-377">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="0f2a8-378">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-378">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="0f2a8-379">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-379">Completely gain control of a system.</span></span>
> * <span data-ttu-id="0f2a8-380">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-380">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="0f2a8-381">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-381">Compromise user or system data.</span></span>
> * <span data-ttu-id="0f2a8-382">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-382">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="0f2a8-383">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-383">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="0f2a8-384">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-384">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="0f2a8-385">Azure 安全性：确保在接受用户文件时采取适当的控制措施</span><span class="sxs-lookup"><span data-stu-id="0f2a8-385">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="0f2a8-386">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-386">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="0f2a8-387">存储方案</span><span class="sxs-lookup"><span data-stu-id="0f2a8-387">Storage scenarios</span></span>

<span data-ttu-id="0f2a8-388">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-388">Common storage options for files include:</span></span>

* <span data-ttu-id="0f2a8-389">数据库</span><span class="sxs-lookup"><span data-stu-id="0f2a8-389">Database</span></span>

  * <span data-ttu-id="0f2a8-390">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-390">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="0f2a8-391">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-391">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="0f2a8-392">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-392">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="0f2a8-393">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-393">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="0f2a8-394">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-394">For large file uploads:</span></span>
    * <span data-ttu-id="0f2a8-395">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-395">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="0f2a8-396">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-396">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="0f2a8-397">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-397">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="0f2a8-398">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-398">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="0f2a8-399">切勿授予执行权限。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-399">**Never grant execute permission.**</span></span>

* <span data-ttu-id="0f2a8-400">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-400">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="0f2a8-401">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-401">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="0f2a8-402">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-402">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="0f2a8-403">有关详细信息，请参阅[快速入门：使用 .net 在对象存储中创建 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-403">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="0f2a8-404">此主题说明了 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>，但在处理 <xref:System.IO.Stream> 时，可以使用 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> 将 <xref:System.IO.FileStream> 保存到 blob 存储。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-404">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="0f2a8-405">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="0f2a8-405">File upload scenarios</span></span>

<span data-ttu-id="0f2a8-406">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-406">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="0f2a8-407">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="0f2a8-407">**Buffering**</span></span>

<span data-ttu-id="0f2a8-408">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-408">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="0f2a8-409">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-409">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="0f2a8-410">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-410">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="0f2a8-411">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-411">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="0f2a8-412">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-412">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="0f2a8-413">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-413">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="0f2a8-414">物理存储</span><span class="sxs-lookup"><span data-stu-id="0f2a8-414">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="0f2a8-415">Database</span><span class="sxs-lookup"><span data-stu-id="0f2a8-415">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="0f2a8-416">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="0f2a8-416">**Streaming**</span></span>

<span data-ttu-id="0f2a8-417">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-417">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="0f2a8-418">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-418">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="0f2a8-419">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-419">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="0f2a8-420">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-420">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="0f2a8-421">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="0f2a8-421">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="0f2a8-422">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-422">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="0f2a8-423">下面的示例演示 Razor 如何使用页面窗体上传一个文件（示例应用中的*Pages/BufferedSingleFileUploadPhysical* ）：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-423">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="0f2a8-424">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-424">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="0f2a8-425">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-425">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="0f2a8-426">无验证。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-426">There's no validation.</span></span>

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

<span data-ttu-id="0f2a8-427">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-427">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="0f2a8-428">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-428">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="0f2a8-429">改用 `XMLHttpRequest`</span><span class="sxs-lookup"><span data-stu-id="0f2a8-429">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="0f2a8-430">例如：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-430">For example:</span></span>

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

<span data-ttu-id="0f2a8-431">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-431">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="0f2a8-432">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-432">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="0f2a8-433">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-433">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="0f2a8-434">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-434">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="0f2a8-435">除了显示和日志记录用途外，请勿使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-435">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="0f2a8-436">显示或日志记录时，HTML 对文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-436">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="0f2a8-437">攻击者可以提供恶意文件名，包括完整路径或相对路径。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-437">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="0f2a8-438">应用程序应：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-438">Applications should:</span></span>
>
> * <span data-ttu-id="0f2a8-439">从用户提供的文件名中删除路径。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-439">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="0f2a8-440">为 UI 或日志记录保存经 HTML 编码、已删除路径的文件名。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-440">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="0f2a8-441">生成新的随机文件名进行存储。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-441">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="0f2a8-442">以下代码可从文件名中删除路径：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-442">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="0f2a8-443">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-443">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="0f2a8-444">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-444">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="0f2a8-445">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="0f2a8-445">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="0f2a8-446">验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-446">Validation</span></span>](#validation)

<span data-ttu-id="0f2a8-447">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-447">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="0f2a8-448">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-448">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="0f2a8-449">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-449">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="0f2a8-450">[成员列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="0f2a8-450">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="0f2a8-451">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-451">Binding matches form files by name.</span></span> <span data-ttu-id="0f2a8-452">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-452">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="0f2a8-453">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-453">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="0f2a8-454">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-454">The following example:</span></span>

* <span data-ttu-id="0f2a8-455">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-455">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="0f2a8-456">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-456">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="0f2a8-457">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-457">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="0f2a8-458">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-458">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="0f2a8-459">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-459">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="0f2a8-460">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-460">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="0f2a8-461">传递到  的路径必须包含文件名<xref:System.IO.FileStream> \*\*。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-461">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="0f2a8-462">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-462">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="0f2a8-463">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-463">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="0f2a8-464">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-464">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="0f2a8-465">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-465">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="0f2a8-466">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-466">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="0f2a8-467">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="0f2a8-468">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-468">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="0f2a8-469">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-469">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="0f2a8-470">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="0f2a8-470">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="0f2a8-471">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="0f2a8-471">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="0f2a8-472">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-472">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="0f2a8-473">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-473">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="0f2a8-474"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-474"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="0f2a8-475">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-475">The prior example uses a bound model property.</span></span>

<span data-ttu-id="0f2a8-476">`FileUpload`在 Razor 页面窗体中使用：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-476">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="0f2a8-477">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-477">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="0f2a8-478">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-478">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="0f2a8-479">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-479">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="0f2a8-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="0f2a8-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="0f2a8-482">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-482">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="0f2a8-483">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-483">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="0f2a8-484">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-484">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="0f2a8-485">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-485">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="0f2a8-486">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-486">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="0f2a8-487">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="0f2a8-487">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="0f2a8-488">验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-488">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="0f2a8-489">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="0f2a8-489">Upload large files with streaming</span></span>

<span data-ttu-id="0f2a8-490">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-490">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="0f2a8-491">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-491">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="0f2a8-492">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-492">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="0f2a8-493">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-493">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="0f2a8-494">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-494">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="0f2a8-495">初始页响应加载窗体并将防伪令牌保存在 Cookie 中（通过 `GenerateAntiforgeryTokenCookieAttribute` 属性）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-495">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="0f2a8-496">该属性使用 ASP.NET Core 的内置[防伪支持](xref:security/anti-request-forgery)来设置带有请求令牌的 cookie：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-496">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="0f2a8-497">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-497">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="0f2a8-498">在示例应用中， `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 页面约定](xref:razor-pages/razor-pages-conventions)作为筛选器应用于和的页面应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-498">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="0f2a8-499">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-499">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="0f2a8-500">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-500">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="0f2a8-501">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-501">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="0f2a8-502">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-502">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="0f2a8-503">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-503">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="0f2a8-504">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-504">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="0f2a8-505">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)：\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="0f2a8-506">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-506">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="0f2a8-507">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-507">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="0f2a8-508">验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-508">Validation</span></span>

<span data-ttu-id="0f2a8-509">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-509">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="0f2a8-510">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-510">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="0f2a8-511">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-511">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="0f2a8-512">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-512">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="0f2a8-513">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-513">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="0f2a8-514">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-514">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="0f2a8-515">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-515">Fully understand the implementation.</span></span>
> * <span data-ttu-id="0f2a8-516">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-516">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="0f2a8-517">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="0f2a8-517">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="0f2a8-518">内容验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-518">Content validation</span></span>

<span data-ttu-id="0f2a8-519">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-519">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="0f2a8-520">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-520">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="0f2a8-521">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-521">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="0f2a8-522">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-522">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="0f2a8-523">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-523">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="0f2a8-524">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-524">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="0f2a8-525">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-525">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="0f2a8-526">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-526">File extension validation</span></span>

<span data-ttu-id="0f2a8-527">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-527">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="0f2a8-528">例如：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-528">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="0f2a8-529">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-529">File signature validation</span></span>

<span data-ttu-id="0f2a8-530">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-530">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="0f2a8-531">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-531">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="0f2a8-532">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-532">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="0f2a8-533">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-533">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="0f2a8-534">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-534">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="0f2a8-535">文件名安全</span><span class="sxs-lookup"><span data-stu-id="0f2a8-535">File name security</span></span>

<span data-ttu-id="0f2a8-536">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-536">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="0f2a8-537">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-537">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="0f2a8-538">自动对属性值进行 HTML 编码以便显示。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-538"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="0f2a8-539">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-539">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="0f2a8-540">在之外 Razor ，始终 <xref:System.Net.WebUtility.HtmlEncode*> 根据用户的请求来命名内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-540">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="0f2a8-541">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-541">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="0f2a8-542">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-542">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="0f2a8-543">大小验证</span><span class="sxs-lookup"><span data-stu-id="0f2a8-543">Size validation</span></span>

<span data-ttu-id="0f2a8-544">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-544">Limit the size of uploaded files.</span></span>

<span data-ttu-id="0f2a8-545">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-545">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="0f2a8-546">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-546">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="0f2a8-547">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-547">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="0f2a8-548">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-548">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="0f2a8-549">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="0f2a8-549">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="0f2a8-550">在 Razor 发布窗体数据或直接使用 JavaScript 的非窗体中 `FormData` ，在窗体的元素中指定的名称或 `FormData` 必须与控制器的操作中参数的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-550">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="0f2a8-551">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-551">In the following example:</span></span>

* <span data-ttu-id="0f2a8-552">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-552">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="0f2a8-553">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-553">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="0f2a8-554">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-554">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="0f2a8-555">对于名为的页 Razor 页面处理程序方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-555">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="0f2a8-556">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-556">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="0f2a8-557">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="0f2a8-557">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="0f2a8-558">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="0f2a8-558">Multipart body length limit</span></span>

<span data-ttu-id="0f2a8-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="0f2a8-560">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-560">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="0f2a8-561">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-561">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="0f2a8-562">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-562">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="0f2a8-563">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="0f2a8-564">在 Razor 页面应用中，将筛选器应用于中的[约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-564">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="0f2a8-565">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-565">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="0f2a8-566">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="0f2a8-566">Kestrel maximum request body size</span></span>

<span data-ttu-id="0f2a8-567">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-567">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="0f2a8-568">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-568">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="0f2a8-569">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="0f2a8-570">在 Razor 页面应用中，将筛选器应用于中的[约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-570">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="0f2a8-571">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-571">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="0f2a8-572">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="0f2a8-572">Other Kestrel limits</span></span>

<span data-ttu-id="0f2a8-573">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-573">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="0f2a8-574">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="0f2a8-574">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="0f2a8-575">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="0f2a8-575">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="0f2a8-576">IIS 内容长度限制</span><span class="sxs-lookup"><span data-stu-id="0f2a8-576">IIS content length limit</span></span>

<span data-ttu-id="0f2a8-577">默认的请求限制 (`maxAllowedContentLength`) 为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-577">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="0f2a8-578">请在 web.config 文件中自定义此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="0f2a8-578">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="0f2a8-579">此设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-579">This setting only applies to IIS.</span></span> <span data-ttu-id="0f2a8-580">在 Kestrel 上托管时，默认情况下不会出现此行为。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-580">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="0f2a8-581">有关详细信息，请参阅[请求限制 \< requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-581">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="0f2a8-582">ASP.NET Core 模块中的限制或 IIS 请求筛选模块的存在可能会将上传限制在 2 或 4 GB。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-582">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="0f2a8-583">有关详细信息，请参阅[无法上传大小超出 2 GB 的文件 (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-583">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0f2a8-584">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0f2a8-584">Troubleshoot</span></span>

<span data-ttu-id="0f2a8-585">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-585">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="0f2a8-586">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="0f2a8-586">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="0f2a8-587">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="0f2a8-587">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="0f2a8-588">有关提高此限制的详细信息，请参阅 [IIS 内容长度限制](#iis-content-length-limit)部分。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-588">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="0f2a8-589">连接失败</span><span class="sxs-lookup"><span data-stu-id="0f2a8-589">Connection failure</span></span>

<span data-ttu-id="0f2a8-590">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-590">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="0f2a8-591">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-591">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="0f2a8-592">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-592">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="0f2a8-593">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="0f2a8-593">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="0f2a8-594">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-594">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="0f2a8-595">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-595">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="0f2a8-596">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-596">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="0f2a8-597">数据流太长</span><span class="sxs-lookup"><span data-stu-id="0f2a8-597">Stream was too long</span></span>

<span data-ttu-id="0f2a8-598">本主题中的示例依赖于 <xref:System.IO.MemoryStream> 来保存已上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-598">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="0f2a8-599">`MemoryStream` 的大小限制为 `int.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-599">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="0f2a8-600">如果应用的文件上传方案要求保存大于 50 MB 的文件内容，请使用另一种方法，该方法不依赖单个 `MemoryStream` 来保存已上传文件的内容。</span><span class="sxs-lookup"><span data-stu-id="0f2a8-600">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="0f2a8-601">其他资源</span><span class="sxs-lookup"><span data-stu-id="0f2a8-601">Additional resources</span></span>

* [<span data-ttu-id="0f2a8-602">HTTP 连接请求排出</span><span class="sxs-lookup"><span data-stu-id="0f2a8-602">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* <span data-ttu-id="0f2a8-603">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="0f2a8-603">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="0f2a8-604">Azure 安全：安全框架：输入验证 |措施</span><span class="sxs-lookup"><span data-stu-id="0f2a8-604">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="0f2a8-605">Azure 云设计模式：附属密钥模式</span><span class="sxs-lookup"><span data-stu-id="0f2a8-605">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
