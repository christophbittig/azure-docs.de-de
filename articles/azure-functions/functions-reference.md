---
title: Leitfaden zur Entwicklung von Azure Functions
description: Lernen Sie die Konzepte und Techniken der Azure Functions kennen, die Sie benötigen, um alle Programmiersprachen und Bindungen übergreifend Funktionen in Azure zu entwickeln.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 9/02/2021
ms.openlocfilehash: 49c6fc554eab18ec598db7ec21ef8c15b95d7be9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669643"
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

Ein Verbindungsname kann jedoch auch auf eine Sammlung mehrerer Konfigurationselemente verweisen. Umgebungsvariablen können als Sammlung behandelt werden, indem ein gemeinsames Präfix verwendet wird, das auf doppelte Unterstriche (`__`) endet. Auf die Gruppe kann dann verwiesen werden, indem der Verbindungsname auf dieses Präfix festgelegt wird.

Beispielsweise kann die `connection`-Eigenschaft für eine Azure-Blobtriggerdefinition `Storage1` lauten. Solange kein einzelner Zeichenfolgenwert mit `Storage1` als Name konfiguriert ist, wird `Storage1__serviceUri` für die `serviceUri`-Eigenschaft der Verbindung verwendet. Die Verbindungseigenschaften unterscheiden sich für jeden Dienst. Weitere Informationen finden Sie in der Dokumentation für die Erweiterung, die die Verbindung verwendet.

### <a name="configure-an-identity-based-connection"></a>Konfigurieren einer identitätsbasierten Verbindung

Einige Verbindungen in Azure Functions werden so konfiguriert, dass anstelle eines Geheimnisses eine Identität verwendet wird. Die Unterstützung hängt von der Erweiterung ab, die die Verbindung verwendet. In einigen Fällen ist möglicherweise weiterhin eine Verbindungszeichenfolge in Functions erforderlich, obwohl der Dienst, mit dem Sie eine Verbindung herstellen, identitätsbasierte Verbindungen unterstützt.

Identitätsbasierte Verbindungen werden von den folgenden Trigger- und Bindungserweiterungen unterstützt:

> [!NOTE]
> Identitätsbasierte Verbindungen werden mit Durable Functions nicht unterstützt.

| Name der Erweiterung | Erweiterungsversion                                                                                     | Unterstützte Pläne     |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------|
| Azure Blob     | [Version 5.0.0-beta1 oder höher](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | All                 |
| Azure Queue    | [Version 5.0.0-beta1 oder höher](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | All                 |
| Azure Event Hubs    | [Version 5.0.0-beta1 oder höher](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | All            |
| Azure-Servicebus    | [Version 5.0.0-beta2 oder höher](./functions-bindings-service-bus.md#service-bus-extension-5x-and-higher) | All         |
| Azure Cosmos DB   | [Version 4.0.0-preview1 oder höher](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) | Elastic Premium |


Die von der Functions-Runtime (`AzureWebJobsStorage`) verwendeten Speicherverbindungen können auch mithilfe einer identitätsbasierten Verbindung konfiguriert werden. Weitere Informationen finden Sie weiter unten unter [Verbinden zum Hostspeicher mit einer Identität](#connecting-to-host-storage-with-an-identity).

Identitätsbasierte Verbindungen verwenden eine [verwaltete Identität](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json), wenn sie im Azure Functions-Dienst gehostet werden. Standardmäßig wird eine vom System zugewiesene Identität verwendet, auch wenn mit den Eigenschaften `credential` und `clientID` eine vom Benutzer zugewiesene Identität angegeben werden kann. Bei Ausführung in anderen Kontexten (z. B. bei der lokalen Entwicklung) wird stattdessen Ihre Entwickleridentität verwendet, obwohl dies mithilfe alternativer Verbindungsparameter angepasst werden kann.

#### <a name="grant-permission-to-the-identity"></a>Erteilen der Berechtigung für die Identität

Unabhängig davon, welche Identität verwendet wird, muss diese über Berechtigungen zum Ausführen der vorgesehenen Aktionen verfügen. Dies erfolgt in der Regel durch Zuweisen einer Rolle in Azure RBAC oder durch Angeben der Identität in einer Zugriffsrichtlinie, abhängig vom Dienst, mit dem Sie eine Verbindung herstellen. Informationen dazu, welche Berechtigungen erforderlich sind und wie diese festgelegt werden können, finden Sie in der Dokumentation zur jeweiligen Erweiterung.

> [!IMPORTANT]
> Vom Zieldienst werden möglicherweise einige nicht für alle Kontexte erforderliche Berechtigungen verfügbar gemacht. Befolgen Sie nach Möglichkeit das **Prinzip der geringsten Berechtigung**, und gewähren Sie der Identität nur die erforderlichen Berechtigungen. Wenn die App z. B. nur aus einem Blob lesen muss, verwenden Sie die Rolle [Speicherblob-Datenleser](../role-based-access-control/built-in-roles.md#storage-blob-data-reader), da die Rolle [Besitzer der Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) über zu umfangreiche Berechtigungen für einen Lesevorgang verfügt.
Die folgenden Rollen umfassen die primären Berechtigungen, die für jede Erweiterung im Normalbetrieb benötigt werden:

| Dienst     | Integrierte Beispielrollen |
|-------------|------------------------|
| Azure-Blobs  | [Speicherblob-Datenleser](../role-based-access-control/built-in-roles.md#storage-blob-data-reader), [Speicherblob-Datenbesitzer](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)                 |
| Azure-Warteschlangen | [Speicherwarteschlangen-Datenleser](../role-based-access-control/built-in-roles.md#storage-queue-data-reader), [Speicherwarteschlangen-Datennachrichtenprozessor](../role-based-access-control/built-in-roles.md#storage-queue-data-message-processor), [Speicherwarteschlangen-Datennachrichtensender](../role-based-access-control/built-in-roles.md#storage-queue-data-message-sender), [Speicherwarteschlangen-Datenmitwirkender](../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)             |
| Event Hubs   |    [Azure Event Hubs-Datenempfänger](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver), [Azure Event Hubs-Datensender](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender), [Azure Event Hubs-Datenbesitzer](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)              |
| Service Bus | [Azure Service Bus-Datenempfänger](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver), [Azure Service Bus-Datensender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender), [Azure Service Bus-Datenbesitzer](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) |
| Azure Cosmos DB | [Integrierter Cosmos DB-Datenleser](../cosmos-db/how-to-setup-rbac.md#built-in-role-definitions), [Integrierter Mitwirkender an Cosmos DB-Daten](../cosmos-db/how-to-setup-rbac.md#built-in-role-definitions) |

#### <a name="connection-properties"></a>Verbindungseigenschaften

Eine identitätsbasierte Verbindung für einen Azure-Dienst akzeptiert die folgenden Eigenschaften, wobei `<CONNECTION_NAME_PREFIX>` der Wert Ihrer `connection`-Eigenschaft in der Trigger- oder Bindungsdefinition ist:

| Eigenschaft    | Erforderlich für Erweiterungen | Umgebungsvariable | BESCHREIBUNG |
|---|---|---|---|
| Dienst-URI | Azure Blob<sup>1</sup>, Azure Warteschlange | `<CONNECTION_NAME_PREFIX>__serviceUri` | Der URI der Datenebene des Diensts, mit dem Sie eine Verbindung herstellen. |
| Vollqualifizierter Namespace | Event Hubs, Service Bus | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Der vollqualifizierte Event Hubs- und Service Bus-Namespace. |
| Kontoendpunkt | Azure Cosmos DB | `<CONNECTION_NAME_PREFIX>__accountEndpoint` | Der URI des Azure Cosmos DB-Kontoendpunkts. |
| Token-Anmeldeinformationen | (Optional) | `<CONNECTION_NAME_PREFIX>__credential` | Damit wird festgelegt, wie für die Verbindung ein Token abgerufen werden soll. Wird nur empfohlen, wenn eine vom Benutzer zugewiesene Identität angegeben wird. Dann muss sie auf „managedidentity“ festgelegt werden. Dies gilt nur, wenn sie im Azure Functions-Dienst gehostet wird. |
| Client-ID | (Optional) | `<CONNECTION_NAME_PREFIX>__clientId` | Wenn `credential` auf „managedidentity“ festgelegt ist, wird mit dieser Eigenschaft die vom Benutzer zugewiesene Identität angegeben, die beim Abrufen eines Tokens verwendet werden soll. Die Eigenschaft akzeptiert eine Client-ID, die einer vom Benutzer zugewiesenen Identität entspricht, die der Anwendung zugeordnet ist. Wenn nichts angegeben wird, wird die vom System zugewiesene Identität verwendet. Diese Eigenschaft wird in [Szenarios für die lokale Entwicklung](#local-development-with-identity-based-connections) anders verwendet, wenn `credential` nicht festgelegt werden darf. |

<sup>1</sup> Für Azure Blob sind sowohl Blob- als auch Warteschlangendienst-URIs erforderlich.

Für einen bestimmten Verbindungstyp können weitere Optionen unterstützt werden. Weitere Informationen finden Sie in der Dokumentation zu der Komponente, die die Verbindung herstellt.

##### <a name="local-development-with-identity-based-connections"></a>Lokale Entwicklung mit identitätsbasierten Verbindungen

Bei lokaler Ausführung weist die Konfiguration oben die Laufzeit an, Ihre lokale Entwickleridentität zu verwenden. Die Verbindung versucht, ein Token von den folgenden Speicherorten in der angegebenen Reihenfolge abzurufen:

- Lokaler Cache, der von Microsoft-Anwendungen gemeinsam genutzt wird
- Aktueller Benutzerkontext in Visual Studio
- Aktueller Benutzerkontext in Visual Studio Code
- Aktueller Benutzerkontext in der Azure CLI

Wenn keine dieser Optionen erfolgreich ist, tritt ein Fehler auf.

In einigen Fällen möchten Sie möglicherweise die Verwendung einer anderen Identität angeben. Sie können Konfigurationseigenschaften für die Verbindung hinzufügen, die auf die alternative Identität verweisen.

> [!NOTE]
> Die folgenden Konfigurationsoptionen werden nicht unterstützt, wenn das Hosting im Azure Functions-Dienst erfolgt.

Um eine Verbindung mithilfe eines Azure Active Directory-Dienstprinzipals mit einer Client-ID und einem Geheimnis herzustellen, definieren Sie die Verbindung zusätzlich zu obigen [Verbindungseigenschaften](#connection-properties) mit den folgenden erforderlichen Eigenschaften:

| Eigenschaft    | Umgebungsvariable | BESCHREIBUNG |
|---|---|---|
| Mandanten-ID | `<CONNECTION_NAME_PREFIX>__tenantId` | Die ID des Azure Active Directory-Mandanten (Verzeichnis). |
| Client-ID | `<CONNECTION_NAME_PREFIX>__clientId` |  Die Client-ID (Anwendungs-ID) einer App-Registrierung im Mandanten. |
| Geheimer Clientschlüssel | `<CONNECTION_NAME_PREFIX>__clientSecret` | Ein Clientgeheimnis, das für die App-Registrierung generiert wurde. |

Beispiel für `local.settings.json`-Eigenschaften, die für identitätsbasierte Verbindungen mit Azure Blob Storage erforderlich sind: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

Beispiel für `local.settings.json`-Eigenschaften, die für identitätsbasierte Verbindungen mit Azure Cosmos DB erforderlich sind: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__accountEndpoint": "<accountEndpoint>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="connecting-to-host-storage-with-an-identity"></a>Verbinden zum Hostspeicher mit einer Identität

Azure Functions verwendet standardmäßig die `AzureWebJobsStorage`-Verbindung für Kernfunktionalität wie die Koordination der Singletonausführung von Timertriggern und der standardmäßigen Speicherung von App-Schlüsseln. Dies kann auch so konfiguriert werden, dass eine Identität genutzt wird.

> [!CAUTION]
> Einige Apps werden `AzureWebJobsStorage` für Speicherverbindungen in ihren Triggern, Bindungen und/oder Funktionscode wiederverwenden. Stellen Sie sicher, dass bei Verwendung von `AzureWebJobsStorage` immer das identitätsbasierte Verbindungsformat verwendet werden kann, bevor Sie diese Verbindung durch eine Verbindungszeichenfolge ändern.

Um die Verbindung auf diese Weise zu konfigurieren, stellen Sie sicher, dass die Identität der App über die Rolle [Besitzer von Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) verfügt, um die Kernfunktionalität des Hosts zu unterstützen. Möglicherweise benötigen Sie zusätzliche Berechtigungen, wenn Sie „AzureWebJobsStorage“ für andere Zwecke verwenden.

Wenn Sie ein Speicherkonto verwenden, das global das standardmäßige DNS-Suffix und den standardmäßigen Dienstnamen für Azure verwendet, können Sie gemäß dem `https://<accountName>.blob/queue/file/table.core.windows.net`-Format den Namen Ihres Speicherkontos als `AzureWebJobsStorage__accountName` festlegen. 

Wenn Sie stattdessen ein Speicherkonto in einer Sovereign Cloud oder mit benutzerdefiniertem DNS verwenden, legen Sie für `AzureWebJobsStorage__serviceUri` die URI Ihres Blobdiensts fest. Wenn „AzureWebJobsStorage“ für einen anderen Dienst verwendet wird, können Sie stattdessen separat `AzureWebJobsStorage__blobServiceUri`, `AzureWebJobsStorage__queueServiceUri` und `AzureWebJobsStorage__tableServiceUri` angeben.

## <a name="reporting-issues"></a>Melden von Problemen
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)
* [Lokales Codieren und Testen von Azure Functions](./functions-develop-local.md)
* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [C#-Entwicklerreferenz zu Azure Functions](functions-dotnet-class-library.md)
* [Node.js-Entwicklerreferenz zu Azure Functions](functions-reference-node.md)
