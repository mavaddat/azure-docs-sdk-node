---
title: 
keywords: Azure, javascript, SDK, API, @azure/identity-cache-persistence, entra-id
ms.date: 02/18/2025
ms.topic: reference
ms.devlang: javascript
ms.service: entra-id
---
# Azure Identity Plugin for Token Cache Persistence

This package provides a plugin to the Azure Identity library for JavaScript ([`@azure/identity`](https://npmjs.com/package/@azure/identity)) that enables persistent token caching. Token cache persistence allows the built-in token cache to persist across sessions using a secure storage system provided by the local operating system.

[Source code](https://github.com/Azure/azure-sdk-for-js/tree/@azure/identity-cache-persistence_1.2.0/sdk/identity/identity-cache-persistence) | [Samples](https://github.com/Azure/azure-sdk-for-js/blob/@azure/identity-cache-persistence_1.2.0/sdk/identity/identity-cache-persistence/samples-dev)

## Getting started

### Prerequisites

- An [Azure subscription](https://azure.microsoft.com/free/).

### Install the package

This package is designed to be used with Azure Identity for JavaScript. Install both `@azure/identity` and this package using `npm`:

```sh
$ npm install --save @azure/identity
$ npm install --save @azure/identity-cache-persistence
```

#### Supported Environments

Azure Identity plugins for JavaScript support stable (even numbered) versions of Node.js starting from v12. While the plugins may run in other Node versions, no support is guaranteed. `@azure/identity-cache-persistence` **does not** support browser environments.

## Key concepts

If this is your first time using `@azure/identity` or Microsoft Entra ID, we recommend that you read [Using `@azure/identity` with Microsoft Entra ID](https://github.com/Azure/azure-sdk-for-js/blob/@azure/identity-cache-persistence_1.2.0/documentation/using-azure-identity.md) first. This document will give you a deeper understanding of the platform and how to configure your Azure account correctly.

### Azure Identity Plugins

As of `@azure/identity` version 2.0.0, the Identity client library for JavaScript includes a plugin API. This package (`@azure/identity-cache-persistence`) exports a plugin object that you must pass as an argument to the top-level `useIdentityPlugin` function from the `@azure/identity` package. Enable token cache persistence in your program as follows:

```ts snippet:getting_started
import { useIdentityPlugin } from "@azure/identity";
import { cachePersistencePlugin } from "@azure/identity-cache-persistence";

useIdentityPlugin(cachePersistencePlugin);
```

After calling `useIdentityPlugin`, the persistent token cache plugin is registered to the `@azure/identity` package and will be available on all credentials that support persistent token caching (those that have `tokenCachePersistenceOptions` in their constructor options).

## Examples

Once the plugin is registered, you can enable token cache persistence by passing `tokenCachePersistenceOptions` with an `enabled` property set to `true` to a credential constructor. In the following example, we use the `DeviceCodeCredential`, since persistent caching of its tokens allows you to skip the interactive device-code authentication flow if a cached token is available.

```ts snippet:device_code_credential_example
import { DeviceCodeCredential } from "@azure/identity";

const credential = new DeviceCodeCredential({
  tokenCachePersistenceOptions: {
    enabled: true,
  },
});

// We'll use the Microsoft Graph scope as an example
const scope = "https://graph.microsoft.com/.default";

// Print out part of the access token
console.log((await credential.getToken(scope)).token.substring(0, 10), "...");
```

## Troubleshooting

### Logging

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```ts snippet:logging
import { setLogLevel } from "@azure/logger";

setLogLevel("info");
```

## Next steps

### Provide Feedback

If you encounter bugs or have suggestions, please [open an issue](https://github.com/Azure/azure-sdk-for-js/issues).

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/identity-cache-persistence_1.2.0/CONTRIBUTING.md) to learn more about how to build and test the code.

