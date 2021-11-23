---
title: Leitfaden zur Entwicklung von Azure Functions
description: Lernen Sie die Konzepte und Techniken der Azure Functions kennen, die Sie benötigen, um alle Programmiersprachen und Bindungen übergreifend Funktionen in Azure zu entwickeln.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 9/02/2021
ms.openlocfilehash: 7aef7301207772711bcce7fbec4bde8937c94cf1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523446"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions: Entwicklerhandbuch
In Azure Functions nutzen bestimmte Funktionen einige wichtige technische Konzepte und Komponenten gemeinsam, unabhängig von der verwendeten Sprache oder Bindung. Bevor Sie sich mit den spezifischen Details einer bestimmten Sprache oder Bindung beschäftigen, sollten Sie diese Übersicht lesen, die für alle Funktionen gilt.

Dieser Artikel setzt voraus, dass Sie die [Einführung in Azure Functions](functions-overview.md) bereits gelesen haben.

## <a name="function-code"></a>Funktionscode
Ein *Funktion* ist das primäre Konzept in Azure Functions. Eine Funktion enthält zwei wichtige Komponenten: Ihren Code, der in einer Vielzahl von Programmiersprachen geschrieben sein kann, und die Datei „function.json“ mit Konfigurationsinformationen. Für kompilierte Sprachen wird diese Konfigurationsdatei automatisch aus Anmerkungen im Code generiert. Für Skriptsprachen müssen Sie die Konfigurationsdatei selbst bereitstellen.

Die Datei „function.json“ definiert den Trigger, die Bindungen und weitere Konfigurationseinstellungen der Funktion. Jede Funktion verfügt nur über einen einzigen Trigger. Anhand dieser Konfigurationsdatei ermittelt die Runtime, welche Ereignisse überwacht werden sollen und wie Daten in die Funktionsausführung übergeben und aus dieser zurückgegeben werden. Im Folgenden finden Sie eine function.json-Beispieldatei.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Weitere Informationen finden Sie unter [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md).

In der Eigenschaft `bindings` werden sowohl Trigger als auch Bindungen konfiguriert. Einige Einstellungen gelten für alle Bindungen, einige Einstellungen sind spezifisch für einen bestimmten Bindungstyp. Jede Bindung erfordert folgende Einstellungen:

| Eigenschaft    | Werte | Typ | Kommentare|
|---|---|---|---|
| type  | Der Name der Bindung.<br><br>Beispiel: `queueTrigger`. | Zeichenfolge | |
| direction | `in`, `out`  | Zeichenfolge | Gibt an, ob die Bindung zum Empfangen von Daten in der Funktion oder zum Senden von Daten aus der Funktion dient. |
| name | Der Funktionsbezeichner.<br><br>Beispiel: `myQueue`. | Zeichenfolge | Der Name, der für die gebundenen Daten in der Funktion verwendet wird. In C# ist dies ein Argumentname, in JavaScript ist es der Schlüssel in einer Schlüssel-Wert-Liste. |

## <a name="function-app"></a>Funktionen-App
Eine Funktions-App bietet einen Ausführungskontext in Azure, in dem Ihre Funktionen ausgeführt werden. Daher ist dies die Bereitstellungs- und Verwaltungseinheit für Ihre Funktionen. Eine Funktions-App besteht aus einer oder mehreren individuellen Funktionen, die zusammen verwaltet, bereitgestellt und skaliert werden. Der Tarif, die Bereitstellungsmethode und die Runtimeversion sind für alle Funktionen in einer Funktions-App gleich. Eine Funktionen-App ist somit eine Möglichkeit, mit der Sie Ihre Funktionen organisieren und kollektiv verwalten können. Weitere Informationen finden Sie unter [Verwalten einer Funktions-App](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Alle Funktionen in einer Funktions-App müssen in der gleichen Programmiersprache erstellt werden. In [Vorgängerversionen](functions-versions.md) der Azure Functions-Runtime war dies nicht erforderlich.

## <a name="folder-structure"></a>Ordnerstruktur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Oben sehen Sie die standardmäßige (und empfohlene) Ordnerstruktur für eine Funktions-App. Falls Sie den Dateispeicherort des Codes einer Funktion ändern möchten, ändern Sie den Abschnitt `scriptFile` der Datei _function.json_. Außerdem empfehlen wir, das Projekt mithilfe der [Paketbereitstellung](deployment-zip-push.md) in Ihrer Funktions-App in Azure bereitzustellen. Sie können auch vorhandene Tools wie [Continuous Integration und Continuous Deployment](functions-continuous-deployment.md) und Azure DevOps verwenden.

> [!NOTE]
> Wenn Sie ein Paket manuell bereitstellen, müssen die Datei _host.json_ und die Funktionsordner direkt im Ordner `wwwroot` bereitgestellt werden. Schließen Sie den Ordner `wwwroot` nicht in Ihre Bereitstellungen ein. Andernfalls erhalten Sie `wwwroot\wwwroot`-Ordner.

#### <a name="use-local-tools-and-publishing"></a>Verwenden von lokalen Tools und Veröffentlichung
Funktions-Apps können mit verschiedenen Tools erstellt und veröffentlicht werden, darunter [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) und [Azure Functions Core Tools](./functions-develop-local.md). Weitere Informationen finden Sie unter [Lokales Codieren und Testen von Azure Functions](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Bearbeiten von Funktionen im Azure-Portal
Mit dem integrierten Functions-Editor im Azure-Portal können Sie Ihren Code und die Datei *function.json* direkt inline aktualisieren. Dies wird nur für kleine Änderungen oder Proof of Concept-Projekte empfohlen. Die bewährte Methode ist die Verwendung eines lokalen Entwicklungstools wie Visual Studio Code.

## <a name="parallel-execution"></a>Parallele Ausführung
Wenn die Auslösung mehrerer Ereignisse schneller erfolgt als die Runtime einer Singlethreadfunktion sie verarbeiten kann, kann die Runtime die Funktion mehrmals parallel aufrufen.  Wenn eine Funktionen-App den [verbrauchsbasierten Hostingplan](event-driven-scaling.md) verwendet, kann die App automatisch aufskaliert werden.  Jede Instanz der Funktionen-App – unabhängig davon, ob die App im verbrauchsbasierten Hostingplan oder einem regulären [App Service-Hostingplan](../app-service/overview-hosting-plans.md) ausgeführt wird – kann gleichzeitige Funktionsaufrufe über mehrere Threads parallel verarbeiten.  Die maximale Anzahl gleichzeitiger Funktionsaufrufe in jeder Funktionen-App-Instanz variiert je nach Art des verwendeten Triggers sowie je nach den Ressourcen, die von anderen Funktionen innerhalb der Funktionen-App verwendet werden.

## <a name="functions-runtime-versioning"></a>Versionsverwaltung der Functions-Runtime

Sie können die Version der Functions-Runtime mit der App-Einstellung `FUNCTIONS_EXTENSION_VERSION` konfigurieren. Der Wert „~3“ bedeutet beispielsweise, dass für Ihre Funktions-App „3.x“ als Hauptversion verwendet wird. Funktions-Apps werden auf jede neue Nebenversion aktualisiert, wenn sie freigegeben werden. Weitere Informationen finden Sie unter [Einstellen von Runtimeversionen von Azure Functions als Ziel](set-runtime-version.md), einschließlich der Informationen zum Anzeigen der genauen Version Ihrer Funktions-App.

## <a name="repositories"></a>Repositorys
Der Code für Azure Functions ist Open Source und in GitHub-Repositorys gespeichert:

* [Azure-Funktionen](https://github.com/Azure/Azure-Functions)
* [Azure Functions-Host](https://github.com/Azure/azure-functions-host/)
* [Azure Functions-Portal](https://github.com/azure/azure-functions-ux)
* [Azure Functions-Vorlagen](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-Erweiterungen](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindungen
Hier finden Sie eine Tabelle aller unterstützten Bindungen.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Haben Sie Probleme mit Fehlern, die aus den Bindungen stammen? Lesen Sie die Dokumentation [Fehlerbehandlung in Azure Functions](functions-bindings-error-pages.md).


## <a name="connections"></a>Verbindungen

Ihr Funktionsprojekt verweist anhand des Namens auf Verbindungsinformationen aus seinem Konfigurationsanbieter. Die Verbindungsdetails werden nicht direkt akzeptiert, sodass sie in verschiedenen Umgebungen geändert werden können. Eine Triggerdefinition kann z. B. eine `connection`-Eigenschaft enthalten. Dies kann sich auf eine Verbindungszeichenfolge beziehen, aber Sie können die Verbindungszeichenfolge nicht direkt in einer Datei `function.json` festlegen. Stattdessen würden Sie `connection` auf den Namen einer Umgebungsvariablen festlegen, die die Verbindungszeichenfolge enthält.

Der Standardkonfigurationsanbieter verwendet Umgebungsvariablen. Diese werden möglicherweise von [Anwendungseinstellungen](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) festgelegt, wenn Sie im Azure Functions-Dienst ausgeführt werden, oder aus der [lokalen Einstellungsdatei](functions-develop-local.md#local-settings-file) bei der lokalen Entwicklung.

### <a name="connection-values"></a>Verbindungswerte

Wenn der Verbindungsname in einen einzelnen exakten Wert aufgelöst wird, identifiziert die Laufzeit den Wert als _Verbindungszeichenfolge_, die in der Regel ein Geheimnis enthält. Die Details einer Verbindungszeichenfolge werden durch den Dienst definiert, mit dem Sie eine Verbindung herstellen möchten.

Ein Verbindungsname kann jedoch auch auf eine Sammlung von mehreren Konfigurationselementen verweisen, die für das Konfigurieren [identitätsbasierter Verbindungen](#configure-an-identity-based-connection) nützlich ist. Umgebungsvariablen können als Sammlung behandelt werden, indem ein gemeinsames Präfix verwendet wird, das auf doppelte Unterstriche (`__`) endet. Auf die Gruppe kann dann verwiesen werden, indem der Verbindungsname auf dieses Präfix festgelegt wird.

Beispielsweise kann die `connection`-Eigenschaft für eine Azure-Blobtriggerdefinition „Storage1“ lauten. Solange kein einzelner Zeichenfolgenwert von einer Umgebungsvariablen namens „Storage1“ konfiguriert wurde, kann die `blobServiceUri`-Eigenschaft durch eine Umgebungsvariable namens `Storage1__blobServiceUri` über die Verbindung informiert werden. Die Verbindungseigenschaften unterscheiden sich für jeden Dienst. In der Dokumentation finden Sie Informationen zu der Komponente, die die Verbindung verwendet.

### <a name="configure-an-identity-based-connection"></a>Konfigurieren einer identitätsbasierten Verbindung

Einige Verbindungen in Azure Functions können so konfiguriert werden, dass anstelle eines Geheimnisses eine Identität verwendet wird. Die Unterstützung hängt von der Erweiterung ab, die die Verbindung verwendet. In einigen Fällen ist möglicherweise weiterhin eine Verbindungszeichenfolge in Functions erforderlich, obwohl der Dienst, mit dem Sie eine Verbindung herstellen, identitätsbasierte Verbindungen unterstützt. Ein Tutorial zum Konfigurieren Ihrer Funktions-Apps mit verwalteten Identitäten finden Sie im [Tutorial zum Erstellen einer Funktions-App mit identitätsbasierten Verbindungen](./functions-identity-based-connections-tutorial.md).

Identitätsbasierte Verbindungen werden von den folgenden Komponenten unterstützt:

| Verbindungsquelle                                       | Unterstützte Pläne | Weitere Informationen                                                                                                         |
|---------------------------------------------------------|-----------------|--------------------------------------------------------------------------------------------------------------------|
| Azure Blob Auslöser und Bindungen               | All             | [Erweiterung Version 5.0.0 oder höher](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)     |
| Azure Queue Auslöser und Bindungen            | Alle             | [Erweiterung Version 5.0.0 oder höher](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)    |
| Azure Event Hubs Auslöser und Bindungen     | Alle             | [Erweiterung Version 5.0.0 oder höher](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)    |
| Azure Service Bus Auslöser und Bindungen       | Alle             | [Erweiterung Version 5.0.0 oder höher](./functions-bindings-service-bus.md#service-bus-extension-5x-and-higher)  |
| Trigger und Bindungen bei Azure Cosmos DB – Vorschau         | Elastic Premium | [Erweiterungsversion 4.0.0-preview1 oder höher](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) |
| Vom Host benötigter Speicher („AzureWebJobsStorage“) – Vorschau | All             | [Verbinden zum Hostspeicher mit einer Identität](#connecting-to-host-storage-with-an-identity-preview)                        |

> [!NOTE]
> Identitätsbasierte Verbindungen werden mit Durable Functions nicht unterstützt.

[!INCLUDE [functions-identity-based-connections-configuration](../../includes/functions-identity-based-connections-configuration.md)]

Wählen Sie unten eine Registerkarte aus, um mehr über Berechtigungen für die einzelnen Komponenten zu erfahren:

# <a name="azure-blobs-extension"></a>[Azure Blobs-Erweiterung](#tab/blob)

[!INCLUDE [functions-blob-permissions](../../includes/functions-blob-permissions.md)]

# <a name="azure-queues-extension"></a>[Azure Queues-Erweiterung](#tab/queue)

[!INCLUDE [functions-queue-permissions](../../includes/functions-queue-permissions.md)]

# <a name="event-hubs-extension"></a>[Event Hubs-Erweiterung](#tab/eventhubs)

[!INCLUDE [functions-event-hubs-permissions](../../includes/functions-event-hubs-permissions.md)]

# <a name="service-bus-extension"></a>[Service Bus-Erweiterung](#tab/servicebus)

[!INCLUDE [functions-service-bus-permissions](../../includes/functions-service-bus-permissions.md)]

# <a name="azure-cosmos-db-extension-preview"></a>[Azure Cosmos DB Erweiterung (Vorschau)](#tab/cosmos)

[!INCLUDE [functions-cosmos-permissions](../../includes/functions-cosmos-permissions.md)]

# <a name="functions-host-storage-preview"></a>[Funktionen Hostspeicher (Vorschau)](#tab/azurewebjobsstorage)

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

---

#### <a name="common-properties-for-identity-based-connections"></a>Allgemeine Eigenschaften für identitätsbasierte Verbindungen

Eine identitätsbasierte Verbindung für einen Azure-Dienst akzeptiert die folgenden allgemeinen Eigenschaften, wobei `<CONNECTION_NAME_PREFIX>` der Wert Ihrer `connection`-Eigenschaft in der Trigger- oder Bindungsdefinition ist:

| Eigenschaft    |  Vorlage für Umgebungsvariable | BESCHREIBUNG |
|---|---|---|---|
| Token-Anmeldeinformationen |  `<CONNECTION_NAME_PREFIX>__credential` | Damit wird festgelegt, wie für die Verbindung ein Token abgerufen werden soll. Wird nur empfohlen, wenn eine vom Benutzer zugewiesene Identität angegeben wird. Dann muss sie auf „managedidentity“ festgelegt werden. Dies gilt nur, wenn sie im Azure Functions-Dienst gehostet wird. |
| Client-ID | `<CONNECTION_NAME_PREFIX>__clientId` | Wenn `credential` auf „managedidentity“ festgelegt ist, wird mit dieser Eigenschaft die vom Benutzer zugewiesene Identität angegeben, die beim Abrufen eines Tokens verwendet werden soll. Die Eigenschaft akzeptiert eine Client-ID, die einer vom Benutzer zugewiesenen Identität entspricht, die der Anwendung zugeordnet ist. Wenn nichts angegeben wird, wird die vom System zugewiesene Identität verwendet. Diese Eigenschaft wird in [Szenarios für die lokale Entwicklung](#local-development-with-identity-based-connections) anders verwendet, wenn `credential` nicht festgelegt werden darf. |

Für einen bestimmten Verbindungstyp können weitere Optionen unterstützt werden. Weitere Informationen finden Sie in der Dokumentation zu der Komponente, die die Verbindung herstellt.

##### <a name="local-development-with-identity-based-connections"></a>Lokale Entwicklung mit identitätsbasierten Verbindungen

> [!NOTE]
> Die lokale Entwicklung mit identitätsbasierten Verbindungen erfordert aktualisierte Versionen der [Azure Functions Core Tools](./functions-run-local.md). Sie können die derzeit installierte Version überprüfen, indem Sie `func -v` ausführen. Verwenden Sie für Functions v3 die Version `3.0.3904` oder höher. Verwenden Sie für Functions v4 die Version `4.0.3904` oder höher. 

Bei lokaler Ausführung weist die Konfiguration oben die Laufzeit an, Ihre lokale Entwickleridentität zu verwenden. Die Verbindung versucht, ein Token von den folgenden Speicherorten in der angegebenen Reihenfolge abzurufen:

- Lokaler Cache, der von Microsoft-Anwendungen gemeinsam genutzt wird
- Aktueller Benutzerkontext in Visual Studio
- Aktueller Benutzerkontext in Visual Studio Code
- Aktueller Benutzerkontext in der Azure CLI

Wenn keine dieser Optionen erfolgreich ist, tritt ein Fehler auf.

Da dabei die Entwickleridentität verwendet wird, verfügen Sie möglicherweise bereits über einige Rollen für Entwicklungsressourcen. Diese bieten jedoch möglicherweise keinen Datenzugriff. Verwaltungsrollen wie [Besitzer](../role-based-access-control/built-in-roles.md#owner) sind nicht ausreichend. Überprüfen Sie, welche Berechtigungen für Verbindungen für jede Komponente erforderlich sind, und stellen Sie sicher, dass sie Ihnen selbst zugewiesen sind.

In einigen Fällen möchten Sie möglicherweise die Verwendung einer anderen Identität angeben. Sie können Konfigurationseigenschaften für die Verbindung hinzufügen, die auf die alternative Identität basierend auf einer Client-ID und einem geheimen Clientschlüssel für ein Azure Active Directory-Dienstprinzipal verweisen. **Diese Konfigurationsoption wird nicht unterstützt, wenn das Hosting im Azure Functions-Dienst erfolgt.** Um eine ID und ein Geheimnis auf Ihrem lokalen Computer zu verwenden, definieren Sie die Verbindung mit den folgenden zusätzlichen Eigenschaften:

| Eigenschaft    | Vorlage für Umgebungsvariable | BESCHREIBUNG |
|---|---|---|
| Mandanten-ID | `<CONNECTION_NAME_PREFIX>__tenantId` | Die ID des Azure Active Directory-Mandanten (Verzeichnis). |
| Client-ID | `<CONNECTION_NAME_PREFIX>__clientId` |  Die Client-ID (Anwendungs-ID) einer App-Registrierung im Mandanten. |
| Geheimer Clientschlüssel | `<CONNECTION_NAME_PREFIX>__clientSecret` | Ein Clientgeheimnis, das für die App-Registrierung generiert wurde. |

Beispiel für `local.settings.json`-Eigenschaften, die für identitätsbasierte Verbindungen mit Azure Blob erforderlich sind: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__blobServiceUri": "<blobServiceUri>",
    "<CONNECTION_NAME_PREFIX>__queueServiceUri": "<queueServiceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="connecting-to-host-storage-with-an-identity-preview"></a>Verbinden mit dem Hostspeicher mit einer Identität (Vorschau)

Azure Functions verwendet standardmäßig die „AzureWebJobsStorage“-Verbindung für Kernfunktionalitäten wie die Koordination der Singletonausführung von Timertriggern und der standardmäßigen Speicherung von App-Schlüsseln. Sie kann auch so konfiguriert werden, dass eine Identität genutzt wird.

> [!CAUTION]
> Andere Komponenten in Functions basieren für Standardverhalten auf „AzureWebJobsStorage“. Sie sollten sie nicht in eine identitätsbasierte Verbindung verschieben, wenn Sie ältere Versionen von Erweiterungen verwenden, die diese Art von Verbindung nicht unterstützen, einschließlich Triggern und Bindungen für Azure-Blobs und Event Hubs. Ebenso wird `AzureWebJobsStorage` für Bereitstellungsartefakte verwendet, wenn der serverseitig integrierte Linux-Verbrauch verwendet wird. Wenn Sie diese Option aktivieren, müssen Sie die Bereitstellung über [ein externes Bereitstellungspaket](/run-functions-from-deployment-package) durchführen.
>
> Außerdem verwenden einige Apps „AzureWebJobsStorage“ für andere Speicherverbindungen in ihren Triggern, Bindungen und/oder in ihrem Funktionscode wieder. Stellen Sie sicher, dass alle Benutzer*innen von „AzureWebJobsStorage“ das identitätsbasierte Verbindungsformat verwenden können, bevor Sie diese Verbindung einer Verbindungszeichenfolge ändern.

Um eine identitätsbasierte Verbindung für „AzureWebJobsStorage“ zu verwenden, konfigurieren Sie die folgenden App-Einstellungen:

| Einstellung                       | BESCHREIBUNG                                | Beispielwert                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__blobServiceUri`| Der Datenebenen-URI des Blob-Diensts des Speicherkontos im HTTPS-Schema. | https://<Speicherkontoname>.blob.core.windows.net |
| `AzureWebJobsStorage__queueServiceUri` | Der Datenebenen-URI des Warteschlangendiensts des Speicherkontos im HTTPS-Schema. | https://<Speicherkontoname>.queue.core.windows.net |

[Allgemeine Eigenschaften für identitätsbasierte Verbindungen](#common-properties-for-identity-based-connections) können auch festgelegt werden.

Wenn Sie ein Speicherkonto verwenden, das global das standardmäßige DNS-Suffix und den standardmäßigen Dienstnamen für Azure verwendet, können Sie gemäß dem `https://<accountName>.blob/queue/file/table.core.windows.net`-Format stattdessen `AzureWebJobsStorage__accountName` mit dem Namen Ihres Speicherkontos festlegen. Die Blob- und Warteschlangenendpunkte werden für dieses Konto abgeleitet. Dies funktioniert nicht, wenn sich das Speicherkonto in einer Sovereign Cloud befindet oder über ein benutzerdefiniertes DNS verfügt.

| Einstellung                       | BESCHREIBUNG                                | Beispielwert                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__accountName` | Der Kontoname eines Speicherkontos, der nur gültig ist, wenn sich das Konto nicht in einer Sovereign Cloud befindet und nicht über ein benutzerdefiniertes DNS verfügt. | <Speicherkontoname> |

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

## <a name="reporting-issues"></a>Melden von Problemen
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)
* [Lokales Codieren und Testen von Azure Functions](./functions-develop-local.md)
* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [C#-Entwicklerreferenz zu Azure Functions](functions-dotnet-class-library.md)
* [Node.js-Entwicklerreferenz zu Azure Functions](functions-reference-node.md)
