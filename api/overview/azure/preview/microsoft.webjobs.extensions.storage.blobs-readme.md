---
title: Azure WebJobs Storage Blobs client library for .NET
keywords: Azure, .net, SDK, API, Microsoft.Azure.WebJobs.Extensions.Storage.Blobs,
ms.date: 11/11/2020
ms.topic: reference
ms.devlang: .net
ms.service: 
---
# Azure WebJobs Storage Blobs client library for .NET - version 5.0.0-beta.1 


This extension provides functionality for accessing Azure Storage Blobs in Azure Functions.

## Getting started

### Install the package

Install the Storage Blobs extension with [NuGet][nuget]:

```Powershell
dotnet add package Azure.WebJobs.Extensions.Storage.Blobs
```

### Prerequisites

You need an [Azure subscription][azure_sub] and a
[Storage Account][storage_account_docs] to use this package.

To create a new Storage Account, you can use the [Azure Portal][storage_account_create_portal],
[Azure PowerShell][storage_account_create_ps], or the [Azure CLI][storage_account_create_cli].
Here's an example using the Azure CLI:

```Powershell
az storage account create --name <your-resource-name> --resource-group <your-resource-group-name> --location westus --sku Standard_LRS
```

### Authenticate the client

In order for the extension to access Blobs, you will need the connection string which can be found in the [Azure Portal](https://portal.azure.com/) or by using the [Azure CLI](https://docs.microsoft.com/cli/azure) snippet below.

```bash
az storage account show-connection-string -g <your-resource-group-name> -n <your-resource-name>
```

The connection string can be supplied through [AzureWebJobsStorage app setting](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

## Key concepts

### Using Blob binding

Please follow the [input binding tutorial](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-input?tabs=csharp) and [output binding tutorial](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-output?tabs=csharp) to learn about using this extension for accessing Blobs.

### Using Blob trigger

Please follow the [tutorial](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger?tabs=csharp) to learn about triggering an Azure Function when a blob is modified.

## Examples

### Binding to string

```C# Snippet:BlobFunction_String
public static class BlobFunction_String
{
    [FunctionName("BlobFunction")]
    public static void Run(
        [BlobTrigger("sample-container/sample-blob-1")] string blobTriggerContent,
        [Blob("sample-container/sample-blob-2")] string blobContent,
        ILogger logger)
    {
        logger.LogInformation("Blob sample-container/sample-blob-1 has been updated with content: {content}", blobTriggerContent);
        logger.LogInformation("Blob sample-container/sample-blob-2 has content: {content}", blobContent);
    }
}
```

### Reading from stream

```C# Snippet:BlobFunction_ReadStream
public static class BlobFunction_ReadStream
{
    [FunctionName("BlobFunction")]
    public static void Run(
        [BlobTrigger("sample-container/sample-blob-1")] Stream blobTriggerStream,
        [Blob("sample-container/sample-blob-2", FileAccess.Read)] Stream blobStream,
        ILogger logger)
    {
        using var blobTriggerStreamReader = new StreamReader(blobTriggerStream);
        logger.LogInformation("Blob sample-container/sample-blob-1 has been updated with content: {content}", blobTriggerStreamReader.ReadToEnd());
        using var blobStreamReader = new StreamReader(blobStream);
        logger.LogInformation("Blob sample-container/sample-blob-2 has content: {content}", blobStreamReader.ReadToEnd());
    }
}
```

### Writing to stream

```C# Snippet:BlobFunction_WriteStream
public static class BlobFunction_WriteStream
{
    [FunctionName("BlobFunction")]
    public static async Task Run(
        [BlobTrigger("sample-container/sample-blob-1")] Stream blobTriggerStream,
        [Blob("sample-container/sample-blob-2", FileAccess.Write)] Stream blobStream,
        ILogger logger)
    {
        await blobTriggerStream.CopyToAsync(blobStream);
        logger.LogInformation("Blob sample-container/sample-blob-1 has been copied to sample-container/sample-blob-2");
    }
}
```

### Binding to Azure Storage Blob SDK types

```C# Snippet:BlobFunction_BlobClient
public static class BlobFunction_BlobClient
{
    [FunctionName("BlobFunction")]
    public static async Task Run(
        [BlobTrigger("sample-container/sample-blob-1")] BlobClient blobTriggerClient,
        [Blob("sample-container/sample-blob-2")] BlobClient blobClient,
        ILogger logger)
    {
        BlobProperties blobTriggerProperties = await blobTriggerClient.GetPropertiesAsync();
        logger.LogInformation("Blob sample-container/sample-blob-1 has been updated on: {datetime}", blobTriggerProperties.LastModified);
        BlobProperties blobProperties = await blobClient.GetPropertiesAsync();
        logger.LogInformation("Blob sample-container/sample-blob-2 has been updated on: {datetime}", blobProperties.LastModified);
    }
}
```

## Troubleshooting

Please refer to [Monitor Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) for troubleshooting guidance.

## Next steps

Read the [introduction to Azure Function](https://docs.microsoft.com/azure/azure-functions/functions-overview) or [creating an Azure Function guide](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function).

## Contributing

See the [Storage CONTRIBUTING.md][storage_contrib] for details on building,
testing, and contributing to this library.

This project welcomes contributions and suggestions.  Most contributions require
you to agree to a Contributor License Agreement (CLA) declaring that you have
the right to, and actually do, grant us the rights to use your contribution. For
details, visit [cla.microsoft.com][cla].

This project has adopted the [Microsoft Open Source Code of Conduct][coc].
For more information see the [Code of Conduct FAQ][coc_faq]
or contact [opencode@microsoft.com][coc_contact] with any
additional questions or comments.



<!-- LINKS -->
[nuget]: https://www.nuget.org/
[storage_account_docs]: https://docs.microsoft.com/azure/storage/common/storage-account-overview
[storage_account_create_ps]: https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell
[storage_account_create_cli]: https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli
[storage_account_create_portal]: https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal
[azure_sub]: https://azure.microsoft.com/free/
[RequestFailedException]: https://github.com/Azure/azure-sdk-for-net/tree/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs_5.0.0-beta.1/sdk/core/Azure.Core/src/RequestFailedException.cs
[storage_contrib]: https://github.com/Azure/azure-sdk-for-net/blob/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs_5.0.0-beta.1/sdk/storage/CONTRIBUTING.md
[cla]: https://cla.microsoft.com
[coc]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com

