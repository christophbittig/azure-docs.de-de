---
title: 'Schnellstart: Verwalten von Containerregistrierungsinhalten mit Clientbibliotheken'
description: Verwenden Sie diese Schnellstartanleitung, um Repositorys, Images und Artefakte mithilfe der Azure Container Registry-Clientbibliotheken zu verwalten.
author: dlepow
ms.topic: quickstart
ms.date: 10/05/2021
ms.author: danlep
ms.custom: ''
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 6af32e7bd841960cdf1fd3aef6af50171cc1e594
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007608"
---
# <a name="quickstart-use-the-azure-container-registry-client-libraries"></a>Schnellstart: Verwenden der Azure Container Registry-Clientbibliotheken

::: zone pivot="programming-language-csharp, programming-language-java, programming-language-javascript, programming-language-python"

Dieser Artikel hilft Ihnen beim Einstieg in die Verwendung der Clientbibliothek für Azure Container Registry. Führen Sie diese Schritte aus, um Beispielcode für Vorgänge auf Datenebene für Images und Artefakte auszuprobieren.

Verwenden Sie die Clientbibliothek für Azure Container Registry für Folgendes:

* Auflisten von Images oder Artefakten in einer Registrierung
* Abrufen von Metadaten für Images und Artefakte, Repositorys und Tags
* Festlegen von Lese-/Schreib-/Löscheigenschaften für Registrierungselemente
* Löschen von Images und Artefakten, Repositorys und Tags

Azure Container Registry verfügt auch über eine Verwaltungsbibliothek für Vorgänge auf Steuerungsebene, einschließlich Registrierungserstellung und Updates. 

## <a name="prerequisites"></a>Voraussetzungen

* Sie benötigen ein [Azure-Abonnement](https://azure.microsoft.com/free/) und eine Azure-Containerregistrierung, um diese Bibliothek verwenden zu können.

    Zum Erstellen einer neuen Azure-Containerregistrierung können Sie das [Azure-Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md) oder die [Azure CLI](container-registry-get-started-azure-cli.md) verwenden. Beispiel für die Verwendung der Azure-Befehlszeilenschnittstelle:

    ```azurecli
    az acr create --name MyContainerRegistry --resource-group MyResourceGroup \
        --location westus --sku Basic
    ```

* Pushen Sie Containerimages in Ihre Registrierung. Informationen zu den erforderlichen Schritten finden Sie unter [Pushen des ersten Images in eine Azure-Containerregistrierung mit der Docker CLI](container-registry-get-started-docker-cli.md).

## <a name="key-concepts"></a>Wichtige Begriffe

* Eine Azure-Containerregistrierung speichert *Containerimages* und [OCI-Artefakte](container-registry-oci-artifacts.md). 
* Ein Image oder Artefakt besteht aus einem *Manifest* und *Ebenen*. 
* Ein Manifest beschreibt die Ebenen, aus denen sich das Image oder Artefakt zusammensetzt. Es wird anhand seines *Digest* eindeutig identifiziert. 
* Ein Image oder Artefakt kann auch *markiert* werden, um es mit einem für Benutzer lesbaren Alias zu versehen. Einem Image oder Artefakt können null oder mehr Tags zugeordnet sein, und jedes Tag identifiziert das Image eindeutig. 
* Eine Sammlung mit Images oder Artefakten, die den gleichen Namen, aber unterschiedliche Tags haben, ist ein *Repository*.

Weitere Informationen finden Sie unter [Informationen zu Registrierungen, Repositorys und Artefakten](container-registry-concepts.md).

::: zone-end

::: zone pivot="programming-language-csharp"

## <a name="get-started"></a>Erste Schritte

[Quellcode][dotnet_source] | [Paket (NuGet)][dotnet_package] | [API-Referenz][dotnet_docs] | [Beispiele][dotnet_samples]

Zum Entwickeln von .NET-Anwendungscode, der eine Verbindung mit einer Azure Container Registry-Instanz herstellen kann, benötigen Sie die Bibliothek `Azure.Containers.ContainerRegistry`.

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie die Azure Container Registry-Clientbibliothek für .NET mit [NuGet][nuget]:

```Powershell
dotnet add package Azure.Containers.ContainerRegistry --prerelease
```

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Damit Ihre Anwendung eine Verbindung mit der Registrierung herstellen kann, müssen Sie ein für die Authentifizierung geeignetes `ContainerRegistryClient`-Element erstellen. Verwenden Sie die [Azure-Identitätsbibliothek][dotnet_identity], um Azure Active Directory-Unterstützung für die Authentifizierung von Azure SDK-Clients mit den entsprechenden Azure-Diensten hinzuzufügen.  

Wenn Sie Ihre Anwendung lokal entwickeln und debuggen, können Sie Ihren eigenen Benutzer verwenden, um sich bei der Registrierung zu authentifizieren. Dazu können Sie [den Benutzer mit der Azure CLI authentifizieren](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity#authenticating-via-the-azure-cli) und Ihre Anwendung über diese Anwendung ausführen. Wenn Ihre Anwendung einen Client verwendet, der für die Authentifizierung mit `DefaultAzureCredential` erstellt wurde, wird die Authentifizierung bei der Registrierung am angegebenen Endpunkt ordnungsgemäß ausgeführt.  

```C#
// Create a ContainerRegistryClient that will authenticate to your registry through Azure Active Directory
Uri endpoint = new Uri("https://myregistry.azurecr.io");
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });
```

In der [INFODATEI zu Azure-Identitäten][dotnet_identity] finden Sie weitere Ansätze für die Authentifizierung mit `DefaultAzureCredential`, die sowohl lokal als auch in Bereitstellungsumgebungen genutzt werden können. Informationen zum Herstellen einer Verbindung mit Registrierungen in nicht öffentlichen Azure-Clouds finden Sie in der [API-Referenz][dotnet_docs].

Weitere Informationen zur Verwendung von Azure AD mit Azure Container Registry finden Sie in der [Übersicht über die Authentifizierung](container-registry-authentication.md).

## <a name="examples"></a>Beispiele

In jedem Beispiel wird davon ausgegangen, dass die Umgebungsvariable `REGISTRY_ENDPOINT` auf eine Zeichenfolge festgelegt ist, die das Präfix `https://` und den Namen des Anmeldeservers enthält, z. B. https://myregistry.azurecr.io.

In den folgenden Beispielen werden asynchrone APIs verwendet, die eine Aufgabe zurückgeben. Synchrone APIs sind ebenfalls verfügbar.

### <a name="list-repositories-asynchronously"></a>Asynchrones Auflisten von Repositorys

Durchlaufen Sie die Sammlung von Repositorys in der Registrierung:

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="set-artifact-properties-asynchronously"></a>Asynchrones Festlegen von Artefakteigenschaften

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="delete-images-asynchronously"></a>Asynchrones Löschen von Images

```C# Snippet:ContainerRegistry_Tests_Samples_DeleteImageAsync
using System.Linq;
using Azure.Containers.ContainerRegistry;
using Azure.Identity;

// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Iterate through repositories
AsyncPageable<string> repositoryNames = client.GetRepositoryNamesAsync();
await foreach (string repositoryName in repositoryNames)
{
    ContainerRepository repository = client.GetRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    AsyncPageable<ArtifactManifestProperties> imageManifests =
        repository.GetManifestPropertiesCollectionAsync(orderBy: ArtifactManifestOrderBy.LastUpdatedOnDescending);

    // Delete images older than the first three.
    await foreach (ArtifactManifestProperties imageManifest in imageManifests.Skip(3))
    {
        RegistryArtifact image = repository.GetArtifact(imageManifest.Digest);
        Console.WriteLine($"Deleting image with digest {imageManifest.Digest}.");
        Console.WriteLine($"   Deleting the following tags from the image: ");
        foreach (var tagName in imageManifest.Tags)
        {
            Console.WriteLine($"        {imageManifest.RepositoryName}:{tagName}");
            await image.DeleteTagAsync(tagName);
        }
        await image.DeleteAsync();
    }
}
```

::: zone-end

::: zone pivot="programming-language-java"

## <a name="get-started"></a>Erste Schritte

[Quellcode][java_source] | [Paket (Maven)][java_package] | [API-Referenz][java_docs] | [Beispiele][java_samples]

### <a name="currently-supported-environments"></a>Die derzeitig unterstützten Umgebungen

* [Java Development Kit (JDK)][jdk_link], Version 8 oder höher

### <a name="include-the-package"></a>Einschließen des Pakets

[//]: # ({x-version-update-start;com.azure:azure-containers-containerregistry;current})
```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-containers-containerregistry</artifactId>
  <version>1.0.0-beta.3</version>
</dependency>
```
[//]: # ({x-version-update-end})

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Die [Azure-Identitätsbibliothek][java_identity] bietet Azure Active Directory-Unterstützung für die Authentifizierung.

In den folgenden Beispielen wird davon ausgegangen, dass Sie über eine Registrierungsendpunkt-Zeichenfolge verfügen, die das Präfix `https://` und den Namen des Anmeldeservers enthält, z. B. „https://myregistry.azurecr.io“.

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L31-L35 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();
```

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L39-L43 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryAsyncClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildAsyncClient();
```

Weitere Informationen zur Verwendung von Azure AD mit Azure Container Registry finden Sie in der [Übersicht über die Authentifizierung](container-registry-authentication.md).

## <a name="examples"></a>Beispiele

In jedem Beispiel wird davon ausgegangen, dass Sie über eine Registrierungsendpunkt-Zeichenfolge verfügen, die das Präfix `https://` und den Namen des Anmeldeservers enthält, z. B. „https://myregistry.azurecr.io“.

### <a name="list-repository-names"></a>Auflisten der Repositorynamen

Durchlaufen Sie die Sammlung von Repositorys in der Registrierung:

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L47-L53 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();

client.listRepositoryNames().forEach(repository -> System.out.println(repository));
```

### <a name="set-artifact-properties"></a>Festlegen von Artefakteigenschaften

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L119-L132 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

RegistryArtifact image = client.getArtifact(repositoryName, digest);

image.updateTagProperties(
    tag,
    new ArtifactTagProperties()
        .setWriteEnabled(false)
        .setDeleteEnabled(false));
```

### <a name="delete-images"></a>Löschen von Images

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L85-L113 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

final int imagesCountToKeep = 3;
for (String repositoryName : client.listRepositoryNames()) {
    final ContainerRepository repository = client.getRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    PagedIterable<ArtifactManifestProperties> imageManifests =
        repository.listManifestProperties(
            ArtifactManifestOrderBy.LAST_UPDATED_ON_DESCENDING,
            Context.NONE);

    imageManifests.stream().skip(imagesCountToKeep)
        .forEach(imageManifest -> {
            System.out.printf(String.format("Deleting image with digest %s.%n", imageManifest.getDigest()));
            System.out.printf("    This image has the following tags: ");

            for (String tagName : imageManifest.getTags()) {
                System.out.printf("        %s:%s", imageManifest.getRepositoryName(), tagName);
            }

            repository.getArtifact(imageManifest.getDigest()).delete();
        });
}
```

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="get-started"></a>Erste Schritte

[Quellcode][javascript_source] | [Paket (npm)][javascript_package] | [API-Referenz][javascript_docs] | [Beispiele][javascript_samples]

### <a name="currently-supported-environments"></a>Die derzeitig unterstützten Umgebungen

- [LTS-Versionen von Node.js](https://nodejs.org/about/releases/)

Ausführlichere Informationen finden Sie in der [Supportrichtlinie](https://github.com/Azure/azure-sdk-for-js/blob/main/SUPPORT.md).

### <a name="install-the-azurecontainer-registry-package"></a>Installieren Sie das Paket `@azure/container-registry`.

Installieren Sie die Container Registry-Clientbibliothek für JavaScript mit `npm`:

```bash
npm install @azure/container-registry
```

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Die [Azure-Identitätsbibliothek][javascript_identity] bietet Azure Active Directory-Unterstützung für die Authentifizierung.

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT;
// Create a ContainerRegistryClient that will authenticate through Active Directory
const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
```

Weitere Informationen zur Verwendung von Azure AD mit Azure Container Registry finden Sie in der [Übersicht über die Authentifizierung](container-registry-authentication.md).

## <a name="examples"></a>Beispiele

In jedem Beispiel wird davon ausgegangen, dass die Umgebungsvariable `CONTAINER_REGISTRY_ENDPOINT` auf eine Zeichenfolge festgelegt ist, die das Präfix `https://` und den Namen des Anmeldeservers enthält, z. B. https://myregistry.azurecr.io.

### <a name="list-repositories-asynchronously"></a>Asynchrones Auflisten von Repositorys

Durchlaufen Sie die Sammlung von Repositorys in der Registrierung:

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // endpoint should be in the form of "https://myregistryname.azurecr.io"
  // where "myregistryname" is the actual name of your registry
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

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

### <a name="set-artifact-properties-asynchronously"></a>Asynchrones Festlegen von Artefakteigenschaften

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";

  // Create a new ContainerRegistryClient and RegistryArtifact to access image operations
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
  const image = client.getArtifact("library/hello-world", "v1");

  // Set permissions on the image's "latest" tag
  await image.updateTagProperties("latest", { canWrite: false, canDelete: false });
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="delete-images-asynchronously"></a>Asynchrones Löschen von Images

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  // Create a new ContainerRegistryClient
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  // Iterate through repositories
  const repositoryNames = client.listRepositoryNames();
  for await (const repositoryName of repositoryNames) {
    const repository = client.getRepository(repositoryName);
    // Obtain the images ordered from newest to oldest by passing the `orderBy` option
    const imageManifests = repository.listManifestProperties({
      orderBy: "LastUpdatedOnDescending"
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

::: zone-end

::: zone pivot="programming-language-python"

## <a name="get-started"></a>Erste Schritte

[Quellcode][python_source] | [Paket (Pypi)][python_package] | [API-Referenz][python_docs] | [Beispiele][python_samples]

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie die Azure Container Registry-Clientbibliothek für Python mit [pip][pip_link]:

```bash
pip install --pre azure-containerregistry
```

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Die [Azure-Identitätsbibliothek][python_identity] bietet Azure Active Directory-Unterstützung für die Authentifizierung. `DefaultAzureCredential` geht davon aus, dass die Umgebungsvariablen `AZURE_CLIENT_ID`, `AZURE_TENANT_ID` und `AZURE_CLIENT_SECRET` festgelegt sind. Weitere Informationen finden Sie unter [Umgebungsvariablen für Azure-Identitäten](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#environment-variables).

```python
# Create a ContainerRegistryClient that will authenticate through Active Directory
from azure.containerregistry import ContainerRegistryClient
from azure.identity import DefaultAzureCredential

account_url = "https://mycontainerregistry.azurecr.io"
client = ContainerRegistryClient(account_url, DefaultAzureCredential())
```

## <a name="examples"></a>Beispiele

In jedem Beispiel wird davon ausgegangen, dass die Umgebungsvariable `CONTAINERREGISTRY_ENDPOINT` auf eine Zeichenfolge festgelegt ist, die das Präfix `https://` und den Namen des Anmeldeservers enthält, z. B. https://myregistry.azurecr.io.

### <a name="list-tags-asynchronously"></a>Asynchrones Auflisten von Tags

In diesem Beispiel wird davon ausgegangen, dass die Registrierung über das Repository `hello-world` verfügt.

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class ListTagsAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def list_tags(self):
        # Create a new ContainerRegistryClient      
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        manifest = await client.get_manifest_properties("library/hello-world", "latest")
        print(manifest.repository_name + ": ")
        for tag in manifest.tags:
            print(tag + "\n")
```

### <a name="set-artifact-properties-asynchronously"></a>Asynchrones Festlegen von Artefakteigenschaften

In diesem Beispiel wird davon ausgegangen, dass die Registrierung über das Repository `hello-world` mit dem Tag `v1` verfügt.

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class SetImagePropertiesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def set_image_properties(self):
        # Create a new ContainerRegistryClient
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        audience = "https://management.azure.com"
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        # [START update_manifest_properties]
        # Set permissions on the v1 image's "latest" tag
        await client.update_manifest_properties(
            "library/hello-world",
            "latest",
            can_write=False,
            can_delete=False
        )
        # [END update_manifest_properties]
        # After this update, if someone were to push an update to "myacr.azurecr.io\hello-world:v1", it would fail.
        # It's worth noting that if this image also had another tag, such as "latest", and that tag did not have
        # permissions set to prevent reads or deletes, the image could still be overwritten. For example,
        # if someone were to push an update to "myacr.azurecr.io\hello-world:latest"
        # (which references the same image), it would succeed.
```

### <a name="delete-images-asynchronously"></a>Asynchrones Löschen von Images

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry import ManifestOrder
from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class DeleteImagesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def delete_images(self):
        # [START list_repository_names]   
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        async with client:
            async for repository in client.list_repository_names():
                print(repository)
                # [END list_repository_names]

                # [START list_manifest_properties]
                # Keep the three most recent images, delete everything else
                manifest_count = 0
                async for manifest in client.list_manifest_properties(repository, order_by=ManifestOrder.LAST_UPDATE_TIME_DESCENDING):
                    manifest_count += 1
                    if manifest_count > 3:
                        await client.delete_manifest(repository, manifest.digest)
                # [END list_manifest_properties]
```

::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Azure-Containerregistrierung bereinigen und entfernen möchten, können Sie die Ressource oder Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](container-registry-get-started-portal.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](container-registry-get-started-azure-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie die Azure Container Registry-Clientbibliothek verwenden, um Vorgänge für Images und Artefakte in Ihrer Containerregistrierung auszuführen.

* Weitere Informationen finden Sie in der API-Referenzdokumentation:

    * [.NET][dotnet_docs]
    * [Java][java_docs]
    * [JavaScript][javascript_docs]
    * [Python][python_docs]

* Informieren Sie sich über die Azure Container Registry-[REST-API][rest_docs].

[dotnet_source]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/src
[dotnet_package]: https://www.nuget.org/packages/Azure.Containers.ContainerRegistry/
[dotnet_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/samples
[dotnet_docs]: /dotnet/api/azure.containers.containerregistry
[rest_docs]: /rest/api/containerregistry/
[product_docs]:  container-registry-intro.md
[nuget]: https://www.nuget.org/
[dotnet_identity]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity/README.md
[javascript_identity]: https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md
[javascript_source]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/src
[javascript_package]: https://www.npmjs.com/package/@azure/container-registry
[javascript_docs]: /javascript/api/overview/azure/container-registry-readme
[jdk_link]: /java/azure/jdk/
[javascript_samples]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/samples
[java_source]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/containerregistry/azure-containers-containerregistry/src
[java_package]: https://search.maven.org/artifact/com.azure/azure-containers-containerregistry
[java_docs]: /java/api/overview/azure/containers-containerregistry-readme
[java_identity]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/identity/azure-identity/README.md
[java_samples]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/containerregistry/azure-containers-containerregistry/src/samples/
[python_package]: https://pypi.org/project/azure-containerregistry/
[python_docs]: /python/api/overview/azure/containerregistry-readme
[python_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry/samples
[pip_link]: https://pypi.org
[python_identity]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity
[python_source]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry
