---
title: Azure StandbyPoolManagement client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure/arm-standbypool, standbypool
ms.date: 04/25/2025
ms.topic: reference
ms.devlang: javascript
ms.service: standbypool
---
# Azure StandbyPoolManagement client library for JavaScript - version 2.0.0 


This package contains an isomorphic SDK (runs both in Node.js and in browsers) for Azure StandbyPoolManagement client.



Key links:

- [Source code](https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-standbypool_2.0.0/sdk/standbypool/arm-standbypool)
- [Package (NPM)](https://www.npmjs.com/package/@azure/arm-standbypool)
- [API reference documentation](https://learn.microsoft.com/javascript/api/@azure/arm-standbypool?view=azure-node-preview)
- [Samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-standbypool_2.0.0/sdk/standbypool/arm-standbypool/samples)

## Getting started

### Currently supported environments

- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)
- Latest versions of Safari, Chrome, Edge and Firefox.

See our [support policy](https://github.com/Azure/azure-sdk-for-js/blob/@azure/arm-standbypool_2.0.0/SUPPORT.md) for more details.

### Prerequisites

- An [Azure subscription][azure_sub].

### Install the `@azure/arm-standbypool` package

Install the Azure StandbyPoolManagement client library for JavaScript with `npm`:

```bash
npm install @azure/arm-standbypool
```

### Create and authenticate a `StandbyPoolManagementClient`

To create a client object to access the Azure StandbyPoolManagement API, you will need the `endpoint` of your Azure StandbyPoolManagement resource and a `credential`. The Azure StandbyPoolManagement client can use Azure Active Directory credentials to authenticate.
You can find the endpoint for your Azure StandbyPoolManagement resource in the [Azure Portal][azure_portal].

You can authenticate with Azure Active Directory using a credential from the [@azure/identity][azure_identity] library or [an existing AAD Token](https://github.com/Azure/azure-sdk-for-js/blob/@azure/arm-standbypool_2.0.0/sdk/identity/identity/samples/AzureIdentityExamples.md#authenticating-with-a-pre-fetched-access-token).

To use the [DefaultAzureCredential][defaultazurecredential] provider shown below, or other credential providers provided with the Azure SDK, please install the `@azure/identity` package:

```bash
npm install @azure/identity
```

You will also need to **register a new AAD application and grant access to Azure StandbyPool** by assigning the suitable role to your service principal (note: roles such as `"Owner"` will not grant the necessary permissions).

For more information about how to create an Azure AD Application check out [this guide](https://learn.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Using Node.js and Node-like environments, you can use the `DefaultAzureCredential` class to authenticate the client.

```ts snippet:ReadmeSampleCreateClient_Node
import { StandbyPoolManagementClient } from "@azure/arm-standbypool";
import { DefaultAzureCredential } from "@azure/identity";

const subscriptionId = "00000000-0000-0000-0000-000000000000";
const client = new StandbyPoolManagementClient(new DefaultAzureCredential(), subscriptionId);
```

For browser environments, use the `InteractiveBrowserCredential` from the `@azure/identity` package to authenticate.

```ts snippet:ReadmeSampleCreateClient_Browser
import { InteractiveBrowserCredential } from "@azure/identity";
import { StandbyPoolManagementClient } from "@azure/arm-standbypool";

const subscriptionId = "00000000-0000-0000-0000-000000000000";
const credential = new InteractiveBrowserCredential({
  tenantId: "<YOUR_TENANT_ID>",
  clientId: "<YOUR_CLIENT_ID>"
 });
const client = new StandbyPoolManagementClient(credential, subscriptionId);
```


### JavaScript Bundle
To use this client library in the browser, first you need to use a bundler. For details on how to do this, please refer to our [bundling documentation](https://aka.ms/AzureSDKBundling).

## Key concepts

### StandbyPoolManagementClient

`StandbyPoolManagementClient` is the primary interface for developers using the Azure StandbyPoolManagement client library. Explore the methods on this client object to understand the different features of the Azure StandbyPoolManagement service that you can access.

## Troubleshooting

### Logging

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```ts snippet:SetLogLevel
import { setLogLevel } from "@azure/logger";

setLogLevel("info");
```

For more detailed instructions on how to enable logs, you can look at the [@azure/logger package docs](https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-standbypool_2.0.0/sdk/core/logger).

## Next steps

Please take a look at the [samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-standbypool_2.0.0/sdk/standbypool/arm-standbypool/samples) directory for detailed examples on how to use this library.

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/arm-standbypool_2.0.0/CONTRIBUTING.md) to learn more about how to build and test the code.

## Related projects

- [Microsoft Azure SDK for JavaScript](https://github.com/Azure/azure-sdk-for-js)

[azure_sub]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[azure_identity]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-standbypool_2.0.0/sdk/identity/identity
[defaultazurecredential]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-standbypool_2.0.0/sdk/identity/identity#defaultazurecredential

