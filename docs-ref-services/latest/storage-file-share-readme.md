---
title: Azure Storage File Share client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure/storage-file-share, storage
ms.date: 07/24/2025
ms.topic: reference
ms.devlang: javascript
ms.service: storage
---
# Azure Storage File Share client library for JavaScript - version 12.28.0 


Azure Files offers fully managed file shares in the cloud that are accessible via the industry standard Server Message Block (SMB) protocol. Azure file shares can be mounted concurrently by cloud or on-premises deployments of Windows, Linux, and macOS. Additionally, Azure file shares can be cached on Windows Servers with Azure File Sync for fast access near where the data is being used.

This project provides a client library in JavaScript that makes it easy to consume Microsoft Azure File Storage service.

Use the client libraries in this package to:

- Get/Set File Service Properties
- Create/List/Delete File Shares
- Create/List/Delete File Directories
- Create/Read/List/Update/Delete Files

> Note: This package was previously published under the name `@azure/storage-file`.
> It has been renamed to `@azure/storage-file-share` to better align with the upcoming new package
> for Azure Storage Files DataLake and provide a consistent set of APIs for working with files on Azure.

Key links:

- [Source code](https://github.com/Azure/azure-sdk-for-js/tree/@azure/storage-file-share_12.28.0/sdk/storage/storage-file-share)
- [Package (npm)](https://www.npmjs.com/package/@azure/storage-file-share/)
- [API Reference Documentation](https://learn.microsoft.com/javascript/api/@azure/storage-file-share)
- [Product documentation](https://learn.microsoft.com/azure/storage/files/storage-files-introduction)
- [Samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure/storage-file-share_12.28.0/sdk/storage/storage-file-share/samples)
- [Azure Storage File REST APIs](https://learn.microsoft.com/rest/api/storageservices/file-service-rest-api)

## Getting started

### Currently supported environments

- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)
- Latest versions of Safari, Chrome, Edge, and Firefox.

See our [support policy](https://github.com/Azure/azure-sdk-for-js/blob/@azure/storage-file-share_12.28.0/SUPPORT.md) for more details.

### Prerequisites

- An [Azure subscription](https://azure.microsoft.com/free/)
- A [Storage Account](https://learn.microsoft.com/azure/storage/common/storage-account-create)

### Install the package

The preferred way to install the Azure File Storage client library for JavaScript is to use the npm package manager. Type the following into a terminal window:

```bash
npm install @azure/storage-file-share
```

### Authenticate the client

Azure Storage supports several ways to authenticate. In order to interact with the Azure Storage File Share service you'll need to create an instance of a Storage client - `ShareServiceClient`, `ShareClient`, or `ShareDirectoryClient` for example. See [samples for creating the `ShareServiceClient`](#create-the-share-service-client) to learn more about authentication.

- [Shared Key](#with-storagesharedkeycredential)
- [Shared access signatures](#with-sas-token)

### Compatibility

This library is compatible with Node.js and browsers, and validated against LTS Node.js versions (>=8.16.0) and latest versions of Chrome, Firefox and Edge.

#### Web Workers

This library requires certain DOM objects to be globally available when used in the browser, which web workers do not make available by default. You will need to polyfill these to make this library work in web workers.

For more information please refer to our [documentation for using Azure SDK for JS in Web Workers](https://aka.ms/azsdk/js/web-workers)

This library depends on following DOM APIs which need external polyfills loaded when used in web workers:

- [`document`](https://developer.mozilla.org/docs/Web/API/Document)
- [`DOMParser`](https://developer.mozilla.org/docs/Web/API/DOMParser)
- [`Node`](https://developer.mozilla.org/docs/Web/API/Node)
- [`XMLSerializer`](https://developer.mozilla.org/docs/Web/API/XMLSerializer)

#### Differences between Node.js and browsers

There are differences between Node.js and browsers runtime. When getting started with this library, pay attention to APIs or classes marked with _"ONLY AVAILABLE IN NODE.JS RUNTIME"_ or _"ONLY AVAILABLE IN BROWSERS"_.

- If a file holds compressed data in `gzip` or `deflate` format and its content encoding is set accordingly, downloading behavior is different between Node.js and browsers. In Node.js storage clients will download the file in its compressed format, while in browsers the data will be downloaded in de-compressed format.

##### Following features, interfaces, classes or functions are only available in Node.js

- Shared Key Authorization based on account name and account key
  - `StorageSharedKeyCredential`
- Shared Access Signature(SAS) generation
  - `generateAccountSASQueryParameters()`
  - `generateFileSASQueryParameters()`
- Parallel uploading and downloading. Note that `ShareFileClient.uploadData()` is available in both Node.js and browsers.
  - `ShareFileClient.uploadFile()`
  - `ShareFileClient.uploadStream()`
  - `ShareFileClient.downloadToBuffer()`
  - `ShareFileClient.downloadToFile()`

##### Following features, interfaces, classes or functions are only available in browsers

N/A

### JavaScript Bundle

To use this client library in the browser, first you need to use a bundler. For details on how to do this, please refer to our [bundling documentation](https://aka.ms/AzureSDKBundling).

### CORS

You need to set up [Cross-Origin Resource Sharing (CORS)](https://learn.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) rules for your storage account if you need to develop for browsers. Go to Azure portal and Azure Storage Explorer, find your storage account, create new CORS rules for blob/queue/file/table service(s).

For example, you can create following CORS settings for debugging. But please customize the settings carefully according to your requirements in production environment.

- Allowed origins: \*
- Allowed verbs: DELETE,GET,HEAD,MERGE,POST,OPTIONS,PUT
- Allowed headers: \*
- Exposed headers: \*
- Maximum age (seconds): 86400

## Key concepts

The following components and their corresponding client libraries make up the Azure Storage File Share service:

- The _storage account_ itself, represented by a `ShareServiceClient`
- A _file share_ within the storage account, represented by a `ShareClient`
- An optional _hierarchy of directories_ within the file share, represented by `ShareDirectoryClient` instances
- A _file_ within the file share, which may be up to 1 TiB in size, represented by a `ShareFileClient`

## Examples

- [Import the package](#import-the-package)
- [Create the share service client](#create-the-share-service-client)
- [List shares in the account](#list-shares-in-the-account)
- [Create a new share and a directory](#create-a-new-share-and-a-directory)
- [Create an azure file then upload to it](#create-an-azure-file-then-upload-to-it)
- [List files and directories under a directory](#list-files-and-directories-under-a-directory)
- [Download a file and convert it to a string (Node.js)](#download-a-file-and-convert-it-to-a-string-nodejs)
- [Download a file and convert it to a string (Browsers)](#download-a-file-and-convert-it-to-a-string-browsers)

### Import the package

To use the clients, import the package into your file:

```ts snippet:ignore
import * as AzureStorageFileShare from "@azure/storage-file-share";
```

Alternative, selectively import only the types you need:

```ts snippet:ignore
import { ShareServiceClient, StorageSharedKeyCredential } from "@azure/storage-file-share";
```

### Create the share service client

The `ShareServiceClient` requires an URL to the file share service and an access credential. It also optionally accepts some settings in the `options` parameter.

#### using connection string

Alternatively, you can instantiate a `ShareServiceClient` using the `fromConnectionString()` static method with the full connection string as the argument. (The connection string can be obtained from the azure portal.)

```ts snippet:ReadmeSampleCreateClient_ConnectionString
import { ShareServiceClient } from "@azure/storage-file-share";

const connectionString = "<connection string>";

const shareServiceClient = ShareServiceClient.fromConnectionString(connectionString);
```

#### with `StorageSharedKeyCredential`

Pass in a `StorageSharedKeyCredential` with your account name and account key. (The account-name and account-key can be obtained from the azure portal.)

```ts snippet:ReadmeSampleCreateClient_StorageSharedKeyCredential
import { StorageSharedKeyCredential, ShareServiceClient } from "@azure/storage-file-share";

// Enter your storage account name and shared key
const account = "<account>";
const accountKey = "<accountkey>";

// Use StorageSharedKeyCredential with storage account and account key
// StorageSharedKeyCredential is only available in Node.js runtime, not in browsers
const credential = new StorageSharedKeyCredential(account, accountKey);
const serviceClient = new ShareServiceClient(
  // When using AnonymousCredential, following url should include a valid SAS
  `https://${account}.file.core.windows.net`,
  credential,
);
```

#### with SAS Token

Also, You can instantiate a `ShareServiceClient` with a shared access signatures (SAS). You can get the SAS token from the Azure Portal or generate one using `generateAccountSASQueryParameters()`.

```ts snippet:ReadmeSampleCreateClient_SASToken
import { ShareServiceClient } from "@azure/storage-file-share";

const account = "<account name>";
const sas = "<service Shared Access Signature Token>";

const serviceClientWithSAS = new ShareServiceClient(
  `https://${account}.file.core.windows.net?${sas}`,
);
```

### List shares in the account

Use `ShareServiceClient.listShares()` to iterator shares in this account,
with the new `for-await-of` syntax:

```ts snippet:ReadmeSampleListShares
import { StorageSharedKeyCredential, ShareServiceClient } from "@azure/storage-file-share";

const account = "<account>";
const accountKey = "<accountkey>";

const credential = new StorageSharedKeyCredential(account, accountKey);
const serviceClient = new ShareServiceClient(
  `https://${account}.file.core.windows.net`,
  credential,
);

let i = 1;
for await (const share of serviceClient.listShares()) {
  console.log(`Share${i++}: ${share.name}`);
}
```

Alternatively without `for-await-of`:

```ts snippet:ReadmeSampleListShares_Iterator
import { StorageSharedKeyCredential, ShareServiceClient } from "@azure/storage-file-share";

const account = "<account>";
const accountKey = "<accountkey>";

const credential = new StorageSharedKeyCredential(account, accountKey);
const serviceClient = new ShareServiceClient(
  `https://${account}.file.core.windows.net`,
  credential,
);

const shareIter = serviceClient.listShares();
let i = 1;
let { value, done } = await shareIter.next();
while (!done) {
  console.log(`Share ${i++}: ${value.name}`);
  ({ value, done } = await shareIter.next());
}
```

### Create a new share and a directory

```ts snippet:ReadmeSampleCreateShareAndDirectory
import { StorageSharedKeyCredential, ShareServiceClient } from "@azure/storage-file-share";

const account = "<account>";
const accountKey = "<accountkey>";

const credential = new StorageSharedKeyCredential(account, accountKey);
const serviceClient = new ShareServiceClient(
  `https://${account}.file.core.windows.net`,
  credential,
);

const shareName = `newshare${+new Date()}`;
const shareClient = serviceClient.getShareClient(shareName);
await shareClient.create();
console.log(`Create share ${shareName} successfully`);

const directoryName = `newdirectory${+new Date()}`;
const directoryClient = shareClient.getDirectoryClient(directoryName);
await directoryClient.create();
console.log(`Create directory ${directoryName} successfully`);
```

### Create an azure file then upload to it

```ts snippet:ReadmeSampleCreateFileAndUpload
import { StorageSharedKeyCredential, ShareServiceClient } from "@azure/storage-file-share";

const account = "<account>";
const accountKey = "<accountkey>";

const credential = new StorageSharedKeyCredential(account, accountKey);
const serviceClient = new ShareServiceClient(
  `https://${account}.file.core.windows.net`,
  credential,
);

const shareName = "<share name>";
const directoryName = "<directory name>";
const directoryClient = serviceClient.getShareClient(shareName).getDirectoryClient(directoryName);

const content = "Hello World!";
const fileName = `newdirectory${+new Date()}`;
const fileClient = directoryClient.getFileClient(fileName);
await fileClient.create(content.length);
console.log(`Create file ${fileName} successfully`);

// Upload file range
await fileClient.uploadRange(content, 0, content.length);
console.log(`Upload file range "${content}" to ${fileName} successfully`);
```

### List files and directories under a directory

Use `DirectoryClient.listFilesAndDirectories()` to iterator over files and directories,
with the new `for-await-of` syntax. The `kind` property can be used to identify whether
a iterm is a directory or a file.

```ts snippet:ReadmeSampleListFilesAndDirectories
import { StorageSharedKeyCredential, ShareServiceClient } from "@azure/storage-file-share";

const account = "<account>";
const accountKey = "<accountkey>";

const credential = new StorageSharedKeyCredential(account, accountKey);
const serviceClient = new ShareServiceClient(
  `https://${account}.file.core.windows.net`,
  credential,
);

const shareName = "<share name>";
const directoryName = "<directory name>";
const directoryClient = serviceClient.getShareClient(shareName).getDirectoryClient(directoryName);

let i = 1;
for await (const item of directoryClient.listFilesAndDirectories()) {
  if (item.kind === "directory") {
    console.log(`${i} - directory\t: ${item.name}`);
  } else {
    console.log(`${i} - file\t: ${item.name}`);
  }
  i++;
}
```

Alternatively without using `for-await-of`:

```ts snippet:ReadmeSampleListFilesAndDirectories_Iterator
import { StorageSharedKeyCredential, ShareServiceClient } from "@azure/storage-file-share";

const account = "<account>";
const accountKey = "<accountkey>";

const credential = new StorageSharedKeyCredential(account, accountKey);
const serviceClient = new ShareServiceClient(
  `https://${account}.file.core.windows.net`,
  credential,
);

const shareName = "<share name>";
const directoryName = "<directory name>";
const directoryClient = serviceClient.getShareClient(shareName).getDirectoryClient(directoryName);

let i = 1;
const iter = directoryClient.listFilesAndDirectories();
let { value, done } = await iter.next();
while (!done) {
  if (value.kind === "directory") {
    console.log(`${i} - directory\t: ${value.name}`);
  } else {
    console.log(`${i} - file\t: ${value.name}`);
  }
  ({ value, done } = await iter.next());
  i++;
}
```

For a complete sample on iterating please see [samples/v12/typescript/src/listFilesAndDirectories.ts](https://github.com/Azure/azure-sdk-for-js/blob/@azure/storage-file-share_12.28.0/sdk/storage/storage-file-share/samples/v12/typescript/src/listFilesAndDirectories.ts).

### Download a file and convert it to a string (Node.js)

```ts snippet:ReadmeSampleDownloadFileAndConvertToString_Node
import { StorageSharedKeyCredential, ShareServiceClient } from "@azure/storage-file-share";

const account = "<account>";
const accountKey = "<accountkey>";

const credential = new StorageSharedKeyCredential(account, accountKey);
const serviceClient = new ShareServiceClient(
  `https://${account}.file.core.windows.net`,
  credential,
);

const shareName = "<share name>";
const fileName = "<file name>";
const fileClient = serviceClient
  .getShareClient(shareName)
  .rootDirectoryClient.getFileClient(fileName);

// Get file content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadFileResponse.readableStreamBody
const downloadFileResponse = await fileClient.download();
if (downloadFileResponse.readableStreamBody) {
  const buffer = await streamToBuffer(downloadFileResponse.readableStreamBody);
  console.log(`Downloaded file content: ${buffer.toString()}`);
}

// [Node.js only] A helper method used to read a Node.js readable stream into a Buffer
async function streamToBuffer(readableStream: NodeJS.ReadableStream): Promise<Buffer> {
  return new Promise((resolve, reject) => {
    const chunks: Buffer[] = [];
    readableStream.on("data", (data) => {
      chunks.push(data instanceof Buffer ? data : Buffer.from(data));
    });
    readableStream.on("end", () => {
      resolve(Buffer.concat(chunks));
    });
    readableStream.on("error", reject);
  });
}
```

### Download a file and convert it to a string (Browsers)

Please refer to the [JavaScript Bundle](#javascript-bundle) section for more information on using this library in the browser.

```ts snippet:ReadmeSampleDownloadFileAndConvertToString_Browser
import { ShareServiceClient } from "@azure/storage-file-share";

const account = "<account name>";
const sas = "<service Shared Access Signature Token>";

const serviceClient = new ShareServiceClient(`https://${account}.file.core.windows.net?${sas}`);

const shareName = "<share name>";
const fileName = "<file name>";
const fileClient = serviceClient
  .getShareClient(shareName)
  .rootDirectoryClient.getFileClient(fileName);

// Get file content from position 0 to the end
// In browsers, get downloaded data by accessing downloadFileResponse.blobBody
const downloadFileResponse = await fileClient.download(0);
if (downloadFileResponse.blobBody) {
  console.log(`Downloaded file content: ${(await downloadFileResponse.blobBody).text()}`);
}
```

A complete example of simple `ShareServiceClient` scenarios is at [samples/v12/typescript/src/shareSerivceClient.ts](https://github.com/Azure/azure-sdk-for-js/blob/@azure/storage-file-share_12.28.0/sdk/storage/storage-file-share/samples/v12/typescript/src/shareServiceClient.ts).

## Troubleshooting

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```ts snippet:SetLogLevel
import { setLogLevel } from "@azure/logger";

setLogLevel("info");
```

## Next steps

More code samples

- [File Share Storage Samples (JavaScript)](https://github.com/Azure/azure-sdk-for-js/tree/@azure/storage-file-share_12.28.0/sdk/storage/storage-file-share/samples/v12/javascript)
- [File Share Storage Samples (TypeScript)](https://github.com/Azure/azure-sdk-for-js/tree/@azure/storage-file-share_12.28.0/sdk/storage/storage-file-share/samples/v12/typescript)
- [File Share Storage Test Cases](https://github.com/Azure/azure-sdk-for-js/tree/@azure/storage-file-share_12.28.0/sdk/storage/storage-file-share/test)

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/storage-file-share_12.28.0/CONTRIBUTING.md) to learn more about how to build and test the code.

Also refer to [Storage specific guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/storage-file-share_12.28.0/sdk/storage/CONTRIBUTING.md) for additional information on setting up the test environment for storage libraries.

