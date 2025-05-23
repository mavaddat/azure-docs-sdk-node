---
title: Azure Document Translator Rest-Level client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure-rest/ai-document-translator, documenttranslator
ms.date: 02/14/2025
ms.topic: reference
ms.devlang: javascript
ms.service: documenttranslator
---
# Azure Document Translator Rest-Level client library for JavaScript - version 1.0.0-beta.2 


[Azure Document Translator](https://learn.microsoft.com/azure/cognitive-services/translator/document-translation/overview) is a cloud-based feature of the Azure Translator service and is part of the Azure Cognitive Service family of REST APIs. The Document Translation API translates documents to and from 90 languages and dialects while preserving document structure and data format.

**Note:** This Rest Level Library targets Azure Document Translator service API version v1.0-preview.1.

Use the client library to:

| Feature                           | Description                                                                                                                                                         |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Translate large files             | Translate whole documents asynchronously.                                                                                                                           |
| Translate numerous files          | Translate multiple files to and from 90 languages and dialects.                                                                                                     |
| Preserve source file presentation | Translate files while preserving the original layout and format.                                                                                                    |
| Apply custom translation          | Translate documents using general and [custom translation](https://learn.microsoft.com/azure/cognitive-services/translator/customization#custom-translator) models. |
| Apply custom glossaries           | Translate documents using custom glossaries.                                                                                                                        |

[Source code](https://github.com/Azure/azure-sdk-for-js/blob/@azure-rest/ai-document-translator_1.0.0-beta.2/sdk/documenttranslator/ai-document-translator-rest/) |
[Package (NPM)](https://www.npmjs.com/package/@azure-rest/ai-document-translator) |
[API reference documentation](https://learn.microsoft.com/azure/cognitive-services/translator/document-translation/overview) |
[Product documentation](https://learn.microsoft.com/azure/cognitive-services/translator/document-translation/overview) |
[Samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/ai-document-translator_1.0.0-beta.2/sdk/documenttranslator/ai-document-translator-rest/samples)

## Getting started

### Currently supported environments

- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)

### Prerequisites

- An [Azure subscription][azure_sub].
- An existing [Translator Service][translator_resource] resource. If you need to create the resource, you can use the [Azure Portal][azure_portal] or [Azure CLI][azure_cli].

### Install the `@azure-rest/ai-document-translator` package

Install the Azure Document Translator client library for JavaScript with `npm`:

```bash
npm install @azure-rest/ai-document-translator
```

### Create a Document Translation resource

Document Translation supports [single-service access][single_service] only.
To access the service, create a Translator resource.

You can create the resource using

**Option 1:** [Azure Portal][translator_resource]

**Option 2:** [Azure CLI][azure_cli_create_dt_resource].
Below is an example of how you can create a Document Translation resource using the CLI:

```bash
# Create a new resource group to hold the document translation resource -
# if using an existing resource group, skip this step
az group create --name my-resource-group --location westus2
```

```bash
# Create document translation
az cognitiveservices account create \
    --name document-translation-resource \
    --custom-domain document-translation-resource \
    --resource-group my-resource-group \
    --kind TextTranslation \
    --sku S1 \
    --location westus2 \
    --yes
```

### Create and authenticate a `DocumentTranslator`

#### Looking up the endpoint

You can find the endpoint for your Document Translation resource using the
[Azure Portal][azure_portal_get_endpoint].

> Note that the service requires a custom domain endpoint. Follow the instructions in the above link to format your endpoint:
> https://{NAME-OF-YOUR-RESOURCE}.cognitiveservices.azure.com/

#### Using an API Key

The API key can be found in the Azure Portal or by running the following Azure CLI command:

```bash
az cognitiveservices account keys list --name "resource-name" --resource-group "resource-group-name"
```

Once you have an API key and endpoint, you can use the `AzureKeyCredential` class to authenticate the client as follows:

```ts snippet:ReadmeSampleCreateClient_APIKey
import DocumentTranslator from "@azure-rest/ai-document-translator";

const client = DocumentTranslator("<endpoint>", { key: "<API key>" });
```

## Key concepts

The Document Translation service requires that you upload your files to an Azure Blob Storage source container and provide
a target container where the translated documents can be written. SAS tokens to the containers (or files) are used to
access the documents and create the translated documents in the target container. Additional information about setting this up can be found in
the service documentation:

- [Set up Azure Blob Storage containers][source_containers] with your documents
- Optionally apply [glossaries][glossary] or a [custom model for translation][custom_model]
- Generate [SAS tokens][sas_token] to your containers (or files)

### DocumentTranslator

Interaction with the Document Translation rest library begins with an instance of the `DocumentTranslation`.
The client provides operations for:

- Creating a translation job to translate documents in your source container(s) and write results to you target container(s).
- Checking the status of individual documents in the translation job and monitoring each document's progress.
- Enumerating all past and current translation jobs with the option to wait until the job(s) finish.
- Identifying supported glossary and document formats.

### Translation Input

To create a translation job, send a post request with body of type `BatchSubmissionRequest` to the `"/batches/"` path..
Creating a `BatchSubmissionRequest` requires that you pass the SAS URLs to your source and target containers (or files)
and the target language(s) for translation.

A single source container with documents can be translated to many different languages:

```ts snippet:ReadmeSampleBatchSubmissionRequest
import { StartTranslationDetails } from "@azure-rest/ai-document-translator";

const batchSubmissionRequest: StartTranslationDetails = {
  inputs: [
    {
      source: { sourceUrl: "<sas_url_to_source>" },
      targets: [{ language: "fr", targetUrl: "<sas_url_to_target_fr>" }],
    },
  ],
};
```

Or multiple different sources can be provided each with their own targets.

```ts snippet:ReadmeSampleBatchSubmissionRequestMultiple
import { StartTranslationDetails } from "@azure-rest/ai-document-translator";

const batchSubmissionRequest: StartTranslationDetails = {
  inputs: [
    {
      source: { sourceUrl: "<sas_url_to_source_A>" },
      targets: [
        { language: "fr", targetUrl: "<sas_url_to_target_A_fr>" },
        { language: "de", targetUrl: "<sas_url_to_target_A_de>" },
      ],
    },
    {
      source: { sourceUrl: "<sas_url_to_source_B>" },
      targets: [
        { language: "fr", targetUrl: "<sas_url_to_target_B_fr>" },
        { language: "de", targetUrl: "<sas_url_to_target_B_de>" },
      ],
    },
  ],
};
```

> Note: the target_url for each target language must be unique.

See the service documentation for all [supported languages][supported_languages].

## Examples

Please refer to the samples folder to see code samples, including:

- [List Supported Formats](https://github.com/Azure/azure-sdk-for-js/blob/@azure-rest/ai-document-translator_1.0.0-beta.2/sdk/documenttranslator/ai-document-translator-rest/samples/v1/typescript/src/listFormats.ts)
- [Translate documents](https://github.com/Azure/azure-sdk-for-js/blob/@azure-rest/ai-document-translator_1.0.0-beta.2/sdk/documenttranslator/ai-document-translator-rest/samples/v1/typescript/src/translateFromBlob.ts)

## Troubleshooting

### Logging

Enabling logging may help uncover useful information about failures. In order to see a log of HTTP requests and responses, set the `AZURE_LOG_LEVEL` environment variable to `info`. Alternatively, logging can be enabled at runtime by calling `setLogLevel` in the `@azure/logger`:

```ts snippet:SetLogLevel
import { setLogLevel } from "@azure/logger";

setLogLevel("info");
```

For more detailed instructions on how to enable logs, you can look at the [@azure/logger package docs](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/ai-document-translator_1.0.0-beta.2/sdk/core/logger).

## Next steps

Please take a look at the [samples](https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/ai-document-translator_1.0.0-beta.2/sdk/documenttranslator/ai-document-translator-rest/samples) directory for detailed examples on how to use this library.

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure-rest/ai-document-translator_1.0.0-beta.2/CONTRIBUTING.md) to learn more about how to build and test the code.

## Related projects

- [Microsoft Azure SDK for JavaScript](https://github.com/Azure/azure-sdk-for-js)



[azure_cli]: https://learn.microsoft.com/cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[translator_resource]: https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation
[azure_portal]: https://portal.azure.com
[azure_identity]: https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/ai-document-translator_1.0.0-beta.2/sdk/identity/identity
[cognitive_auth]: https://learn.microsoft.com/azure/cognitive-services/authentication
[register_aad_app]: https://learn.microsoft.com/azure/cognitive-services/authentication#assign-a-role-to-a-service-principal
[defaultazurecredential]: https://github.com/Azure/azure-sdk-for-js/tree/@azure-rest/ai-document-translator_1.0.0-beta.2/sdk/identity/identity#defaultazurecredential
[azure_cli_create_dt_resource]: https://learn.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli?tabs=windows
[azure_portal_get_endpoint]: https://learn.microsoft.com/azure/ai-services/translator/document-translation/quickstarts/document-translation-rest-api?pivots=programming-language-javascript#retrieve-your-key-and-document-translation-endpoint
[source_containers]: https://learn.microsoft.com/azure/ai-services/translator/document-translation/quickstarts/document-translation-rest-api?pivots=programming-language-javascript#create-azure-blob-storage-containers
[glossary]: https://learn.microsoft.com/azure/cognitive-services/translator/document-translation/overview#supported-glossary-formats
[custom_model]: https://learn.microsoft.com/azure/cognitive-services/translator/custom-translator/quickstart-build-deploy-custom-model
[sas_token]: https://learn.microsoft.com/azure/ai-services/translator/document-translation/how-to-guides/create-sas-tokens?tabs=Containers
[supported_languages]: https://learn.microsoft.com/azure/cognitive-services/translator/language-support#translate

