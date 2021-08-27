---
title: Erstellen und Verwenden von Ressourcendateien
description: Erfahren Sie, wie Sie Batch-Ressourcendateien aus verschiedenen Eingabequellen erstellen. Dieser Artikel behandelt einige gängige Methoden, wie Ressourcendateien erstellt und auf einer VM platziert werden.
ms.date: 08/18/2021
ms.topic: how-to
ms.openlocfilehash: a4939cc6c60d226d8b75569ab08447973968735a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446569"
---
# <a name="creating-and-using-resource-files"></a>Erstellen und Verwenden von Ressourcendateien

Eine Azure Batch-Aufgabe erfordert oft eine Form von Daten zur Verarbeitung. Sie können diese Daten Ihrem virtuellen Batch-Computer über eine Aufgabe mithilfe von Ressourcendateien bereitstellen. Ressourcendateien werden von allen Arten von Aufgaben unterstützt: Aufgaben, Startaufgaben, Aufgaben für die Arbeitsvorbereitung, Aufgaben für die Freigabe von Aufträgen, etc. Dieser Artikel behandelt einige gängige Methoden, wie Ressourcendateien erstellt und auf einem virtuellen Computer platziert werden.  

Ressourcendateien übertragen Daten auf einen virtuellen Computer im Batch, aber die Art der Daten und wie sie verwendet werden, ist flexibel. Es gibt jedoch einige gängige Anwendungsfälle:

- Bereitstellung allgemeiner Dateien auf jeder VM mithilfe von Ressourcendateien in einem Starttask
- Bereitstellung von Eingabedaten für die Verarbeitung durch Tasks

Allgemeine Dateien können z.B. Dateien einer Startaufgabe sein, die zur Installation von Anwendungen verwendet werden, die Ihre Aufgabe ausführt. Die Eingabedaten können unbearbeitete Bild- oder Videodaten oder beliebige Informationen sein, die von Batch verarbeitet werden sollen.

## <a name="types-of-resource-files"></a>Ressourcendateitypen

Es gibt einige verschiedene Optionen zum Generieren von Ressourcendateien, wobei jeweils eine eigene [Methode](/dotnet/api/microsoft.azure.batch.resourcefile#methods) verwendet wird. Der Erstellungsprozess für Ressourcendateien variiert je nachdem, wo die ursprünglichen Daten gespeichert werden und ob mehrere Dateien erstellt werden sollen.

- [Speichercontainer-URL:](#storage-container-url) Diese generiert Ressourcendateien aus einem Speichercontainer in Azure.
- [Speichercontainername:](#storage-container-name-autostorage) Hiermit werden Ressourcendateien aus dem Namen eines Containers im Azure-Speicherkonto erstellt, das mit Ihrem Batch-Konto verknüpft ist (Autostorage-Konto).
- [Einzelne Ressourcendatei vom Webendpunkt:](#single-resource-file-from-web-endpoint) Hiermit wird eine einzelne Ressourcendatei aus einer beliebigen gültigen HTTP-URL generiert.

### <a name="storage-container-url"></a>Speichercontainer-URL

Die Verwendung einer Speichercontainer-URL bedeutet, dass Sie mit den richtigen Berechtigungen auf Dateien in jedem Speichercontainer in Azure zugreifen können.

In diesem C#-Beispiel wurden die Dateien bereits als Blobspeicher in einen Azure-Speichercontainer hochgeladen. Um auf die Daten zuzugreifen, die für die Erstellung einer Ressourcendatei benötigt werden, müssen wir zunächst Zugriff auf den Speichercontainer erhalten. Dies kann auf verschiedene Arten erfolgen.

#### <a name="shared-access-signature"></a>Shared Access Signature (SAS)

Erstellen Sie eine URI für Shared Access Signatures (SAS) mit den richtigen Berechtigungen für den Zugriff auf den Speichercontainer. Legen Sie die Ablaufzeit und die Berechtigungen für die SAS fest. In diesem Fall wird keine Startzeit angegeben, sodass die SAS sofort gültig wird und zwei Stunden nach ihrer Generierung abläuft.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Für den Containerzugriff müssen Sie sowohl `Read`- als auch `List`-Berechtigungen haben, wohingegen Sie beim Blobzugriff nur `Read`-Berechtigungen benötigen.

Nachdem die Berechtigungen konfiguriert sind, erstellen Sie das SAS-Token und formatieren Sie die SAS-URL für den Zugriff auf den Speichercontainer. Generieren Sie unter Verwendung der formatierten SAS-URL für den Speichercontainer eine Ressourcendatei mit [FromStorageContainerUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Sie können bei Bedarf die Eigenschaft [blobPrefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) verwenden, um Downloads auf die Blobs zu beschränken, deren Name mit einem angegebenen Präfix beginnt:

```csharp
ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl, blobPrefix = yourPrefix);
```

#### <a name="managed-identity"></a>Verwaltete Identität

Erstellen Sie eine [benutzerseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity), und weisen Sie ihr die `Storage Blob Data Reader`-Rolle für Ihren Azure Storage-Container zu. Als Nächstes [weisen Sie die verwaltete Identität Ihrem Pool zu](managed-identity-pools.md), damit Ihre VMs auf die Identität zugreifen können. Schließlich können Sie auf die Dateien in Ihrem Container zugreifen, indem Sie die Identität angeben, die von Azure Batch verwendet werden soll.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(container.Uri, identityReference: new ComputeNodeIdentityReference() { ResourceId = "/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name" });
```

#### <a name="public-access"></a>Öffentlicher Zugriff

Eine Alternative zum Generieren einer SAS-URL oder zur Verwendung einer verwalteten Identität ist die Aktivierung eines anonymen öffentlichen Lesezugriffs auf einen Container und dessen Blobs in Azure Blob Storage. Auf diese Weise können Sie schreibgeschützten Zugriff auf diese Ressourcen gewähren, ohne Ihren Kontoschlüssel freizugeben und eine SAS zu erfordern. Öffentlicher Zugriff wird in der Regel für Szenarien verwendet, in denen bestimmte Blobs stets für anonymen Lesezugriff zur Verfügung stehen sollen. Wenn dieses Szenario zu Ihrer Lösung passt, lesen Sie [Konfigurieren des anonymen öffentlichen Lesezugriffs für Container und Blobs](../storage/blobs/anonymous-read-access-configure.md), um mehr über die Verwaltung des Zugriffs auf Ihre Blobdaten zu erfahren.

### <a name="storage-container-name-autostorage"></a>Speichercontainername (Autostorage)

Anstatt eine SAS-URL zu konfigurieren und zu erstellen, können Sie den Namen Ihres Azure-Speichercontainers verwenden, um auf Ihre Blobdaten zuzugreifen. Der verwendete Speichercontainer muss sich im Azure-Speicherkonto befinden, das mit Ihrem Batch-Konto verknüpft ist und manchmal als *Autostorage-Konto* bezeichnet wird. Die Verwendung des Autostorage-Containers ermöglicht es Ihnen, die Konfiguration und Erstellung einer SAS-URL für den Zugriff auf einen Speichercontainer zu umgehen. Geben Sie stattdessen den Namen des Speichercontainers in Ihrem verknüpften Speicherkonto an.

Wenn Sie noch nicht über ein Autostorage-Konto verfügen, sehen Sie sich die Schritte unter [Erstellen eines Batch-Kontos mit dem Azure-Portal](batch-account-create-portal.md) an, um Details zum Erstellen und Verknüpfen eines Speicherkontos zu erhalten.

Im folgenden Beispiel wird [AutoStorageContainer](/dotnet/api/microsoft.azure.batch.resourcefile.fromautostoragecontainer) verwendet, um die Datei aus Daten im Autostorage-Konto zu generieren.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

Wie bei einer Speichercontainer-URL können Sie die [blobPrefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix)-Eigenschaft verwenden, um anzugeben, welche Blobs heruntergeladen werden:

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName, blobPrefix = yourPrefix);
```

### <a name="single-resource-file-from-web-endpoint"></a>Einzelne Ressourcendatei vom Webendpunkt

Sie können eine gültige HTTP-URL angeben, die Ihre Eingabedaten enthält, um eine einzelne Ressourcendatei zu erstellen. Die URL wird der Batch API bereitgestellt, und dann werden die Daten verwendet, um eine Ressourcendatei zu erstellen. Diese Methode kann unabhängig davon verwendet werden, ob sich die Daten zum Erstellen Ihrer Ressourcendatei in Azure Storage oder an einem anderen Webspeicherort wie einem GitHub-Endpunkt befinden.

Im folgenden Beispiel wird [FromUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromurl) verwendet, um die Datei aus einer Zeichenfolge abzurufen, die eine gültige URL enthält. Dann wird eine Ressourcendatei generiert, die von Ihrer Aufgabe verwendet werden soll. Für dieses Szenario sind keine Anmeldeinformationen erforderlich. (Anmeldeinformationen sind erforderlich, wenn Blob Storage verwendet wird, es sei denn, der öffentliche Lesezugriff ist für den Blobcontainer aktiviert.)

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourURL, filePath);
```

Sie können auch eine Zeichenfolge verwenden, die Sie als URL definieren (oder eine Kombination aus Zeichenfolgen, die zusammen die vollständige URL für Ihre Datei ergeben).

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourDomain + yourFile, filePath);
```

Wenn sich Ihre Datei in Azure Storage befindet, können Sie eine verwaltete Identität verwenden, anstatt eine Shared Access Signature für die Ressourcendatei zu generieren.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourURLFromAzureStorage, 
    identityReference: new ComputeNodeIdentityReference() { ResourceId = "/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"},
    filePath: filepath
);
```

> [!Note]
> Die Authentifizierung der verwalteten Identität funktioniert nur mit Dateien in Azure Storage. Die verwaltete Identität benötigt die `Storage Blob Data Reader`-Rollenzuweisung für den Container, in dem sich die Datei befindet, und muss auch [dem Azure Batch-Pool zugewiesen werden](managed-identity-pools.md).

## <a name="tips-and-suggestions"></a>Tipps und Vorschläge

Azure Batch-Tasks können Dateien auf viele verschiedene Arten verwenden. Aus diesem Grund bietet Azure Batch verschiedene Optionen zum Verwalten von Dateien für Tasks. Die folgenden Szenarios sind nicht als vollständig zu verstehen, decken aber einige gängige Situationen ab und geben Empfehlungen.

### <a name="many-resource-files"></a>Viele Ressourcendateien

Wenn allgemeine Taskdateien von vielen Tasks in Ihrem Batch-Auftrag gemeinsam genutzt werden, sollten Sie ein [Anwendungspaket](batch-application-packages.md) verwenden, damit diese Dateien gemeinsam darin enthalten sind. Anwendungspakete ermöglichen eine Optimierung der Downloadgeschwindigkeit, und Daten in Anwendungspaketen werden zwischen den Tasks zwischengespeichert. Mit Anwendungspaketen müssen Sie nicht mehrere Ressourcendateien manuell verwalten oder SAS-URLs generieren, um auf die Dateien in Azure Storage zuzugreifen. Batch funktioniert im Hintergrund mit Azure Storage zum Speichern von Anwendungspaketen und zum Bereitstellen der Pakete auf Serverknoten. Wenn sich Ihre Taskdateien nicht häufig ändern, eignen sich möglicherweise Anwendungspakete für Ihre Lösung.

Wenn Ihre Tasks hingegen jeweils über viele, für den Task spezifische Dateien verfügen, sind wahrscheinlich Ressourcendateien die beste Option. Tasks, die eindeutige Dateien verwenden, müssen oft aktualisiert oder ersetzt werden, was mit den Anwendungspaketinhalten nicht so einfach ist. Ressourcendateien bieten zusätzliche Flexibilität beim Aktualisieren, Hinzufügen oder Bearbeiten einzelner Dateien.

### <a name="number-of-resource-files-per-task"></a>Anzahl von Ressourcendateien pro Aufgabe

Wenn ein Task mehrere hundert Ressourcendateien angibt, lehnt Azure Batch den Task möglicherweise ab, da er zu groß ist. Es ist am besten, Ihre Aufgaben klein zu halten, indem Sie die Anzahl der Ressourcendateien in der Aufgabe selbst minimieren.

Wenn es keine Möglichkeit gibt, die Anzahl der Dateien zu minimieren, die Ihre Aufgabe benötigt, können Sie die Aufgabe optimieren, indem Sie eine einzelne Ressourcendatei erstellen, die auf einen Speichercontainer mit Ressourcendateien verweist. Legen Sie hierzu Ihre Ressourcendateien in einem Azure Storage-Container ab, und verwenden Sie eine der oben beschriebenen Methoden, um je nach Bedarf Ressourcendateien zu generieren.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Anwendungspakete](batch-application-packages.md) als Alternative zu Ressourcendateien.
- Informieren Sie sich über das [Ausführen von Containeranwendungen in Azure Batch](batch-docker-container-workloads.md) für Ressourcendateien.
- Lesen Sie den Artikel [Persistente Aufträge und Aufgabenausgabe](batch-task-output.md).
- Informieren Sie sich über die [Batch-APIs und Tools](batch-apis-tools.md), die für die Erstellung von Batch-Lösungen verfügbar sind.
