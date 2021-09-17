---
title: Datei einfügen
description: Datei einfügen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 06/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ad25ce992dec7165e2b936e5642e8c3a209ce6a5
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122516275"
---
Event Hubs erfasst Diagnoseprotokolle für die folgenden Kategorien:

| Category | BESCHREIBUNG | 
| -------- | ----------- | 
| Archivprotokolle | Erfasst Informationen zu [Event Hubs Capture](../event-hubs-capture-overview.md)-Vorgängen, insbesondere Protokolle zu Erfassungsfehlern. |
| Betriebsprotokolle | Erfassen alle Verwaltungsvorgänge, die für den Azure Event Hubs-Namespace ausgeführt werden. Datenvorgänge werden aufgrund der großen Menge von Datenvorgängen, die für Azure Event Hubs durchgeführt werden, nicht erfasst. |
| Protokolle zur automatischen Skalierung | Erfassen automatische Vergrößerungsvorgänge, die für einen Event Hubs-Namespace ausgeführt wurden. |
| Kafka-Koordinatorprotokolle | Erfassen Kafka-Koordinatorvorgänge, die sich auf Event Hubs beziehen. |
| Kafka-Benutzerfehlerprotokolle | Erfassen Informationen zu Kafka-APIs, die für Event Hubs aufgerufen werden. |
| Event Hubs-VNET (Virtual Network)-Verbindungsereignis | Erfasst Informationen zu IP-Adressen und virtuellen Netzwerken, die Datenverkehr an Event Hubs senden. |
| Benutzerprotokolle für kundenseitig verwaltete Schlüssel | Erfassen Vorgänge in Verbindung mit Schlüsseln, die kundenseitig verwaltet werden. |


Alle Protokolle werden im JavaScript Object Notation (JSON)-Format gespeichert. Jeder Eintrag enthält Zeichenfolgenfelder im nachfolgend beschriebenen Format.

### <a name="archive-logs-schema"></a>Archivprotokollschema

JSON-Zeichenfolgen im Archivprotokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

Name | BESCHREIBUNG
------- | -------
`TaskName` | Beschreibung der fehlgeschlagenen Aufgabe
`ActivityId` | Interne ID zur Nachverfolgung
`trackingId` | Interne ID zur Nachverfolgung
`resourceId` | Azure Resource Manager-Ressourcen-ID
`eventHub` | Vollständiger Event Hub-Name (mit Namespace-Name)
`partitionId` | Event Hub-Partition, auf die geschrieben wird
`archiveStep` | Mögliche Werte: ArchiveFlushWriter, DestinationInit
`startTime` | Fehlerstartzeit
`failures` | Häufigkeit des Fehlers
`durationInSeconds` | Dauer des Fehlers
`message` | Fehlermeldung
`category` | ArchiveLogs

Es folgt ein Codebeispiel für eine JSON-Zeichenfolge im Archivierungsprotokoll:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Schema „Betriebsprotokolle“

JSON-Zeichenfolgen im Betriebsprotokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

Name | BESCHREIBUNG
------- | -------
`ActivityId` | Interne ID zur Nachverfolgung |
`EventName` | Vorgangsname Eine Liste der Werte für dieses Element finden Sie unter den [Ereignisnamen](#event-names). |
`resourceId` | Azure Resource Manager-Ressourcen-ID |
`SubscriptionId` | Abonnement-ID |
`EventTimeString` | Vorgangsdauer |
`EventProperties` |Eigenschaften für den Vorgang. Dieses Element stellt weitere Informationen zu dem Ereignis bereit, wie im folgenden Beispiel gezeigt. |
`Status` | Vorgangsstatus Der Wert kann entweder **Erfolgreich** oder **Fehler** lauten.  |
`Caller` | Aufrufer des Vorgangs (Azure-Portal oder Verwaltungsclient) |
`Category` | OperationalLogs |

Es folgt ein Codebeispiel für eine JSON-Zeichenfolge im Betriebsprotokoll:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

#### <a name="event-names"></a>Ereignisnamen
Der Ereignisname setzt sich aus Vorgangstyp und Ressourcentyp aus den folgenden Enumerationen zusammen. Beispiel: `Create Queue`, `Retrieve Event Hu` oder `Delete Rule`. 

| Vorgangsart | Ressourcentyp | 
| -------------- | ------------- | 
| <ul><li>Erstellen</li><li>Aktualisieren</li><li>Löschen</li><li>Gerätehandle</li><li>Unbekannt</li></ul> | <ul><li>Namespace</li><li>Warteschlange</li><li>Thema</li><li>Subscription</li><li>EventHub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>Regel</li>ConsumerGroup</li> |

### <a name="autoscale-logs-schema"></a>Schema der Protokolle für automatische Skalierung
Die JSON-Zeichenfolge im Protokoll für automatische Skalierung enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- | 
| `TrackingId` | Interne ID, die für Ablaufverfolgungszwecke verwendet wird. |
| `ResourceId` | Azure Resource Manager-Ressourcen-ID |
| `Message` | Informationsmeldung, die Details zur Aktion der automatischen Vergrößerung enthält. Die Meldung enthält den vorherigen und den aktuellen Wert der Durchsatzeinheit für einen bestimmten Namespace und den Grund, warum die Vergrößerung der Durchsatzeinheit ausgelöst wurde. |

Beispiel für ein Autoskalierungsereignis: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

### <a name="kafka-coordinator-logs-schema"></a>Schema der Kafka-Koordinatorprotokolle
Die JSON-Zeichenfolge im Kafka-Koordinatorprotokoll enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- | 
| `RequestId` | Anforderungs-ID, die zur Ablaufverfolgung verwendet wird |
| `ResourceId` | Azure Resource Manager-Ressourcen-ID |
| `Operation` | Der Name des Vorgangs, der während der Gruppenkoordination ausgeführt wird. |
| `ClientId` | Client-ID |
| `NamespaceName` | Namespacename | 
| `SubscriptionId` | Azure-Abonnement-ID |
| `Message` | Informative Meldung oder Warnmeldung, die Details zu den Aktionen bereitstellt, die während der Gruppenkoordination ausgeführt wurden. |

#### <a name="example"></a>Beispiel

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

### <a name="kafka-user-error-logs-schema"></a>Schema der Kafka-Benutzerfehlerprotokolle
Die JSON-Zeichenfolge im Kafka-Benutzerfehlerprotokoll enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- |
| `TrackingId` | Nachverfolgungs-ID, die zur Ablaufverfolgung verwendet wird. |
| `NamespaceName` | Namespacename |
| `Eventhub` | Event Hub-Name |
| `PartitionId` | Partitions-ID |
| `GroupId` | Gruppen-ID |
| `ClientId` | Client-ID |
| `ResourceId` | Azure Resource Manager-Ressourcen-ID |
| `Message` | Informationsmeldung, die Details zu einem Fehler enthält. |

### <a name="event-hubs-virtual-network-connection-event-schema"></a>Schema für Event Hubs-Verbindungsereignis mit virtuellem Netzwerk
Die JSON-Zeichenfolge für das Event Hubs-Verbindungsereignis mit virtuellem Netzwerk enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---  | ----------- | 
| `SubscriptionId` | Azure-Abonnement-ID |
| `NamespaceName` | Namespacename |
| `IPAddress` | IP-Adresse eines Clients, der eine Verbindung mit dem Event Hubs-Dienst herstellt. |
| `Action` | Aktion, die vom Event Hubs-Dienst beim Auswerten von Verbindungsanforderungen ausgeführt wird. Unterstützt werden die Aktionen zum **Akzeptieren von Verbindungen** und **Ablehnen von Verbindungen**. |
| `Reason` | Gibt einen Grund an, warum die Aktion durchgeführt wurde. |
| `Count` | Anzahl von Vorkommen für die angegebene Aktion. |
| `ResourceId` | Azure Resource Manager-Ressourcen-ID |

Protokolle virtueller Netzwerke werden nur dann generiert, wenn der Namespace Zugriff aus **ausgewählten Netzwerken** oder über **spezifische IP-Adressen** (IP-Filterregeln) erlaubt. Wenn Sie den Zugriff auf Ihren Namespace mit diesen Features nicht einschränken möchten und dennoch Protokolle virtueller Netzwerke erhalten möchten, um IP-Adressen von Clients zu verfolgen, die sich mit dem Namespace der Event Hubs verbinden, können Sie die folgende Umgehungslösung verwenden. [Aktivieren Sie IP-Filterung](../event-hubs-ip-filtering.md), und fügen Sie den gesamten adressierbaren IPv4-Bereich (1.0.0.0/1 bis 255.0.0.0/1) hinzu. Die IP-Filterung von Event Hubs unterstützt keine IPv6-Adressbereiche. Möglicherweise werden private Endpunktadressen im IPv6-Format im Protokoll angezeigt. 

#### <a name="example"></a>Beispiel

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="customer-managed-key-user-logs-schema"></a>Benutzerprotokollschema für kundenseitig verwaltete Schlüssel
Die JSON-Zeichenfolge im Benutzerprotokoll für kundenseitig verwaltete Schlüssel enthält Elemente, die in der folgenden Tabelle aufgeführt werden:

| Name | BESCHREIBUNG |
| ---- | ----------- | 
| `Category` | Typ der Kategorie für eine Meldung. Hierbei handelt es sich um einen der folgenden Werte: **Fehler** oder **Info**. Wenn beispielsweise der Schlüssel aus Ihrem Schlüsseltresor deaktiviert wird, dann handelt es sich hierbei um eine Informationskategorie. Wenn ein Schlüssel nicht entpackt werden kann, dann handelt es sich um einen Fehler.|
| `ResourceId` | Interne Ressourcen-ID, die die Azure-Abonnement-ID und den Namespacenamen umfasst. |
| `KeyVault` | Name der Key Vault-Ressource. |
| `Key` | Der Name des Key Vault-Schlüssels, der zum Verschlüsseln des Event Hubs-Namespace verwendet wird. |
| `Version` | Version des Key Vault-Schlüssels.|
| `Operation` | Der Vorgang, der für den Schlüssel in Ihrem Schlüsseltresor ausgeführt wird. Dazu zählen etwa das Deaktivieren/Aktvieren, das Packen und das Entpacken des Schlüssels. |
| `Code` | Der Code, der dem Vorgang zugeordnet ist. Beispiel: Der Fehlercode 404 bedeutet, dass der Schlüssel nicht gefunden wurde. |
| `Message` | Meldung, die Details zu einem Fehler oder einer Informationsmeldung enthält. |

Im Folgenden finden Sie ein Beispiel für das Protokoll für einen kundenseitig verwalteten Schlüssel:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

Im Folgenden finden Sie die allgemeinen Fehlercodes, nach denen Sie suchen müssen, wenn die BYOK-Verschlüsselung aktiviert ist.

| Aktion | Fehlercode | Resultierender Zustand der Daten |
| ------ | ---------- | ----------------------- | 
| Widerrufen der Berechtigung zum Packen/Entpacken aus einem Schlüsseltresor | 403 |    Inaccessible |
| Entfernen der AAD-Rollenmitgliedschaft aus einem AAD-Prinzipal, der die Berechtigung zum Packen/Entpacken gewährt hat | 403 |  Inaccessible |
| Löschen eines Verschlüsselungsschlüssels aus dem Schlüsseltresor | 404 | Inaccessible |
| Löschen des Schlüsseltresors | 404 | Zugriff nicht möglich (unter der Annahme, dass vorläufiges Löschen aktiviert ist, da dies eine erforderliche Einstellung ist) |
| Ändern des Ablaufzeitraums für den Verschlüsselungsschlüssel, sodass er bereits abgelaufen ist | 403 |   Inaccessible  |
| Ändern des Werts für NBF (not before, nicht vor), sodass der Schlüsselverschlüsselungsschlüssel nicht aktiv ist | 403 | Inaccessible  |
| Auswählen der Option **Allow MSFT Services** (MSFT-Dienste zulassen) für die Firewall des Schlüsseltresors oder anderweitiges Blockieren des Netzwerkzugriffs auf den Schlüsseltresor, der den Verschlüsselungsschlüssel enthält | 403 | Inaccessible |
| Verschieben des Schlüsseltresors in einen anderen Mandanten | 404 | Inaccessible |  
| Zeitweilig auftretendes Netzwerkproblem oder DNS-/AAD-/MSI-Ausfall |  | Zugriff mithilfe des zwischengespeicherten Datenverschlüsselungsschlüssels |

