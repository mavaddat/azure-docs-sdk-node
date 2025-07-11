---
title: Azure Communication SMS client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure/communication-sms, communication
ms.date: 06/16/2025
ms.topic: reference
ms.devlang: javascript
ms.service: communication
---
# Azure Communication SMS client library for JavaScript - version 1.2.0-beta.4 


Azure Communication SMS services gives developers the ability to send SMS messages from a phone number that can be purchased through Communication Services.

## Getting started

### Prerequisites

- An [Azure subscription][azure_sub].
- An existing Communication Services resource. If you need to create the resource, you can use the [Azure Portal][azure_portal], the [Azure PowerShell][azure_powershell], or the [Azure CLI][azure_cli].
- A phone number assigned to your Communication Services resource. See [how to acquire a phone number](#how-to-acquire-a-phone-number) for instructions on how to add a phone number to your Communication Services resource.

### Installing

```bash
npm install @azure/communication-sms
```

### How to acquire a phone number

Phone numbers can be acquired and assigned to a Communication Services resource from the [Azure Portal][azure_portal]. Instructions on how to get a phone number using the [Azure Portal][azure_portal] can be found [here][get_phone_number_az_portal].

You may also get a phone number by using the [`@azure/communication-phone-numbers`][azure_communication-phone-numbers] package. Instructions on how to use the package can be found in the [package's README][azure_communication-phone-numbers_readme].

### Browser support

#### JavaScript Bundle

To use this client library in the browser, first you need to use a bundler. For details on how to do this, please refer to our [bundling documentation](https://aka.ms/AzureSDKBundling).

## Key concepts

### SmsClient

`SmsClient` is the primary interface for developers using this client library. It provides an asynchronous method to send SMS messages.

## Examples

## Authentication

You can get a key and/or connection string from your Communication Services resource in [Azure Portal][azure_portal]. Once you have a key, you may authenticate with any of the following methods:

### Using a connection string

```ts snippet:ReadmeSampleCreateClient_ConnectionString
import { SmsClient } from "@azure/communication-sms";

const connectionString = `endpoint=https://<resource-name>.communication.azure.com/;accessKey=<Base64-Encoded-Key>`;
const client = new SmsClient(connectionString);
```

### Create a credential with `AzureKeyCredential`

```ts snippet:ReadmeSampleCreateClient_KeyCredential
import { AzureKeyCredential } from "@azure/core-auth";
import { SmsClient } from "@azure/communication-sms";

const endpoint = "https://<resource-name>.communication.azure.com";
const credential = new AzureKeyCredential("<Base64-Encoded-Key>");
const client = new SmsClient(endpoint, credential);
```

### Using Azure Active Directory managed identity

Client API key authentication is used in most of the examples, but you can also authenticate with Azure Active Directory using the [Azure Identity library][azure_identity]. To use the [DefaultAzureCredential][defaultazurecredential] provider shown below, or other credential providers provided with the Azure SDK, please install the [`@azure/identity`][azure_identity] package:

```bash
npm install @azure/identity
```

The [`@azure/identity`][azure_identity] package provides a variety of credential types that your application can use to do this. The README for @azure/identity provides more details and samples to get you started.
AZURE_CLIENT_SECRET, AZURE_CLIENT_ID and AZURE_TENANT_ID environment variables are needed to create a DefaultAzureCredential object.

```ts snippet:ReadmeSampleCreateClient_TokenCredential
import { DefaultAzureCredential } from "@azure/identity";
import { SmsClient } from "@azure/communication-sms";

const endpoint = "https://<resource-name>.communication.azure.com";
const credential = new DefaultAzureCredential();
const client = new SmsClient(endpoint, credential);
```

## Send a 1:N SMS Message

To send an SMS message, call the `send` function from the `SmsClient`. You need to pass in a `SmsSendRequest` object.
You may also add pass in an options object to specify whether the delivery report should be enabled and set custom tags for the report.
An array of `SmsSendResult` is returned. A `successful` flag is used to validate if each individual message was sent successfully.

```ts snippet:ReadmeSampleSendSms
import { DefaultAzureCredential } from "@azure/identity";
import { SmsClient } from "@azure/communication-sms";

const endpoint = "https://<resource-name>.communication.azure.com";
const credential = new DefaultAzureCredential();
const client = new SmsClient(endpoint, credential);

const sendResults = await client.send(
  {
    from: "<from-phone-number>", // Your E.164 formatted phone number used to send SMS
    to: ["<to-phone-number-1>", "<to-phone-number-2>"], // The list of E.164 formatted phone numbers to which message is being sent
    message: "Weekly Promotion!", // The message being sent
  },
  {
    enableDeliveryReport: true,
    tag: "marketing",
  },
);

for (const sendResult of sendResults) {
  if (sendResult.successful) {
    console.log("Success: ", sendResult);
  } else {
    console.error("Something went wrong when trying to send this message: ", sendResult);
  }
}
```

## Check if a list of recipients is in the Opt Out list

To check if the recipients are in the Opt Out list, call the `check` function from the `SmsClient.optOuts` with a list of recipient phone numbers.

```ts snippet:ReadmeSampleOptOutCheck
import { DefaultAzureCredential } from "@azure/identity";
import { SmsClient } from "@azure/communication-sms";

const endpoint = "https://<resource-name>.communication.azure.com";
const credential = new DefaultAzureCredential();
const client = new SmsClient(endpoint, credential);

const optOutCheckResults = await client.optOuts.check(
  "<from-phone-number>", // Your E.164 formatted phone number used to send SMS
  ["<to-phone-number-1>", "<to-phone-number-2>"],
);

for (const optOutCheckResult of optOutCheckResults) {
  if (optOutCheckResult.httpStatusCode === 200) {
    console.log("Success: ", optOutCheckResult);
  } else {
    console.error(
      "Something went wrong when trying to send opt out check request: ",
      optOutCheckResult,
    );
  }
}
```

## Add a list of recipients to Opt Out list

To add the list of recipients to Opt Out list, call the `add` function from the `SmsClient.optOuts` with a list of recipient phone numbers.

```ts snippet:ReadmeSampleOptOutAdd
import { DefaultAzureCredential } from "@azure/identity";
import { SmsClient } from "@azure/communication-sms";

const endpoint = "https://<resource-name>.communication.azure.com";
const credential = new DefaultAzureCredential();
const client = new SmsClient(endpoint, credential);

const optOutAddResults = await client.optOuts.add(
  "<from-phone-number>", // Your E.164 formatted phone number used to send SMS
  ["<to-phone-number-1>", "<to-phone-number-2>"],
);

for (const optOutAddResult of optOutAddResults) {
  if (optOutAddResult.httpStatusCode === 200) {
    console.log("Success: ", optOutAddResult);
  } else {
    console.error(
      "Something went wrong when trying to send opt out add request: ",
      optOutAddResult,
    );
  }
}
```

## Remove a list of recipients from Opt Out list

To remove the list of recipients to Opt Out list, call the `remove` function from the `SmsClient.optOuts.` with a list of recipient phone numbers.

```ts snippet:ReadmeSampleOptOutRemove
import { DefaultAzureCredential } from "@azure/identity";
import { SmsClient } from "@azure/communication-sms";

const endpoint = "https://<resource-name>.communication.azure.com";
const credential = new DefaultAzureCredential();
const client = new SmsClient(endpoint, credential);

const optOutRemoveResults = await client.optOuts.remove(
  "<from-phone-number>", // Your E.164 formatted phone number used to send SMS
  ["<to-phone-number-1>", "<to-phone-number-2>"],
);

for (const optOutRemoveResult of optOutRemoveResults) {
  if (optOutRemoveResult.httpStatusCode === 200) {
    console.log("Success: ", optOutRemoveResult);
  } else {
    console.error(
      "Something went wrong when trying to send opt out remove request: ",
      optOutRemoveResult,
    );
  }
}
```

## Troubleshooting

SMS operations will throw an exception if the request to the server fails.
Exceptions will not be thrown if the error is caused by an individual message, only if something fails with the overall request.
Please use the `successful` flag to validate each individual result to verify if the message was sent.

```ts snippet:ReadmeSampleSendSmsErrorHandling
import { DefaultAzureCredential } from "@azure/identity";
import { SmsClient } from "@azure/communication-sms";

const endpoint = "https://<resource-name>.communication.azure.com";
const credential = new DefaultAzureCredential();
const client = new SmsClient(endpoint, credential);

try {
  const sendResults = await client.send({
    from: "<from-phone-number>", // Your E.164 formatted phone number used to send SMS
    to: ["<to-phone-number-1>", "<to-phone-number-2>"], // The list of E.164 formatted phone numbers to which message is being sent
    message: "Hello World via SMS!", // The message being sent
  });
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
} catch (e) {
  console.error(e.message);
}
```

### Logging

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```ts snippet:SetLogLevel
import { setLogLevel } from "@azure/logger";

setLogLevel("info");
```

For more detailed instructions on how to enable logs, you can look at the [@azure/logger package docs](https://github.com/Azure/azure-sdk-for-js/tree/@azure/communication-sms_1.2.0-beta.4/sdk/core/logger).

## Next steps

- Please take a look at the [samples](https://github.com/Azure/azure-sdk-for-js/blob/@azure/communication-sms_1.2.0-beta.4/sdk/communication/communication-sms/samples) directory for detailed examples on how to use this library.
- [Read more about SMS in Azure Communication Services][next_steps]
- For a basic guide on how to configure Delivery Reporting for your SMS messages please refer to the [Handle SMS Events quickstart][handle_sms_events].

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/communication-sms_1.2.0-beta.4/CONTRIBUTING.md) to learn more about how to build and test the code.

## Related projects

- [Microsoft Azure SDK for Javascript](https://github.com/Azure/azure-sdk-for-js)

[azure_cli]: https://learn.microsoft.com/cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[azure_powershell]: https://learn.microsoft.com/powershell/module/az.communication/new-azcommunicationservice
[defaultazurecredential]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/communication-sms_1.2.0-beta.4/sdk/identity/identity#defaultazurecredential
[azure_identity]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/communication-sms_1.2.0-beta.4/sdk/identity/identity
[get_phone_number_az_portal]: https://learn.microsoft.com/azure/communication-services/quickstarts/telephony/get-phone-number?pivots=platform-azp
[azure_communication-phone-numbers]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/communication-sms_1.2.0-beta.4/sdk/communication/communication-phone-numbers
[azure_communication-phone-numbers_readme]: https://github.com/Azure/azure-sdk-for-js/blob/@azure/communication-sms_1.2.0-beta.4/sdk/communication/communication-phone-numbers/README.md
[handle_sms_events]: https://learn.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events
[next_steps]: https://learn.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-javascript

