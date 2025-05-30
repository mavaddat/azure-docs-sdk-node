---
title: Azure Event Grid Namespaces client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure/eventgrid-namespaces, eventgrid
ms.date: 06/11/2024
ms.topic: reference
ms.devlang: javascript
ms.service: eventgrid
---
# Azure Event Grid Namespaces client library for JavaScript - version 1.0.0 


[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) is a cloud-based service that provides reliable event delivery at massive scale.

Use the client library to Send events to Event Grid Namespaces 

Key links:

- [Source code](https://github.com/Azure/azure-sdk-for-js/blob/@azure/eventgrid-namespaces_1.0.0/sdk/eventgrid/eventgrid-namespaces/)
- [Package (NPM)](https://www.npmjs.com/package/@azure/eventgrid-namespaces)
- [API reference documentation](/javascript/api/@azure/eventgrid-namespaces/)
- [Product documentation](/azure/event-grid/)
- [Samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure/eventgrid-namespaces_1.0.0/sdk/eventgrid/eventgrid-namespaces/samples)

## Getting started

### Currently supported environments

- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)
- Latest versions of Safari, Chrome, Edge, and Firefox.

See our [support policy](https://github.com/Azure/azure-sdk-for-js/blob/@azure/eventgrid-namespaces_1.0.0/SUPPORT.md) for more details.

### Prerequisites

- An [Azure subscription][azure_sub].
- An existing [Event Grid][event_grid] Topic or Domain. If you need to create the resource, you can use the [Azure Portal][azure_portal] or [Azure CLI][azure_cli].

If you use the Azure CLI, replace `<your-resource-group-name>` and `<your-resource-name>` with your own unique names:

#### Create an Event Grid Topic

```bash
az eventgrid topic create --location <location> --resource-group <your-resource-group-name> --name <your-resource-name>
```

#### Create an Event Grid Domain

```bash
az eventgrid domain create --location <location> --resource-group <your-resource-group-name> --name <your-resource-name>
```

### Install the `@azure/eventgrid-namespaces` package

Install the Azure Event Grid Namespaces client library for JavaScript with `npm`:

```bash
npm install @azure/eventgrid-namespaces
```

### Create and authenticate Namespace clients

To create a client object to access the Event Grid Namespaces API, you will need the `endpoint` of your Event Grid topic and a `credential`. The Event Grid Namespaces clients can use an Access Key.

You can find the endpoint for your Event Grid topic either in the [Azure Portal][azure_portal] or by using the [Azure CLI][azure_cli] snippet below:

```bash
az eventgrid topic show --name <your-resource-name> --resource-group <your-resource-group-name> --query "endpoint"
```

#### Using an Access Key

Use the [Azure Portal][azure_portal] to browse to your Event Grid resource and retrieve an Access Key, or use the [Azure CLI][azure_cli] snippet below:

```bash
az eventgrid topic key list --resource-group <your-resource-group-name> --name <your-event-grid-topic-name>
```

Once you have an API key and endpoint, you can use the `AzureKeyCredential` class to authenticate the client as follows:

```js
const { EventGridSenderClient, EventGridReceiverClient, AzureKeyCredential } = require("@azure/eventgrid-namespaces");

const eventGridSenderClient = new EventGridSenderClient(
  "<endpoint>",
  new AzureKeyCredential("<Access Key>")
);

const eventGridReceiverClient = new EventGridReceiverClient(
  "<endpoint>",
  new AzureKeyCredential("<Access Key>")
);
```

Azure EventGrid provides integration with Azure Active Directory (Azure AD) for identity-based authentication of requests. With Azure AD, you can use role-based access control (RBAC) to grant access to your Azure Event Grid resources to users, groups, or applications.

To send events to a topic or domain with a `TokenCredential`, the authenticated identity should have the "EventGrid Data Sender" role assigned.

With the `@azure/identity` package, you can seamlessly authorize requests in both development and production environments. To learn more about Azure Active Directory, see the [`@azure/identity` README](https://github.com/Azure/azure-sdk-for-js/blob/@azure/eventgrid-namespaces_1.0.0/sdk/identity/identity/README.md).

For example, use can use `DefaultAzureCredential` to construct a client which will authenticate using Azure Active Directory:

```js
const { EventGridSenderClient, EventGridReceiverClient } = require("@azure/eventgrid-namespaces");
const { DefaultAzureCredential } = require("@azure/identity");

const eventGridSenderClient = new EventGridSenderClient(
  "<endpoint>",
  new DefaultAzureCredential(),
  "<topicName>"
);

const eventGridReceiverClient = new EventGridReceiverClient(
  "<endpoint>",
  new DefaultAzureCredential(),
  "<topicName>",
  "<subscriptionName>"
);
```

## Key concepts

### Sending and Receiving Events

`EventGridSenderClient` can be used for sending events to an Event Grid. You can initialize it as:

```js
const eventGridSenderClient = new EventGridSenderClient(
  "<endpoint>",
  new AzureKeyCredential("<API Key>"),
  "<topicName>"
);
```

`EventGridReceiverClient` can be used for receiving events from an Event Grid. You can initialize it as:

```js
const eventGridReceiverClient = new EventGridReceiverClient(
  "<endpoint>",
  new AzureKeyCredential("<API Key>"),
  "<topicName>",
  "<subscriptionName>"
);
```

### Distributed Tracing and Cloud Events

This library supports distributed tracing using [`@azure/core-tracing`][azure-core-tracing-github]. When using distributed tracing, this library will create a span during a `send` operation. In addition, when sending events using the Cloud Events 1.0 schema, the SDK will add distributed tracing metadata to the events using the [Distributed Tracing extension][cloud-events-distributed-tracing-spec]. The values for the `traceparent` and `tracestate` extension properties correspond to the `traceparent` and `tracestate` headers from the HTTP request which sends the events. If an event already has a `traceparent` extension property it is not updated.

### Event Grid on Kubernetes

This library has been tested and validated on [Kubernetes using Azure Arc][eventgrid-on-kubernetes-using-azure-arc].

## Examples

### Publish an Event to an Event Grid Topic

```js
const { EventGridSenderClient, AzureKeyCredential } = require("@azure/eventgrid-namespaces");

const client = new EventGridSenderClient(
  "<endpoint>",
  new AzureKeyCredential("<API key>"),
  "<topicName>"
);

const cloudEvent: CloudEvent = {
  type: "example",
  source: "https://example.com",
  id: `singleEventIdV210001`,
  time: new Date(),
  data: {
    resourceUri: "https://dummyurl.com",
  },
  specversion: "1.0",
};
// Publish the Cloud Event
await client.sendEvents(cloudEvent);
```

## Troubleshooting

### Logging

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```javascript
const { setLogLevel } = require("@azure/logger");

setLogLevel("info");
```

For more detailed instructions on how to enable the logs, you can look at the [@azure/logger package docs](https://github.com/Azure/azure-sdk-for-js/tree/@azure/eventgrid-namespaces_1.0.0/sdk/core/logger).

## Next steps

Please take a look at the
[samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure/eventgrid-namespaces_1.0.0/sdk/eventgrid/eventgrid-namespaces/samples)
directory for detailed examples on how to use this library.

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/eventgrid-namespaces_1.0.0/CONTRIBUTING.md) to learn more about how to build and test the code.

## Related projects

- [Microsoft Azure SDK for Javascript](https://github.com/Azure/azure-sdk-for-js)



[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[event_grid]: /azure/event-grid
[azure_portal]: https://portal.azure.com
[azure-core-tracing-github]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/eventgrid-namespaces_1.0.0/sdk/core/core-tracing
[cloud-events-distributed-tracing-spec]: https://github.com/cloudevents/spec/blob/v1.0.1/extensions/distributed-tracing.md
[eventgrid-on-kubernetes-using-azure-arc]: /azure/event-grid/kubernetes/

