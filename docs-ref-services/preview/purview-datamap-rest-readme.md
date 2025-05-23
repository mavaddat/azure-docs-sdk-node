---
title: Azure PurviewDataMap REST client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure-rest/purview-datamap, purview
ms.date: 04/11/2025
ms.topic: reference
ms.devlang: javascript
ms.service: purview
---
# Azure PurviewDataMap REST client library for JavaScript - version 1.0.0-beta.3 


Microsoft Purview Data Map provides the foundation for data discovery and data governance. Microsoft Purview Data Map is a cloud native PaaS service that captures metadata about enterprise data present in analytics and operation systems on-premises and cloud. Azure PurviewDataMap client provides a set of APIs in Purview Data Map Data Plane. For a full list of APIs, please refer to [Data Map API](https://learn.microsoft.com/rest/api/purview/datamapdataplane/operation-groups?view=rest-purview-datamapdataplane-2023-09-01).

**Please rely heavily on our [REST client docs](https://github.com/Azure/azure-sdk-for-js/blob/@azure-rest/purview-datamap_1.0.0-beta.3/documentation/rest-clients.md) to use this library**

Key links:

- [Source code](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/purview-datamap_1.0.0-beta.3/sdk/purview/purview-datamap-rest)
- [Package (NPM)](https://www.npmjs.com/package/@azure-rest/purview-datamap)
- [API reference documentation](https://learn.microsoft.com/javascript/api/@azure-rest/purview-datamap?view=azure-node-preview)
- [Samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/purview-datamap_1.0.0-beta.3/sdk/purview/purview-datamap-rest/samples)

## Getting started

### Currently supported environments

- LTS versions of Node.js

### Prerequisites

- You must have an [Azure subscription](https://azure.microsoft.com/free/) to use this package.

### Install the `@azure-rest/purview-datamap` package

Install the Azure PurviewDataMap REST client REST client library for JavaScript with `npm`:

```bash
npm install @azure-rest/purview-datamap
```

### Create and authenticate a `PurviewDataMapClient`

To use an [Azure Active Directory (AAD) token credential](https://github.com/Azure/azure-sdk-for-js/blob/@azure-rest/purview-datamap_1.0.0-beta.3/sdk/identity/identity/samples/AzureIdentityExamples.md#authenticating-with-a-pre-fetched-access-token),
provide an instance of the desired credential type obtained from the
[@azure/identity](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/purview-datamap_1.0.0-beta.3/sdk/identity/identity#credentials) library.

To authenticate with AAD, you must first `npm` install [`@azure/identity`](https://www.npmjs.com/package/@azure/identity)

After setup, you can choose which type of [credential](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/purview-datamap_1.0.0-beta.3/sdk/identity/identity#credentials) from `@azure/identity` to use.
As an example, [DefaultAzureCredential](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/purview-datamap_1.0.0-beta.3/sdk/identity/identity#defaultazurecredential)
can be used to authenticate the client.

Set the values of the client ID, tenant ID, and client secret of the AAD application as environment variables:
AZURE_CLIENT_ID, AZURE_TENANT_ID, AZURE_CLIENT_SECRET

Use the returned token credential to authenticate the client:

```ts snippet:ReadmeSampleCreateClient_Node
import PurviewDataMap from "@azure-rest/purview-datamap";
import { DefaultAzureCredential } from "@azure/identity";

const client = PurviewDataMap(
  "https://<my-account-name>.purview.azure.com",
  new DefaultAzureCredential(),
);
```

## Examples

The following sections provide several code snippets covering some of the most common Purview DataMap scenarios, including:

- [Get All Type Definitions](#get-all-type-definitions)

### Get All Type Definitions

```ts snippet:ReadmeSampleGetAllTypeDefinitions
import PurviewDataMap, { isUnexpected } from "@azure-rest/purview-datamap";
import { DefaultAzureCredential } from "@azure/identity";

const client = PurviewDataMap(
  "https://<my-account-name>.purview.azure.com",
  new DefaultAzureCredential(),
);

const result = await client.path("/atlas/v2/types/typedefs").get();

if (isUnexpected(result)) {
  throw result.body;
}

for (const entityDef of result.body?.businessMetadataDefs) {
  console.log(`Entity Definition Name: ${entityDef.name}`);
}

for (const entityDef of result.body?.classificationDefs) {
  console.log(`Entity Definition Name: ${entityDef.name}`);
}

for (const entityDef of result.body?.entityDefs) {
  console.log(`Entity Definition Name: ${entityDef.name}`);
}

for (const entityDef of result.body?.enumDefs) {
  console.log(`Entity Definition Name: ${entityDef.name}`);
}

for (const entityDef of result.body?.relationshipDefs) {
  console.log(`Entity Definition Name: ${entityDef.name}`);
}

for (const entityDef of result.body?.structDefs) {
  console.log(`Entity Definition Name: ${entityDef.name}`);
}

for (const entityDef of result.body?.termTemplateDefs) {
  console.log(`Entity Definition Name: ${entityDef.name}`);
}
```

## Troubleshooting

### Logging

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```ts snippet:SetLogLevel
import { setLogLevel } from "@azure/logger";

setLogLevel("info");
```

For more detailed instructions on how to enable logs, you can look at the [@azure/logger package docs](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/purview-datamap_1.0.0-beta.3/sdk/core/logger).

