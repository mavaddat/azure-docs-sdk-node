---
title: Azure OnlineExperimentation client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure/arm-onlineexperimentation, onlineexperimentation
ms.date: 06/11/2025
ms.topic: reference
ms.devlang: javascript
ms.service: onlineexperimentation
---
# Azure OnlineExperimentation client library for JavaScript - version 1.0.0-beta.1 


This package contains an isomorphic SDK (runs both in Node.js and in browsers) for Azure OnlineExperimentation client.

Microsoft.OnlineExperimentation Resource Provider management API.

Key links:

- [Source code](https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-onlineexperimentation_1.0.0-beta.1/sdk/onlineexperimentation/arm-onlineexperimentation)
- [Package (NPM)](https://www.npmjs.com/package/@azure/arm-onlineexperimentation)
- [API reference documentation](https://learn.microsoft.com/javascript/api/@azure/arm-onlineexperimentation?view=azure-node-preview)
- [Samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-onlineexperimentation_1.0.0-beta.1/sdk/onlineexperimentation/arm-onlineexperimentation/samples)

## Getting started

### Currently supported environments

- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)
- Latest versions of Safari, Chrome, Edge and Firefox.

See our [support policy](https://github.com/Azure/azure-sdk-for-js/blob/@azure/arm-onlineexperimentation_1.0.0-beta.1/SUPPORT.md) for more details.

### Prerequisites

- An [Azure subscription][azure_sub].

### Install the `@azure/arm-onlineexperimentation` package

Install the Azure OnlineExperimentation client library for JavaScript with `npm`:

```bash
npm install @azure/arm-onlineexperimentation
```

### Create and authenticate a `OnlineExperimentationClient`

To create a client object to access the Azure OnlineExperimentation API, you will need the `endpoint` of your Azure OnlineExperimentation resource and a `credential`. The Azure OnlineExperimentation client can use Azure Active Directory credentials to authenticate.
You can find the endpoint for your Azure OnlineExperimentation resource in the [Azure Portal][azure_portal].

You can authenticate with Azure Active Directory using a credential from the [@azure/identity][azure_identity] library or [an existing AAD Token](https://github.com/Azure/azure-sdk-for-js/blob/@azure/arm-onlineexperimentation_1.0.0-beta.1/sdk/identity/identity/samples/AzureIdentityExamples.md#authenticating-with-a-pre-fetched-access-token).

To use the [DefaultAzureCredential][defaultazurecredential] provider shown below, or other credential providers provided with the Azure SDK, please install the `@azure/identity` package:

```bash
npm install @azure/identity
```

You will also need to **register a new AAD application and grant access to Azure OnlineExperimentation** by assigning the suitable role to your service principal (note: roles such as `"Owner"` will not grant the necessary permissions).

For more information about how to create an Azure AD Application check out [this guide](https://learn.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Using Node.js and Node-like environments, you can use the `DefaultAzureCredential` class to authenticate the client.

```ts snippet:ReadmeSampleCreateClient_Node
import { OnlineExperimentationClient } from "@azure/arm-onlineexperimentation";
import { DefaultAzureCredential } from "@azure/identity";

const subscriptionId = "00000000-0000-0000-0000-000000000000";
const client = new OnlineExperimentationClient(new DefaultAzureCredential(), subscriptionId);
```

For browser environments, use the `InteractiveBrowserCredential` from the `@azure/identity` package to authenticate.

```ts snippet:ReadmeSampleCreateClient_Browser
import { InteractiveBrowserCredential } from "@azure/identity";
import { OnlineExperimentationClient } from "@azure/arm-onlineexperimentation";

const subscriptionId = "00000000-0000-0000-0000-000000000000";
const credential = new InteractiveBrowserCredential({
  tenantId: "<YOUR_TENANT_ID>",
  clientId: "<YOUR_CLIENT_ID>",
});
const client = new OnlineExperimentationClient(credential, subscriptionId);
```


### JavaScript Bundle
To use this client library in the browser, first you need to use a bundler. For details on how to do this, please refer to our [bundling documentation](https://aka.ms/AzureSDKBundling).

## Key concepts

### OnlineExperimentationClient

`OnlineExperimentationClient` is the primary interface for developers using the Azure OnlineExperimentation client library. Explore the methods on this client object to understand the different features of the Azure OnlineExperimentation service that you can access.

## Troubleshooting

### Logging

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```ts snippet:SetLogLevel
import { setLogLevel } from "@azure/logger";

setLogLevel("info");
```

For more detailed instructions on how to enable logs, you can look at the [@azure/logger package docs](https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-onlineexperimentation_1.0.0-beta.1/sdk/core/logger).

## Next steps

Please take a look at the [samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-onlineexperimentation_1.0.0-beta.1/sdk/onlineexperimentation/arm-onlineexperimentation/samples) directory for detailed examples on how to use this library.

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/arm-onlineexperimentation_1.0.0-beta.1/CONTRIBUTING.md) to learn more about how to build and test the code.

## Related projects

- [Microsoft Azure SDK for JavaScript](https://github.com/Azure/azure-sdk-for-js)

[azure_sub]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[azure_identity]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-onlineexperimentation_1.0.0-beta.1/sdk/identity/identity
[defaultazurecredential]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/arm-onlineexperimentation_1.0.0-beta.1/sdk/identity/identity#defaultazurecredential

