---
title: Performance Tuning for Web Servers
description: Performance Tuning for Web Servers
---

# Performance Tuning for Web Servers


This topic describes performance tuning methods and recommendations for Windows Server 2012 R2 web servers.

**In this topic:**

-   [Selecting the proper hardware for performance](#hardware)

-   [Operating system best practices](# os)

-   [Tuning IIS 8.5](#iis)

-   [NTFS file system setting](#filesystem)

-   [Networking subsystem performance settings for IIS](#netsub)

## <a href="" id="hardware"></a>Selecting the proper hardware for performance


It is important to select the proper hardware to satisfy the expected web load, considering average load, peak load, capacity, growth plans, and response times. Hardware bottlenecks limit the effectiveness of software tuning.

[Performance Tuning for Server Hardware](performance-tuning-for-server-hardware.md) provides recommendations for hardware to avoid the following performance constraints:

-   Slow CPUs offer limited processing power for CPU intensive workloads such as ASP, ASP.NET, and SSL scenarios.

-   A small L2 or L3/LLC processor cache might adversely affect performance.

-   A limited amount of memory affects the number of sites that can be hosted, how many dynamic content scripts (such as ASP.NET) can be stored, and the number of application pools or worker processes.

-   Networking becomes a bottleneck because of an inefficient network adapter.

-   The file system becomes a bottleneck because of an inefficient disk subsystem or storage adapter.

## <a href="" id="-os"></a>Operating system best practices


If possible, start with a clean installation of the operating system. Upgrading the software can leave outdated, unwanted, or suboptimal registry settings and previously installed services and applications that consume resources if they are started automatically. If another operating system is installed and you must keep it, you should install the new operating system on a different partition. Otherwise, the new installation overwrites the settings under %Program Files%\\Common Files.

To reduce disk access interference, place the system page file, operating system, web data, ASP template cache, and the Internet Information Services (IIS) log on separate physical disks, if possible.

To reduce contention for system resources, install Microsoft SQL Server and IIS on different servers, if possible.

Avoid installing non-essential services and applications. In some cases, it might be worthwhile to disable services that are not required on a system.

## <a href="" id="iis"></a>Tuning IIS 8.5


Internet Information Services (IIS) 8.5 is included with Windows Server 2012 R2. It uses a process model similar to that of IIS 7.0. A kernel-mode web driver (http.sys) receives and routes HTTP requests, and satisfies requests from its response cache. Worker processes register for URL subspaces, and http.sys routes the request to the appropriate process (or set of processes for application pools).

HTTP.sys is responsible for connection management and request handling. The request can be served from the HTTP.sys cache or passed to a worker process for further handling. Multiple worker processes can be configured, which provides isolation at a reduced cost. For more info on how request handling works, see the following figure:

![request handling in iis 8.5](../media/performance-tuning/perftune-guide-iis-request-handling.png)

HTTP.sys includes a response cache. When a request matches an entry in the response cache, HTTP.sys sends the cache response directly from kernel mode. Some web application platforms, such as ASP.NET, provide mechanisms to enable any dynamic content to be cached in the kernel-mode cache. The static file handler in IIS 8.5 automatically caches frequently requested files in http.sys.

Because a web server has kernel-mode and user-mode components, both components must be tuned for optimal performance. Therefore, tuning IIS 8.5 for a specific workload includes configuring the following:

-   HTTP.sys and the associated kernel-mode cache

-   Worker processes and user-mode IIS, including the application pool configuration

-   Certain tuning parameters that affect performance

The following sections discuss how to configure the kernel-mode and user-mode aspects of IIS 8.5.

### Kernel-mode settings

Performance-related http.sys settings fall into two broad categories: cache management and connection and request management. All registry settings are stored under the following registry entry:

``` syntax
HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Http\Parameters
```

**Note**  
If the HTTP service is already running, you must restart it for the changes to take effect.

 

### Cache management settings

One benefit that HTTP.sys provides is a kernel-mode cache. If the response is in the kernel-mode cache, you can satisfy an HTTP request entirely from the kernel mode, which significantly lowers the CPU cost of handling the request. However, the kernel-mode cache of IIS 8.5 is based on physical memory, and the cost of an entry is the memory that it occupies.

An entry in the cache is helpful only when it is used. However, the entry always consumes physical memory, whether or not the entry is being used. You must evaluate the usefulness of an item in the cache (the savings from being able to serve it from the cache) and its cost (the physical memory occupied) over the lifetime of the entry by considering the available resources (CPU and physical memory) and the workload requirements. HTTP.sys tries to keep only useful, actively accessed items in the cache, but you can increase the performance of the web server by tuning the http.sys cache for particular workloads.

The following are some useful settings for the HTTP.sys kernel-mode cache:

-   **UriEnableCache** Default value: 1

    A non-zero value enables the kernel-mode response and fragment caching. For most workloads, the cache should remain enabled. Consider disabling the cache if you expect a very low response and fragment caching.

-   **UriMaxCacheMegabyteCount** Default value: 0

    A non-zero value that specifies the maximum memory that is available to the kernel-mode cache. The default value, 0, enables the system to automatically adjust how much memory is available to the cache.

    **Note**  
    Specifying the size sets only the maximum, and the system might not let the cache grow to the maximum set size.

     

-   **UriMaxUriBytes** Default value: 262144 bytes (256 KB)

    The maximum size of an entry in the kernel-mode cache. Responses or fragments larger than this are not cached. If you have enough memory, consider increasing the limit. If memory is limited and large entries are crowding out smaller ones, it might be helpful to lower the limit.

-   **UriScavengerPeriod** Default value: 120 seconds

    The HTTP.sys cache is periodically scanned by a scavenger, and entries that are not accessed between scavenger scans are removed. Setting the scavenger period to a high value reduces the number of scavenger scans. However, the cache memory usage might increase because older, less frequently accessed entries can remain in the cache. Setting the period too low causes more frequent scavenger scans, and it can result in too many flushes and cache churn.

### Request and connection management settings

In Windows Server 2012 R2, http.sys manages connections automatically. The following registry settings are no longer used:

-   **MaxConnections**

    ``` syntax
    HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Http\Parameters\MaxConnections
    ```

-   **IdleConnectionsHighMark**

    ``` syntax
    HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Http\Parameters\IdleConnectionsHighMark
    ```

-   **IdleConnectionsLowMark**

    ``` syntax
    HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Http\Parameters\IdleConnectionsLowMark
    ```

-   **IdleListTrimmerPeriod**

    ``` syntax
    HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Http\Parameters\IdleListTrimmerPeriod
    ```

-   **RequestBufferLookasideDepth**

    ``` syntax
    HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Http\Parameters\RequestBufferLookasideDepth
    ```

-   **InternalRequestLookasideDepth**

    ``` syntax
    HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Http\Parameters\InternalRequestLookasideDepth
    ```

### User-mode settings

The settings in this section affect the IIS 8.5 worker process behavior. Most of these settings can be found in the following XML configuration file:

%SystemRoot%\\system32\\inetsrv\\config\\applicationHost.config

Use Appcmd.exe or the IIS 8.5 Management Console to change them. Most settings are automatically detected, and they do not require a restart of the IIS 8.5 worker processes or web application server. For more info about the applicationHost.config file, see [Introduction to ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig).

### User-mode cache behavior settings

This section describes the settings that affect caching behavior in IIS 8.5. The user-mode cache is implemented as a module that listens to the global caching events that are raised by the integrated pipeline. To completely disable the user-mode cache, remove the FileCacheModule (cachfile.dll) module from the list of installed modules in the system.webServer/globalModules configuration section in applicationHost.config.

**system.webServer/caching**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Enabled</p></td>
<td><p>Disables the user-mode IIS cache when set to <strong>False</strong>. When the cache hit rate is very small, you can disable the cache completely to avoid the overhead that is associated with the cache code path. Disabling the user-mode cache does not disable the kernel-mode cache.</p></td>
<td><p>True</p></td>
</tr>
<tr class="even">
<td><p>enableKernelCache</p></td>
<td><p>Disables the kernel-mode cache when set to <strong>False</strong>.</p></td>
<td><p>True</p></td>
</tr>
<tr class="odd">
<td><p>maxCacheSize</p></td>
<td><p>Limits the IIS user-mode cache size to the specified size in Megabytes. IIS adjusts the default depending on available memory. Choose the value carefully based on the size of the set of frequently accessed files versus the amount of RAM or the IIS process address space.</p></td>
<td><p>0</p></td>
</tr>
<tr class="even">
<td><p>maxResponseSize</p></td>
<td><p>Caches files up to the specified size. The actual value depends on the number and size of the largest files in the data set versus the available RAM. Caching large, frequently requested files can reduce CPU usage, disk access, and associated latencies.</p></td>
<td><p>262144</p></td>
</tr>
</tbody>
</table>

 

### Compression behavior settings

IIS starting from 7.0 compresses static content by default. Also, compression of dynamic content is enabled by default when the DynamicCompressionModule is installed. Compression reduces bandwidth usage but increases CPU usage. Compressed content is cached in the kernel-mode cache if possible. IIS 8.5 lets compression be controlled independently for static and dynamic content. Static content typically refers to content that does not change, such as GIF or HTM files. Dynamic content is typically generated by scripts or code on the server, that is, ASP.NET pages. You can customize the classification of any particular extension as static or dynamic.

To completely disable compression, remove StaticCompressionModule and DynamicCompressionModule from the list of modules in the system.webServer/globalModules section in applicationHost.config.

**system.webServer/httpCompression**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>staticCompression-EnableCpuUsage</p>
<p>staticCompression-DisableCpuUsage</p>
<p>dynamicCompression-EnableCpuUsage</p>
<p>dynamicCompression-DisableCpuUsage</p></td>
<td><p>Enables or disables compression if the current percentage CPU usage goes above or below specified limits.</p>
<p>Starting with IIS 7.0, compression is automatically disabled if steady-state CPU increases above the disable threshold. Compression is enabled if CPU drops below the enable threshold.</p></td>
<td><p>50, 100, 50, and 90 respectively</p></td>
</tr>
<tr class="even">
<td><p>directory</p></td>
<td><p>Specifies the directory in which compressed versions of static files are temporarily stored and cached. Consider moving this directory off the system drive if it is accessed frequently.</p></td>
<td><p>%SystemDrive%\inetpub\temp\IIS Temporary Compressed Files</p></td>
</tr>
<tr class="odd">
<td><p>doDiskSpaceLimiting</p></td>
<td><p>Specifies whether a limit exists for how much disk space all compressed files can occupy. Compressed files are stored in the compression directory that is specified by the <strong>directory</strong> attribute.</p></td>
<td><p>True</p></td>
</tr>
<tr class="even">
<td><p>maxDiskSpaceUsage</p></td>
<td><p>Specifies the number of bytes of disk space that compressed files can occupy in the compression directory.</p>
<p>This setting might need to be increased if the total size of all compressed content is too large.</p></td>
<td><p>100 MB</p></td>
</tr>
</tbody>
</table>

 

**system.webServer/urlCompression**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>doStaticCompression</p></td>
<td><p>Specifies whether static content is compressed.</p></td>
<td><p>True</p></td>
</tr>
<tr class="even">
<td><p>doDynamicCompression</p></td>
<td><p>Specifies whether dynamic content is compressed.</p></td>
<td><p>True</p></td>
</tr>
</tbody>
</table>

 

**Note**  
For servers running IIS 8.5 that have low average CPU usage, consider enabling compression for dynamic content, especially if responses are large. This should first be done in a test environment to assess the effect on the CPU usage from the baseline.

 

### Tuning the default document list

The default document module handles HTTP requests for the root of a directory and translates them into requests for a specific file, such as Default.htm or Index.htm. On average, around 25 percent of all requests on the Internet go through the default document path. This varies significantly for individual sites. When an HTTP request does not specify a file name, the default document module searches the list of allowed default documents for each name in the file system. This can adversely affect performance, especially if reaching the content requires making a network round trip or touching a disk.

You can avoid the overhead by selectively disabling default documents and by reducing or ordering the list of documents. For websites that use a default document, you should reduce the list to only the default document types that are used. Additionally, order the list so that it begins with the most frequently accessed default document file name.

You can selectively set the default document behavior on particular URLs by customizing the configuration inside a location tag in applicationHost.config or by inserting a web.config file directly in the content directory. This allows a hybrid approach, which enables default documents only where they are necessary and sets the list to the correct file name for each URL.

To disable default documents completely, remove DefaultDocumentModule from the list of modules in the system.webServer/globalModules section in applicationHost.config.

**system.webServer/defaultDocument**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>enabled</p></td>
<td><p>Specifies that default documents are enabled.</p></td>
<td><p>True</p></td>
</tr>
<tr class="even">
<td><p>&lt;files&gt; element</p></td>
<td><p>Specifies the file names that are configured as default documents.</p></td>
<td><p>The default list is Default.htm, Default.asp, Index.htm, Index.html, Iisstart.htm, and Default.aspx.</p></td>
</tr>
</tbody>
</table>

 

### Central binary logging

Binary IIS logging reduces CPU usage, disk I/O, and disk space usage. Central binary logging is directed to a single file in binary format, regardless of the number of hosted sites. Parsing binary-format logs requires a post-processing tool.

You can enable central binary logging by setting the centralLogFileMode attribute to CentralBinary and setting the **enabled** attribute to **True**. Consider moving the location of the central log file off the system partition and onto a dedicated logging drive to avoid contention between system activities and logging activities.

**system.applicationHost/log**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>centralLogFileMode</p></td>
<td><p>Specifies the logging mode for a server. Change this value to CentralBinary to enable central binary logging.</p></td>
<td><p>Site</p></td>
</tr>
</tbody>
</table>

 

**system.applicationHost/log/centralBinaryLogFile**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>enabled</p></td>
<td><p>Specifies whether central binary logging is enabled.</p></td>
<td><p>False</p></td>
</tr>
<tr class="even">
<td><p>directory</p></td>
<td><p>Specifies the directory where log entries are written.</p></td>
<td><p>%SystemDrive%\inetpub\logs\LogFiles</p></td>
</tr>
</tbody>
</table>

 

### Application and site tunings

The following settings relate to application pool and site tunings.

**system.applicationHost/applicationPools/applicationPoolDefaults**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>queueLength</p></td>
<td><p>Indicates to HTTP.sys how many requests are queued for an application pool before future requests are rejected. When the value for this property is exceeded, IIS rejects subsequent requests with a 503 error.</p>
<p>Consider increasing this for applications that communicate with high-latency back-end data stores if 503 errors are observed.</p></td>
<td><p>1000</p></td>
</tr>
<tr class="even">
<td><p>enable32BitAppOnWin64</p></td>
<td><p>When True, enables a 32-bit application to run on a computer that has a 64-bit processor.</p>
<p>Consider enabling 32-bit mode if memory consumption is a concern. Because pointer sizes and instruction sizes are smaller, 32-bit applications use less memory than 64-bit applications. The drawback to running 32-bit applications on a 64-bit computer is that user-mode address space is limited to 4 GB.</p></td>
<td><p>False</p></td>
</tr>
</tbody>
</table>

 

**system.applicationHost/sites/VirtualDirectoryDefault**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>allowSubDirConfig</p></td>
<td><p>Specifies whether IIS looks for web.config files in content directories lower than the current level (True) or does not look for web.config files in content directories lower than the current level (False). By imposing a simple limitation, which allows configuration only in virtual directories, IIS 8.0 can know that, unless <strong>/&lt;name&gt;.htm</strong> is a virtual directory, it should not look for a configuration file. Skipping the additional file operations can significantly improve performance of websites that have a very large set of randomly accessed static content.</p></td>
<td><p>True</p></td>
</tr>
</tbody>
</table>

 

### Managing IIS 8.5 modules

IIS 8.5 has been factored into multiple, user-extensible modules to support a modular structure. This factorization has a small cost. For each module the integrated pipeline must call the module for every event that is relevant to the module. This happens regardless of whether the module must do any work. You can conserve CPU cycles and memory by removing all modules that are not relevant to a particular website.

A web server that is tuned for simple static files might include only the following five modules: UriCacheModule, HttpCacheModule, StaticFileModule, AnonymousAuthenticationModule, and HttpLoggingModule.

To remove modules from applicationHost.config, remove all references to the module from the system.webServer/handlers and system.webServer/modules sections in addition to removing the module declaration in system.webServer/globalModules.

### Classic ASP settings

The following settings apply only to classic ASP pages and do not affect ASP.NET settings. For ASP.NET performance recommendations, see [10 Tips for Writing High-Performance Web Applications](http://msdn.microsoft.com/magazine/cc163854.aspx).

**system.webServer/asp/cache**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>diskTemplateCacheDirectory</p></td>
<td><p>The name of the directory that ASP uses to store compiled templates when the in-memory cache overflows.</p>
<p>Recommendation: Set to a directory that is not heavily used, for example, a drive that is not shared with the operating system, IIS log, or other frequently accessed content.</p></td>
<td><p>%SystemDrive%\inetpub\temp\ASP Compiled Templates</p></td>
</tr>
<tr class="even">
<td><p>maxDiskTemplateCacheFiles</p></td>
<td><p>Specifies the maximum number of compiled ASP templates that can be stored.</p>
<p>Recommendation: Set to the maximum value of 0x7FFFFFFF.</p></td>
<td><p>2000</p></td>
</tr>
<tr class="odd">
<td><p>scriptFileCacheSize</p></td>
<td><p>This attribute specifies the number of precompiled script files to cache.</p>
<p>Recommendation: Set to as many ASP templates as memory limits allow.</p></td>
<td><p>500</p></td>
</tr>
<tr class="even">
<td><p>scriptEngineCacheMax</p></td>
<td><p>Specifies the maximum number of scripting engines that ASP pages will keep cached in memory.</p>
<p>Recommendation: Set to as many script engines as the memory limit allows.</p></td>
<td><p>250</p></td>
</tr>
</tbody>
</table>

 

**system.webServer/asp/limits**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>processorThreadMax</p></td>
<td><p>Specifies the maximum number of worker threads per processor that ASP can create. Increase if the current setting is insufficient to handle the load, which can cause errors when it is serving requests or cause under-usage of CPU resources.</p></td>
<td><p>25</p></td>
</tr>
</tbody>
</table>

 

**system.webServer/asp/comPlus**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>executeInMta</p></td>
<td><p>Set to <strong>True</strong> if errors or failures are detected while IIS is serving ASP content. This can occur, for example, when hosting multiple isolated sites in which each site runs under its own worker process. Errors are typically reported from COM+ in the Event Viewer. This setting enables the multi-threaded apartment model in ASP.</p></td>
<td><p>False</p></td>
</tr>
</tbody>
</table>

 

### ASP.NET concurrency setting

By default, ASP.NET limits request concurrency to reduce steady-state memory consumption on the server. High concurrency applications might need to adjust some settings to improve overall performance. These settings are stored under the following registry setting:

``` syntax
HKEY_LOCAL_MACHINE\Software\Microsoft\ASP.NET\4.0.30319.0\Parameters
```

The following setting is useful to fully use resources on a system:

-   **MaxConcurrentRequestPerCpu** Default value: 5000

    This setting limits the maximum number of concurrently executing ASP.NET requests on a system. The default value is conservative to reduce memory consumption of ASP.NET applications. Consider increasing this limit on systems that run applications that perform long, synchronous I/O operations. Otherwise, users can experience high latency because of queuing or request failures due to exceeding queue limits under a high load when the default setting is used.

### Worker process and recycling options

You can use the IIS Administrator user interface to configure options for recycling IIS worker processes and provide practical solutions to acute situations or events without requiring intervention or resetting a service or computer. Such situations and events include memory leaks, increasing memory load, or unresponsive or idle worker processes. Under ordinary conditions, recycling options might not be needed and recycling can be turned off or the system can be configured to recycle very infrequently.

You can enable process recycling for a particular application by adding attributes to the **recycling/periodicRestart** element. The recycle event can be triggered by several events including memory usage, a fixed number of requests, and a fixed time period. When a worker process is recycled, the queued and executing requests are drained, and a new process is simultaneously started to service new requests. The **recycling/periodicRestart** element is per-application, which means that each attribute in the following table is partitioned on a per-application basis.

**system.applicationHost/applicationPools/ApplicationPoolDefaults/recycling/periodicRestart**

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th>Attribute</th>
<th>Description</th>
<th>Default</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>memory</p></td>
<td><p>Enable process recycling if virtual memory consumption exceeds the specified limit in kilobytes. This is a useful setting for 32-bit computers that have a small, 2 GB address space. It can help avoid failed requests due to out-of-memory errors.</p></td>
<td><p>0</p></td>
</tr>
<tr class="even">
<td><p>privateMemory</p></td>
<td><p>Enable process recycling if private memory allocations exceed a specified limit in kilobytes.</p></td>
<td><p>0</p></td>
</tr>
<tr class="odd">
<td><p>requests</p></td>
<td><p>Enable process recycling after a certain number of requests.</p></td>
<td><p>0</p></td>
</tr>
<tr class="even">
<td><p>time</p></td>
<td><p>Enable process recycling after a specified time period.</p></td>
<td><p>29:00:00</p></td>
</tr>
</tbody>
</table>

 

### Dynamic worker-process page-out tuning

Starting in Windows Server 2012 R2, IIS now offers the option of configuring sites to suspend after they have been idle for a while (in addition to the option of terminate, which existed since IIS 7).

Let’s have a look at fine-tuning the settings for optimal performance.

The main purpose of both the WP Page-out and WP Termination features is to conserve memory utilization on the server, since a site can consume a lot of memory even if it’s just sitting there, listening. Depending on the technology used on the site (static content vs ASP.NET vs other frameworks), the memory used can be anywhere from about 10 MB to hundreds of MBs, and this means that if your server is configured with many sites, figuring out the most effective settings for your sites can dramatically improve performance of both active and suspended sites.

Before we go into specifics, we must keep in mind that if there are no memory constraints, then it’s probably best to simply set the sites to never suspend or terminate. After all, there’s little value in terminating a worker process if it’s the only one on the machine.

**Note**  
In case the site runs unstable code, such as code with a memory leak, or otherwise unstable, setting the site to terminate on idle can be a quick-and-dirty alternative to fixing the code bug. This isn’t something we would encourage, but in a crunch, it may be better to use this feature as a clean-up mechanism while a more permanent solution is in the works.\]

 

Another factor to consider is that if the site does use a lot of memory, then the suspension process itself takes a toll, because the computer has to write the data used by the Worker Process to disk. If the WP is using a large chunk of memory, then suspending it might be more expensive than the cost of having to wait for it to start back up.

To make the best of the site suspension feature, you need to review your sites, and decide which should be suspended, which should be terminated, and which should be active indefinitely. For each action and each site, you need to figure out the ideal time-out period.

Ideally, the sites that you will configure for suspension or termination are those that have visitors every day, but not enough to warrant keeping it active all the time. These are usually sites with around 20 unique visitors a day or less. You can analyze the traffic patterns using the site’s log files and calculate the average daily traffic.

Keep in mind that once a specific user connects to the site, they will typically stay on it for at least a while, making additional requests, and so just counting daily requests may not accurately reflect the real traffic patterns. To get a more accurate reading, you can also use a tool, such as Microsoft Excel, to calculate the average time between requests. For example:

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p></p></td>
<td><p>Request URL</p></td>
<td><p>Request time</p></td>
<td><p>Delta</p></td>
</tr>
<tr class="even">
<td><p>1</p></td>
<td><p>/SourceSilverLight/Geosource.web/grosource.html</p></td>
<td><p>10:01</p></td>
<td><p></p></td>
</tr>
<tr class="odd">
<td><p>2</p></td>
<td><p>/SourceSilverLight/Geosource.web/sliverlight.js</p></td>
<td><p>10:10</p></td>
<td><p>0:09</p></td>
</tr>
<tr class="even">
<td><p>3</p></td>
<td><p>/SourceSilverLight/Geosource.web/clientbin/geo/1.aspx</p></td>
<td><p>10:11</p></td>
<td><p>0:01</p></td>
</tr>
<tr class="odd">
<td><p>4</p></td>
<td><p>/lClientAccessPolicy.xml</p></td>
<td><p>10:12</p></td>
<td><p>0:01</p></td>
</tr>
<tr class="even">
<td><p>5</p></td>
<td><p>/ SourceSilverLight/GeosourcewebService/Service.asmx</p></td>
<td><p>10:23</p></td>
<td><p>0:11</p></td>
</tr>
<tr class="odd">
<td><p>6</p></td>
<td><p>/ SourceSilverLight/Geosource.web/GeoSearchServer….</p></td>
<td><p>11:50</p></td>
<td><p>1:27</p></td>
</tr>
<tr class="even">
<td><p>7</p></td>
<td><p>/rest/Services/CachedServices/Silverlight_load_la…</p></td>
<td><p>12:50</p></td>
<td><p>1:00</p></td>
</tr>
<tr class="odd">
<td><p>8</p></td>
<td><p>/rest/Services/CachedServices/Silverlight_basemap….</p></td>
<td><p>12:51</p></td>
<td><p>0:01</p></td>
</tr>
<tr class="even">
<td><p>9</p></td>
<td><p>/rest/Services/DynamicService/ Silverlight_basemap….</p></td>
<td><p>12:59</p></td>
<td><p>0:08</p></td>
</tr>
<tr class="odd">
<td><p>10</p></td>
<td><p>/rest/Services/CachedServices/Ortho_2004_cache.as...</p></td>
<td><p>13:40</p></td>
<td><p>0:41</p></td>
</tr>
<tr class="even">
<td><p>11</p></td>
<td><p>/rest/Services/CachedServices/Ortho_2005_cache.js</p></td>
<td><p>13:40</p></td>
<td><p>0:00</p></td>
</tr>
<tr class="odd">
<td><p>12</p></td>
<td><p>/rest/Services/CachedServices/OrthoBaseEngine.aspx</p></td>
<td><p>13:41</p></td>
<td><p>0:01</p></td>
</tr>
</tbody>
</table>

 

The hard part, though, is figuring out what setting to apply to make sense. In our case, the site gets a bunch of requests from users, and the table above shows that a total of 4 unique sessions occurred in a period of 4 hours. With the default settings for site suspension, the site would be terminated after the default timeout of 20 minutes, which means each of these users would experience the site spin-up cycle. This makes it an ideal candidate for site suspension, because for most of the time, the site is idle, and so suspending it would conserve resources, and allow the users to reach the site almost instantly.

A final, and very important note about this is that disk performance is crucial for this feature. Because the suspension and wake-up process involve writing and reading large amount of data to the hard drive, we strongly recommend using a fast disk for this. Solid State Drives (SSDs) are ideal and highly recommended for this, and you should make sure that the Windows page file is stored on it (if the operating system itself is not installed on the SSD, configure the operating system to move the page file to it).

Whether you use an SSD or not, we also recommend fixing the size of the page file to accommodate writing the page-out data to it without file-resizing. Page-file resizing might happen when the operating system needs to store data in the page file, because by default, Windows is configured to automatically adjust its size based on need. By setting the size to a fixed one, you can prevent resizing and improve performance a lot.

To configure a pre-fixed page file size, you need to calculate its ideal size, which depends on how many sites you will be suspending, and how much memory they consume. If the average is 200 MB for an active worker process (W3WP) and you have 500 sites on the servers that will be suspending, then the page file should be at least (200 \* 500) MB over the base size of the page file (so base + 100 GB in our example).

**Note**  
When sites are suspended, they will consume approximately 6 MB each, so in our case, memory usage if all sites are suspended would be around 3 GB. In reality, though, you’re probably never going to have them all suspended at the same time.

 

### Secure Sockets Layer tuning parameters

The use of SSL imposes additional CPU cost. The most expensive component of SSL is the cost of establishing a session establishment because it involves a full handshake. Reconnection, encryption, and decryption also add to the cost. For better SSL performance, do the following:

-   Enable HTTP keep-alives for SSL sessions. This eliminates the session establishment costs.

-   Reuse sessions when appropriate, especially with non-keep-alive traffic.

-   Selectively apply encryption only to pages or parts of the site that need it, rather to the entire site.

**Note**  
-   Larger keys provide more security, but they also use more CPU time.

-   All components might not need to be encrypted. However, mixing plain HTTP and HTTPS might result in a pop-up warning that not all content on the page is secure.

 

### Internet Server Application Programming Interface (ISAPI)

No special tuning parameters are needed for ISAPI applications. If you write a private ISAPI extension, make sure that it is written for performance and resource use. For more info, see [Performance Tuning for Server Roles](performance-tuning-for-server-roles.md#bkmk-iisother).

### Managed code tuning guidelines

The integrated pipeline model in IIS 8.5 enables a high degree of flexibility and extensibility. Custom modules that are implemented in native or managed code can be inserted into the pipeline, or they can replace existing modules. Although this extensibility model offers convenience and simplicity, you should be careful before you insert new managed modules that hook into global events. Adding a global managed module means that all requests, including static file requests, must touch managed code. Custom modules are susceptible to events such as garbage collection. In addition, custom modules add significant CPU cost due to marshaling data between native and managed code. If possible, you should implement global modules in native code.

Before you deploy an ASP.NET website, make sure that you compile all scripts. You can do this by calling one .NET script in each directory. After the compilation is complete, you should restart IIS. You should also recompile the scripts after you make changes to machine.config, web.config, or any .aspx scripts.

If session state is not needed, make sure that you turn it off for each page.

When you run multiple hosts that contain ASP.NET scripts in isolated mode (one application pool per site), monitor the memory usage. Make sure that the server has enough RAM for the expected number of concurrently running application pools. Consider using multiple application domains instead of multiple isolated processes.

For ASP.NET performance recommendations, see [10 Tips for Writing High-Performance Web Applications](http://msdn.microsoft.com/magazine/cc163854.aspx).

### <a href="" id="bkmk-iisother"></a>Other issues that affect IIS performance

The following issues can affect IIS performance:

-   Installation of filters that are not cache-aware

    The installation of a filter that is not HTTP-cache-aware causes IIS to completely disable caching, which results in poor performance. ISAPI filters that were written before IIS 6.0 can cause this behavior.

-   Common Gateway Interface (CGI) requests

    For performance reasons, the use of CGI applications to serve requests is not recommended with IIS. Frequently creating and deleting CGI processes involves significant overhead. Better alternatives include using ISAPI application scripts and ASP or ASP.NET scripts. Isolation is available for each of these options.

## <a href="" id="filesystem"></a>NTFS file system setting


The system-global switch **NtfsDisableLastAccessUpdate** (REG\_DWORD) 1 is located under **HKLM\\System\\CurrentControlSet\\Control\\FileSystem** and is set by default to 1. This switch reduces disk I/O load and latencies by disabling date and time stamp updating for the last file or directory access. Clean installations of Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, and Windows Server 2008 enable this setting by default, and you do not need to adjust it. Earlier versions of Windows did not set this key. If your server is running an earlier version of Windows, or it was upgraded to Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, or Windows Server 2008, you should enable this setting.

Disabling the updates is effective when you are using large data sets (or many hosts) that contain thousands of directories. We recommend that you use IIS logging instead if you maintain this information only for Web administration.

**Caution**  
Some applications, such as incremental backup utilities, rely on this update information, and they do not function correctly without it.

 

## <a href="" id="netsub"></a>Networking subsystem performance settings for IIS


For networking subsystem performance settings, see [Performance Tuning for Network Subsystems](performance-tuning-for-network-subsystems.md).

## Related topics


[Performance Tuning for Server Roles](performance-tuning-for-server-roles.md)