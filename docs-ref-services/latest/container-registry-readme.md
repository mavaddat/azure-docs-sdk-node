---
title: Azure Container Registry client library for JavaScript
keywords: Azure, javascript, SDK, API, @azure/container-registry, containerregistry
ms.date: 05/12/2023
ms.topic: reference
ms.devlang: javascript
ms.service: containerregistry
---
# Azure Container Registry client library for JavaScript - version 1.1.0 


Azure Container Registry allows you to store and manage container images and artifacts in a private registry for all types of container deployments.

Use the client library for Azure Container Registry to:

- List images or artifacts in a registry
- Obtain metadata for images and artifacts, repositories and tags
- Set read/write/delete properties on registry items
- Delete images and artifacts, repositories and tags

Key links:

- [Source code][source]
- [Package (NPM)][package]
- [API reference documentation][api_docs]
- [REST API documentation][rest_docs]
- [Product documentation][product_docs]
- [Samples][samples]

## Getting started

### Currently supported environments

- [LTS versions of Node.js](https://github.com/nodejs/release#release-schedule)

See our [support policy](https://github.com/Azure/azure-sdk-for-js/blob/@azure/container-registry_1.1.0/SUPPORT.md) for more details.

> Note: This package cannot be used in the browser due to service limitations, please refer to [this document][cors] for guidance.

### Prerequisites

- An [Azure Subscription](https://azure.microsoft.com)
- A [Container Registry account][container_registry_docs]

To create a new Container Registry, you can use the [Azure Portal][container_registry_create_portal],
[Azure PowerShell][container_registry_create_ps], or the [Azure CLI][container_registry_create_cli].
Here's an example using the Azure CLI:

```Powershell
az acr create --name MyContainerRegistry --resource-group MyResourceGroup --location westus --sku Basic
```

### Install the `@azure/container-registry` package

Install the Container Registry client library for JavaScript with `npm`:

```bash
npm install @azure/container-registry
```

### Authenticate the client

The [Azure Identity library][identity] provides easy Azure Active Directory support for authentication.

```javascript
const {
  ContainerRegistryClient,
  KnownContainerRegistryAudience,
} = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT;
// Create a ContainerRegistryClient that will authenticate through Active Directory
const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(), {
  audience: KnownContainerRegistryAudience.AzureResourceManagerPublicCloud,
});
```

Note that these samples assume you have a `CONTAINER_REGISTRY_ENDPOINT` environment variable set, which is the URL including the name of the login server and the `https://` prefix.

#### National Clouds

To authenticate with a registry in a [National Cloud](/azure/active-directory/develop/authentication-national-cloud), you will need to make the following additions to your configuration:

- Set the `authorityHost` in the credential options or via the `AZURE_AUTHORITY_HOST` environment variable
- Set the `audience` in `ContainerRegistryClientOptions`

```javascript
const {
  ContainerRegistryClient,
  KnownContainerRegistryAudience,
} = require("@azure/container-registry");
const { DefaultAzureCredential, AzureAuthorityHosts } = require("@azure/identity");

const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT;
// Create a ContainerRegistryClient that will authenticate through AAD in the China national cloud
const client = new ContainerRegistryClient(
  endpoint,
  new DefaultAzureCredential({ authorityHost: AzureAuthorityHosts.AzureChina }),
  {
    audience: KnownContainerRegistryAudience.AzureResourceManagerChina,
  }
);
```

For more information on using AAD with Azure Container Registry, please see the service's [Authentication Overview](/azure/container-registry/container-registry-authentication).

## Key concepts

A **registry** stores Docker images and [OCI Artifacts](https://opencontainers.org/). An image or artifact consists of a **manifest** and **layers**. An image's manifest describes the layers that make up the image, and is uniquely identified by its **digest**. An image can also be "tagged" to give it a human-readable alias. An image or artifact can have zero or more **tags** associated with it, and each tag uniquely identifies the image. A collection of images that share the same name but have different tags, is referred to as a **repository**.

For more information please see [Container Registry Concepts](/azure/container-registry/container-registry-concepts).

## Examples

### Registry operations

#### Listing repositories

Iterate through the collection of repositories in the registry.

```javascript
const {
  ContainerRegistryClient,
  KnownContainerRegistryAudience,
} = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // endpoint should be in the form of "https://myregistryname.azurecr.io"
  // where "myregistryname" is the actual name of your registry
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(), {
    audience: KnownContainerRegistryAudience.AzureResourceManagerPublicCloud,
  });

  console.log("Listing repositories");
  const iterator = client.listRepositoryNames();
  for await (const repository of iterator) {
    console.log(`  repository: ${repository}`);
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

#### List tags with anonymous access

```javascript
const {
  ContainerRegistryClient,
  KnownContainerRegistryAudience,
} = require("@azure/container-registry");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";

  // Create a new ContainerRegistryClient for anonymous access
  const client = new ContainerRegistryClient(endpoint, {
    audience: KnownContainerRegistryAudience.AzureResourceManagerPublicCloud,
  });

  // Obtain a RegistryArtifact object to get access to image operations
  const image = client.getArtifact("library/hello-world", "latest");

  // List the set of tags on the hello_world image tagged as "latest"
  const tagIterator = image.listTagProperties();

  // Iterate through the image's tags, listing the tagged alias for the image
  console.log(`${image.fullyQualifiedReference}  has the following aliases:`);
  for await (const tag of tagIterator) {
    console.log(`  ${tag.registryLoginServer}/${tag.repositoryName}:${tag.name}`);
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

#### Set artifact properties

```javascript
const {
  ContainerRegistryClient,
  KnownContainerRegistryAudience,
} = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";

  // Create a new ContainerRegistryClient and RegistryArtifact to access image operations
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(), {
    audience: KnownContainerRegistryAudience.AzureResourceManagerPublicCloud,
  });
  const image = client.getArtifact("library/hello-world", "v1");

  // Set permissions on the image's "latest" tag
  await image.updateTagProperties("latest", { canWrite: false, canDelete: false });
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

#### Delete images

```javascript
const {
  ContainerRegistryClient,
  KnownContainerRegistryAudience,
} = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  // Create a new ContainerRegistryClient
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(), {
    audience: KnownContainerRegistryAudience.AzureResourceManagerPublicCloud,
  });

  // Iterate through repositories
  const repositoryNames = client.listRepositoryNames();
  for await (const repositoryName of repositoryNames) {
    const repository = client.getRepository(repositoryName);
    // Obtain the images ordered from newest to oldest by passing the `order` option
    const imageManifests = repository.listManifestProperties({
      order: "LastUpdatedOnDescending",
    });
    const imagesToKeep = 3;
    let imageCount = 0;
    // Delete images older than the first three.
    for await (const manifest of imageManifests) {
      imageCount++;
      if (imageCount > imagesToKeep) {
        const image = repository.getArtifact(manifest.digest);
        console.log(`Deleting image with digest ${manifest.digest}`);
        console.log(`  Deleting the following tags from the image:`);
        for (const tagName of manifest.tags) {
          console.log(`    ${manifest.repositoryName}:${tagName}`);
          image.deleteTag(tagName);
        }
        await image.delete();
      }
    }
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### Blob and manifest operations

#### Upload images

```javascript
const { ContainerRegistryContentClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");
require("dotenv").config();

async function main() {
  // endpoint should be in the form of "https://myregistryname.azurecr.io"
  // where "myregistryname" is the actual name of your registry
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const repository = process.env.CONTAINER_REGISTRY_REPOSITORY || "library/hello-world";
  const client = new ContainerRegistryContentClient(
    endpoint,
    repository,
    new DefaultAzureCredential()
  );

  const config = Buffer.from("Sample config");
  const { digest: configDigest, sizeInBytes: configSize } = await client.uploadBlob(config);

  const layer = Buffer.from("Sample layer");
  const { digest: layerDigest, sizeInBytes: layerSize } = await client.uploadBlob(layer);

  const manifest = {
    schemaVersion: 2,
    config: {
      digest: configDigest,
      size: configSize,
      mediaType: "application/vnd.oci.image.config.v1+json",
    },
    layers: [
      {
        digest: layerDigest,
        size: layerSize,
        mediaType: "application/vnd.oci.image.layer.v1.tar",
      },
    ],
  };

  await client.setManifest(manifest, { tag: "demo" });
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

#### Download images

```javascript
const {
  ContainerRegistryContentClient,
  KnownManifestMediaType,
} = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");
const dotenv = require("dotenv");
const fs = require("fs");
dotenv.config();

function trimSha(digest) {
  const index = digest.indexOf(":");
  return index === -1 ? digest : digest.substring(index);
}

async function main() {
  // endpoint should be in the form of "https://myregistryname.azurecr.io"
  // where "myregistryname" is the actual name of your registry
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const repository = process.env.CONTAINER_REGISTRY_REPOSITORY || "library/hello-world";
  const client = new ContainerRegistryContentClient(
    endpoint,
    repository,
    new DefaultAzureCredential()
  );

  // Download the manifest to obtain the list of files in the image based on the tag
  const result = await client.getManifest("demo");

  if (result.mediaType !== KnownManifestMediaType.OciImageManifest) {
    throw new Error("Expected an OCI image manifest");
  }

  const manifest = result.manifest;

  // Manifests of all media types have a buffer containing their content; this can be written to a file.
  fs.writeFileSync("manifest.json", result.content);

  const configResult = await client.downloadBlob(manifest.config.digest);
  const configFile = fs.createWriteStream("config.json");
  configResult.content.pipe(configFile);

  // Download and write out the layers
  for (const layer of manifest.layers) {
    const fileName = trimSha(layer.digest);
    const layerStream = fs.createWriteStream(fileName);
    const downloadLayerResult = await client.downloadBlob(layer.digest);
    downloadLayerResult.content.pipe(layerStream);
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

#### Delete manifest

```javascript
const { ContainerRegistryContentClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");
require("dotenv").config();

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const repository = process.env.CONTAINER_REGISTRY_REPOSITORY || "library/hello-world";
  // Create a new ContainerRegistryClient
  const client = new ContainerRegistryContentClient(
    endpoint,
    repository,
    new DefaultAzureCredential()
  );

  const downloadResult = await client.getManifest("latest");
  await client.deleteManifest(downloadResult.digest);
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

#### Delete blob

```javascript
const {
  ContainerRegistryContentClient,
  KnownManifestMediaType,
} = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");
require("dotenv").config();

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const repository = process.env.CONTAINER_REGISTRY_REPOSITORY || "library/hello-world";
  // Create a new ContainerRegistryClient
  const client = new ContainerRegistryContentClient(
    endpoint,
    repository,
    new DefaultAzureCredential()
  );

  const downloadResult = await client.getManifest("latest");

  if (downloadResult.mediaType !== KnownManifestMediaType.OciImageManifest) {
    throw new Error("Expected an OCI image manifest");
  }

  for (const layer of downloadResult.manifest.layers) {
    await client.deleteBlob(layer.digest);
  }
}
```

## Troubleshooting

For infomation about troubleshooting, refer to the [troubleshooting guide].

## Next steps

Please take a look at the [samples][samples] directory for detailed examples that demonstrate how to use the client libraries.

## Contributing

If you'd like to contribute to this library, please read the [contributing guide](https://github.com/Azure/azure-sdk-for-js/blob/@azure/container-registry_1.1.0/CONTRIBUTING.md) to learn more about how to build and test the code.

## Related projects

- [Microsoft Azure SDK for Javascript][az_sdk_js]



[azure_sub]: https://azure.microsoft.com/free/
[acr_resource]: https://ms.portal.azure.com/#create/Microsoft.ContainerRegistry
[source]: https://github.com/Azure/azure-sdk-for-js/blob/@azure/container-registry_1.1.0/sdk/containerregistry/container-registry/
[package]: https://www.npmjs.com/package/@azure/container-registry
[api_docs]: /javascript/api/@azure/container-registry
[rest_docs]: /rest/api/containerregistry/
[product_docs]: /azure/container-registry/
[cors]: https://github.com/Azure/azure-sdk-for-js/blob/@azure/container-registry_1.1.0/samples/cors/ts/README.md
[samples]: https://github.com/Azure/azure-sdk-for-js/tree/@azure/container-registry_1.1.0/sdk/containerregistry/container-registry/samples
[container_registry_docs]: /azure/container-registry/container-registry-intro
[container_registry_create_ps]: /azure/container-registry/container-registry-get-started-powershell
[container_registry_create_cli]: /azure/container-registry/container-registry-get-started-azure-cli
[container_registry_create_portal]: /azure/container-registry/container-registry-get-started-portal
[container_registry_concepts]: /azure/container-registry/container-registry-concepts
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[identity]: https://github.com/Azure/azure-sdk-for-js/blob/@azure/container-registry_1.1.0/sdk/identity/identity/README.md
[az_sdk_js]: https://github.com/Azure/azure-sdk-for-js
[troubleshooting guide]: https://github.com/Azure/azure-sdk-for-js/blob/@azure/container-registry_1.1.0/sdk/containerregistry/container-registry/TROUBLESHOOTING.md

