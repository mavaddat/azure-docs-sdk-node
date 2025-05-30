---
title: Azure Azure Digital Twins Core client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure/digital-twins-core, digitaltwins
ms.date: 05/27/2025
ms.topic: reference
ms.devlang: javascript
ms.service: digitaltwins
---
# Azure Azure Digital Twins Core client library for JavaScript - version 2.0.0 


This package contains an isomorphic SDK for Azure Digital Twins API to provide access to the Azure Digital Twins service for managing twins, models, relationships, etc.

## Getting started

### Currently supported environments

- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)
- Latest versions of Safari, Chrome, Edge, and Firefox.

See our [support policy](https://github.com/Azure/azure-sdk-for-js/blob/@azure/digital-twins-core_2.0.0/SUPPORT.md) for more details.

### Prerequisites

- An [Azure Digital Twins instance](https://learn.microsoft.com/azure/digital-twins/how-to-set-up-instance-portal).

### Install the `@azure/digital-twins-core` package

Install the Digital Twins Core client library for JavaScript with `npm`:

```bash
npm install @azure/digital-twins-core
```

### Browser support

#### JavaScript Bundle

To use this client library in the browser, first you need to use a bundler. For details on how to do this, please refer to our
[bundling documentation](https://aka.ms/AzureSDKBundling).

#### CORS

Azure Digital Twins doesn't currently support Cross-Origin Resource Sharing (CORS).
As a result, this library cannot be used to make direct calls to the template service from a browser.
Please refer to [this document](https://github.com/Azure/azure-sdk-for-js/blob/@azure/digital-twins-core_2.0.0/samples/cors/ts/README.md) for guidance.

## Key concepts

### Azure Digital Twins

Azure Digital Twins is an Azure IoT service that creates comprehensive models of the physical environment.
It can create spatial intelligence graphs to model the relationships and interactions between people, spaces, and devices.
You can learn more about Azure Digital Twins by visiting [Azure Digital Twins Documentation](https://learn.microsoft.com/azure/digital-twins/).

### `DigitalTwinsClient`

`DigitalTwinsClient` is the client object that users of this library use to manage their Azure Digital Twins instance.

## Examples

### Create the DigitalTwinsClient

To create a new `DigitalTwinsClient`, you need the endpoint to an Azure Digital Twins instance and credentials.
Here, we use `DefaultAzureCredential` for credentials from the package `@azure/identity`.
It supports different authentication mechanisms and determines the appropriate credential type based of the environment it is executing in.
See the [readme for @azure/identity](https://www.npmjs.com/package/@azure/identity) for more information on the different authentication options you can use.

```ts snippet:ReadmeSampleCreateClient_Node
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);
```

### Create, list, get, decommission, and delete models

#### Create models

In order to create models, we pass in a list of models to `createModels`.
Here, we only create one model.

```ts snippet:ReadmeSampleCreateModels
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const myComponent = {
  "@id": "dtmi:my_component;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  displayName: "Component1",
  contents: [
    {
      "@type": "Property",
      name: "ComponentProp1",
      schema: "string",
    },
  ],
};

const models = await serviceClient.createModels([myComponent]);
```

#### List models

We use `listModels` to list all the models.

```ts snippet:ReadmeSampleListModels
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const models = serviceClient.listModels();
for await (const model of models) {
  console.log(`Model ID: ${model.id}`);
}
```

#### Get model

We can get a specific model using `getModel` with the model ID.

```ts snippet:ReadmeSampleGetModel
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const model = await serviceClient.getModel("<model ID>");
```

#### Decommission model

We can decommission a model using `decomissionModel` with the model ID.

```ts snippet:ReadmeSampleDecomissionModel
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

await serviceClient.decomissionModel("<model ID>");
```

#### Delete model

We can delete a model using `deleteModel` with the model ID.

```ts snippet:ReadmeSampleDeleteModel
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

await serviceClient.deleteModel("<model ID>");
```

### Create, get, query, and delete digital twins

#### Create digital twin

To create a twin, you will need to provide an ID for the digital twin and a JSON string containing the digital twin object.

```ts snippet:ReadmeSampleCreateDigitalTwin
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "myTwin";
const newTwin = "<JSON containing the digitalTwin object>";
const createdTwin = await serviceClient.upsertDigitalTwin(digitalTwinId, newTwin);
```

#### Get digital twin

We can get a digital twin using `getDigitalTwin` with the digital twin ID.

```ts snippet:ReadmeSampleGetDigitalTwin
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "myTwin";
const twin = await serviceClient.getDigitalTwin(digitalTwinId);
console.log(`DigitalTwin's etag: ${twin.etag}`);
console.log(`DigitalTwin: ${twin}`);
```

#### Query digital twins

Query the Azure Digital Twins instance for digital twins using the [Azure Digital Twins query language](https://learn.microsoft.com/azure/digital-twins/how-to-query-graph).
Here's an example of how to query for digital twins and how to iterate over the results.

```ts snippet:ReadmeSampleQueryDigitalTwins
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const query = "SELECT * FROM digitaltwins";
const queryResult = serviceClient.queryTwins(query);
for await (const item of queryResult) {
  console.log(`DigitalTwin: ${item}`);
}
```

#### Delete digital twin

We can delete a digital twin using `deleteDigitalTwin` with the digital twin ID.

```ts snippet:ReadmeSampleDeleteDigitalTwin
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "myTwin";
await serviceClient.deleteDigitalTwin(digitalTwinId);
```

### Get and update digital twin components

#### Get digital twin component

We can get a digital twin component using `getComponent` with the digital twin ID and the path of the component.

```ts snippet:ReadmeSampleGetComponent
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "myTwin";
const componentPath = "Component1";
const component = await serviceClient.getComponent(digitalTwinId, componentPath);
console.log(`Component: ${component}`);
```

#### Update digital twin component

To update a digital twin component (i.e., replace, remove, or add a component property or sub-property within a digital twin), you need to provide a digital twin ID, component path, and a list of patch objects with the properties `op` and `path`.
The value of `op` is "replace", "remove", or "add", and the value of `path` is the path to the digital twin component being updated.
For "replace" and "add" operations, the `value` property should be included with your desired value of the component property.

```ts snippet:ReadmeSampleUpdateComponent
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "myTwin";
const componentPath = "Component1";
const patch = {
  op: "replace",
  path: "/ComponentProp1",
  value: "value2",
};
const updateComponentResponse = await serviceClient.updateComponent(digitalTwinId, componentPath, [
  patch,
]);
```

### Create and list digital twin relationships

#### Create digital twin relationships

`upsertRelationship` creates a relationship on a digital twin provided with ID of a digital twin, name of relationship (in this case, "has"), ID of an relationship (in this case "BuildingHasFloor") and the object representing the relationship to be created.
The object must contain property with key "\$targetId" to specify the target of the relationship.

```ts snippet:ReadmeSampleCreateRelationship
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const relationship = {
  $relationshipId: "BuildingHasFloor",
  $sourceId: "BuildingTwin",
  $relationshipName: "has",
  $targetId: "FloorTwin",
  isAccessRestricted: false,
};

await serviceClient.upsertRelationship(
  relationship["$sourceId"],
  relationship["$relationshipId"],
  relationship,
);
```

#### List digital twin relationships

For a digital twin, `listRelationships` and `listIncomingRelationships` list all the relationships and all incoming relationships, respectively.

```ts snippet:ReadmeSampleListRelationships
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "myTwin";
const relationships = serviceClient.listRelationships(digitalTwinId);
for await (const relationship of relationships) {
  console.log(`Relationship: ${relationship}`);
}
```

```ts snippet:ReadmeSampleListIncomingRelationships
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "myTwin";
const incomingRelationships = serviceClient.listIncomingRelationships(digitalTwinId);
for await (const incomingRelationship of incomingRelationships) {
  console.log(`Relationship: ${incomingRelationship}`);
}
```

### Create, get, list, and delete event routes

#### Create event route

To create an event route, provide an ID of an event route (in this case, "myEventRouteId") and event route data containing the endpoint and optional filter like the example shown below.
For more information on filtering events, see [this documentation](https://learn.microsoft.com/azure/digital-twins/how-to-manage-routes-apis-cli#filter-events).

```ts snippet:ReadmeSampleCreateEventRoute
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const eventHubEndpointName = "myEventHubEndpointName";
const eventRouteId = "myEventRouteId";
const eventFilter =
  "$eventType = 'DigitalTwinTelemetryMessages' or $eventType = 'DigitalTwinLifecycleNotification'";
await serviceClient.upsertEventRoute(eventRouteId, eventHubEndpointName, eventFilter);
```

#### Get event route

We can get an event route using `getEventRoute` with the event route ID.

```ts snippet:ReadmeSampleGetEventRoute
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const eventRouteId = "myEventRouteId";
const eventRoute = serviceClient.getEventRoute(eventRouteId);
console.log(`EventRoute: ${eventRoute}`);
```

#### List event routes

We can list event routes using `listEventRoutes`.

```ts snippet:ReadmeSampleListEventRoutes
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const eventRoutes = serviceClient.listEventRoutes();
for await (const eventRoute of eventRoutes) {
  console.log(`EventRoute: ${eventRoute}`);
}
```

#### Delete event route

We can delete an event route using `deleteEventRoute` with the event route ID.

```ts snippet:ReadmeSampleDeleteEventRoute
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const eventRouteId = "myEventRouteId";
await serviceClient.deleteEventRoute(eventRouteId);
```

### Publish telemetry messages for a digital twin

To publish a telemetry message for a digital twin, you need to provide the digital twin ID, the payload, and a unique ID for the message.

```ts snippet:ReadmeSamplePublishTelemetry
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "<digital twin ID>";
const telemetryPayload = { Telemetry1: 5 };
const response = await serviceClient.publishTelemetry(
  digitalTwinId,
  telemetryPayload,
  "<unique message ID>",
);
```

You can also publish a telemetry message for a specific component in a digital twin.
In addition to the digital twin ID, payload, and unique message ID, you need to specify the target component path.

```ts snippet:ReadmeSamplePublishComponentTelemetry
import { DefaultAzureCredential } from "@azure/identity";
import { DigitalTwinsClient } from "@azure/digital-twins-core";

const url = "<URL to Azure Digital Twins instance>";
const credential = new DefaultAzureCredential();
const serviceClient = new DigitalTwinsClient(url, credential);

const digitalTwinId = "<digital twin ID>";
const componentPath = "<component path>";
const telemetryPayload = { Telemetry1: 5 };
const response = await serviceClient.publishComponentTelemetry(
  digitalTwinId,
  componentPath,
  telemetryPayload,
  "<unique message ID>",
);
```

### Additional Examples

Additional examples can be found in the
[samples directory](https://github.com/Azure/azure-sdk-for-js/tree/@azure/digital-twins-core_2.0.0/sdk/digitaltwins/digital-twins-core/samples).

## Troubleshooting

### Logging

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```ts snippet:SetLogLevel
import { setLogLevel } from "@azure/logger";

setLogLevel("info");
```

For more detailed instructions on how to enable logs, you can look at the [@azure/logger package docs](https://github.com/Azure/azure-sdk-for-js/tree/@azure/digital-twins-core_2.0.0/sdk/core/logger).

## Next steps

- Take a look at the [samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure/digital-twins-core_2.0.0/sdk/digitaltwins/digital-twins-core/samples) directory for detailed examples that demonstrate how to use the client libraries.
- Explore the Azure Digital Twins [documentation](https://learn.microsoft.com/azure/digital-twins/)

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/digital-twins-core_2.0.0/CONTRIBUTING.md) to learn more about how to build and test the code.

## Related projects

- [Microsoft Azure SDK for Javascript](https://github.com/Azure/azure-sdk-for-js)

