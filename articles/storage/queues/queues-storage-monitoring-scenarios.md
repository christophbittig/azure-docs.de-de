---
title: Bewährte Methoden für die Überwachung von Azure Queue Storage
description: Hier erhalten Sie Informationen zu bewährten Methoden und deren Verwendung bei der Überwachung von Azure Queue Storage mithilfe von Metriken und Protokollen.
author: normesta
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.author: normesta
ms.date: 08/24/2021
ms.custom: monitoring
ms.openlocfilehash: e7344330740bebc41a49547c7602a852b5f7665f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122832295"
---
# <a name="best-practices-for-monitoring-azure-queue-storage"></a>Bewährte Methoden für die Überwachung von Azure Queue Storage

Dieser Artikel enthält eine Sammlung häufiger Überwachungsszenarios für Queue Storage sowie Richtlinien zu bewährten Methoden für deren Umsetzung.  

## <a name="monitor-message-counts-in-each-queue"></a>Überwachen der Nachrichtenanzahl in den Warteschlangen 

Sie können für alle Warteschlangen in einem Speicherkonto mithilfe der Metrik `QueueMessageCount` die Anzahl der Nachrichten überwachen. Diese Metrik wird täglich aktualisiert.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Wenn Sie PowerShell verwenden, können Sie einen Befehl wie den folgenden nutzen:  

```powershell
(Get-AzMetric -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/queueServices/default -MetricName "QueueMessageCount").data.Average
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie die Azure CLI verwenden, können Sie einen Befehl wie den folgenden nutzen: 

```azurecli
az monitor metrics list --resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/ --metric "QueueMessageCount" --interval PT1H --query value[0].timeseries[0].data[0].average
```

---

Wenn Sie dynamisch bestimmen möchten, ob Workloads an das Nachrichtenvolumen angepasst werden müssen, können Sie die ungefähre Anzahl der Nachrichten für jede Warteschlange abfragen und dann entsprechend reagieren. Verwenden Sie zum Abrufen der ungefähren Nachrichtenanzahl den REST-Vorgang [Get Queue Metadata](/rest/api/storageservices/get-queue-metadata) oder eines der unterstützten Blob Storage SDKs. 

Im folgenden Beispiel wird die ungefähre Nachrichtenanzahl mithilfe der .NET v12-Bibliothek für Azure Storage abgerufen:

```csharp
        static async Task<string> RetrieveNextMessageAsync(QueueClient theQueue)
        {
            if (await theQueue.ExistsAsync())
            {
                QueueProperties properties = await theQueue.GetPropertiesAsync();

                if (properties.ApproximateMessagesCount > 0)
                {
                    QueueMessage[] retrievedMessage = await theQueue.ReceiveMessagesAsync(1);
                    string theMessage = retrievedMessage[0].MessageText;
                    await theQueue.DeleteMessageAsync(retrievedMessage[0].MessageId, retrievedMessage[0].PopReceipt);
                    return theMessage;
                }

                return null;
            }

            return null;
        }
```

Eine weitere Möglichkeit besteht in der Nutzung von Service Bus, das Nachrichten pro Entität unterstützt. Weitere Informationen finden Sie unter [Referenz zum Überwachen von Azure Service Bus-Daten](../../service-bus-messaging/monitor-service-bus-reference.md). 

## <a name="audit-account-activity"></a>Überwachen der Kontoaktivität

In vielen Fällen müssen Sie die Aktivitäten in Ihren Speicherkonten auf Sicherheit und Konformität überwachen. Die Vorgänge in Speicherkonten lassen sich in zwei Kategorien unterteilen: Vorgänge auf *Steuerungsebene* und Vorgänge auf *Datenebene*. 

Ein Vorgang auf Steuerungsebene ist jede Azure Resource Manager-Anforderung zum Erstellen eines Speicherkontos oder zum Aktualisieren einer Eigenschaft eines vorhandenen Speicherkontos. Weitere Informationen finden Sie unter [Azure Resource Manager](../../azure-resource-manager/management/overview.md). 

Bei einem Vorgang auf Datenebene handelt es sich um einen Vorgang, der sich auf die Daten in einem Speicherkonto auswirkt und sich aus einer Anforderung an den Speicherdienstendpunkt ergibt. So handelt es sich beispielsweise um einen Vorgang auf Datenebene, wenn Sie der Warteschlange eine Nachricht hinzufügen. Weitere Informationen finden Sie unter [Azure Storage-API](/rest/api/storageservices/). 

In diesem Abschnitt erfahren Sie, wie Sie die Informationen zum „Wann“, „Wer“, „Was“ und „Wie“ für Vorgänge auf Steuerungs- und Datenebene ermitteln.

### <a name="auditing-control-plane-operations"></a>Überwachen von Vorgängen auf Steuerungsebene

Resource Manager-Vorgänge werden im [Azure-Aktivitätsprotokoll](../../azure-monitor/essentials/activity-log.md) erfasst. Öffnen Sie zum Anzeigen des Aktivitätsprotokolls Ihr Speicherkonto im Azure-Portal, und klicken Sie anschließend auf **Aktivitätsprotokoll**.

> [!div class="mx-imgBorder"]
> ![Aktivitätsprotokoll](./media/queues-storage-monitoring-scenarios/activity-log.png)


Öffnen Sie einen beliebigen Protokolleintrag, um den JSON-Code anzuzeigen, mit dem die Aktivität beschrieben wird. Mit dem folgenden JSON-Code werden die Informationen zum „Wann“, „Was“ und „Wie“ für einen Vorgang auf Steuerungsebene angegeben:

> [!div class="mx-imgBorder"]
> ![Aktivitätsprotokoll im JSON-Code](./media/queues-storage-monitoring-scenarios/activity-log-json.png)

Die Verfügbarkeit der Informationen zum „Wer“ hängt von der Authentifizierungsmethode ab, die zum Ausführen des Vorgangs auf Steuerungsebene verwendet wurde. Wurde die Autorisierung von einem Azure AD-Sicherheitsprinzipal durchgeführt, wird der Objektbezeichner des Sicherheitsprinzipals auch in dieser JSON-Ausgabe angezeigt (z. B. `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`). Da möglicherweise nicht immer weitere identitätsbezogene Informationen wie E-Mail-Adresse oder Name angezeigt werden, stellt der Objektbezeichner die beste Möglichkeit zur eindeutigen Identifizierung des Sicherheitsprinzipals dar. 

Sie können den Anzeigenamen des Sicherheitsprinzipals ermitteln, indem Sie mithilfe des Werts des Objektbezeichners auf der Azure AD-Seite im Azure-Portal nach dem Sicherheitsprinzipal suchen. Im folgenden Screenshot sehen Sie ein Suchergebnis in Azure AD:

> [!div class="mx-imgBorder"]
> ![Suche in Azure Active Directory](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>Überwachen von Vorgängen auf Datenebene

Vorgänge auf Datenebene werden in [Azure-Ressourcenprotokollen für Queue Storage](monitor-queue-storage.md#analyzing-logs) erfasst. Sie können die [Diagnoseeinstellung](monitor-queue-storage.md#send-logs-to-azure-log-analytics) so konfigurieren, dass die Protokolle in den Log Analytics-Arbeitsbereich exportiert werden und so eine native Abfrage ermöglicht wird. 

Mit der folgenden Log Analytics-Abfrage werden die Informationen zum „Wann“, „Wer“, „Was“ und „Wie“ in einer Liste von Protokolleinträgen abgerufen. 

```kusto
StorageQueueLogs 
| where TimeGenerated > ago(3d) 
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

Für das „Wann“ der Überwachung wird im Feld `TimeGenerated` der Zeitpunkt angezeigt, an dem der Protokolleintrag erfasst wurde. 

Für das „Was“ der Überwachung wird im Feld `Uri` das Element angezeigt, das geändert oder gelesen wurde. 

Für das „Wie“ der Überwachung wird im Feld `OperationName` der Vorgang angezeigt, der ausgeführt wurde. 

Für das „Wer“ der Überwachung wird in `AuthenticationType` der Authentifizierungstyp angezeigt, der für eine Anforderung verwendet wurde. In diesem Feld können alle von Azure Storage unterstützten Authentifizierungstypen angezeigt werden, z. B. die Verwendung eines Kontoschlüssels oder eines SAS-Tokens sowie die Azure Active Directory-Authentifizierung (Azure AD). 

Wurde eine Anforderung über Azure AD authentifiziert, stellt das Feld `RequesterObjectId` die zuverlässigste Methode zur Identifizierung des Sicherheitsprinzipals dar. Sie können den Anzeigenamen des Sicherheitsprinzipals ermitteln, indem Sie mithilfe des Werts in Feld `RequesterObjectId` auf der Azure AD-Seite im Azure-Portal nach dem Sicherheitsprinzipal suchen. Im folgenden Screenshot sehen Sie ein Suchergebnis in Azure AD:

> [!div class="mx-imgBorder"]
> ![Suche in Azure Active Directory (2)](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

In einigen Fällen wird in den Protokollen möglicherweise ein Benutzerprinzipalname (User Principal Name, *UPN*) angezeigt. Ist der Sicherheitsprinzipal beispielsweise ein Azure AD-Benutzer, wird wahrscheinlich der UPN angezeigt. Bei anderen Typen von Sicherheitsprinzipalen, wie etwa benutzerseitig zugewiesenen verwalteten Identitäten, oder in bestimmten Szenarios, wie etwa bei der mandantenübergreifenden Azure AD-Authentifizierung, wird der UPN nicht in den Protokollen angezeigt. 

Mit der folgenden Abfrage werden alle von OAuth-Sicherheitsprinzipalen ausgeführten Schreibvorgänge angezeigt:

```kusto
StorageQueueLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutMessage"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

Die Authentifizierung über gemeinsam verwendete Schlüssel und die SAS-Authentifizierung ermöglichen keine Überwachung einzelner Identitäten. Wenn Sie also die Möglichkeit der identitätsbasierten Überwachung verbessern möchten, sollten Sie auf Azure AD umsteigen und die Authentifizierung über gemeinsam verwendete Schlüssel sowie die SAS-Authentifizierung verhindern. Informationen zum Verhindern dieser beiden Authentifizierungstypen finden Sie unter [Verhindern der Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Azure Storage-Konto](../common/shared-key-authorization-prevent.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json&tabs=portal). Informationen zu den ersten Schritten mit Azure AD finden Sie unter [Autorisieren des Zugriffs auf Warteschlangen mithilfe von Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="optimize-cost-for-infrequent-queries"></a>Optimieren der Kosten bei seltenem Durchführen von Abfragen

Sie können Protokolle in Log Analytics exportieren, um dort die umfassenden nativen Abfragefunktionen zu nutzen. Bei umfangreichen Transaktionen in Ihrem Speicherkonto können die Kosten für die Verwendung von Protokollen in Log Analytics beträchtlich sein. Weitere Informationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/). Wenn Sie Protokolle nur gelegentlich abfragen möchten (z. B. zur Konformitätsüberwachung), können Sie die Gesamtkosten reduzieren, indem Sie die Protokolle in ein Speicherkonto exportieren und dann zusätzlich zu den Protokolldaten eine serverlose Abfragelösung verwenden, wie etwa Azure Synapse.

Mit Azure Synapse können Sie einen serverlosen SQL-Pool erstellen, um Protokolldaten bei Bedarf abzufragen. Dadurch lassen sich die Kosten erheblich senken. 

1. Exportieren Sie die Protokolle in ein Speicherkonto. Weitere Informationen finden Sie unter [Erstellen einer Diagnoseeinstellung](monitor-queue-storage.md#creating-a-diagnostic-setting).

2. Erstellen und konfigurieren Sie einen Synapse-Arbeitsbereich. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Synapse-Arbeitsbereichs](../../synapse-analytics/quickstart-create-workspace.md).

2. Fragen Sie die Protokolle ab. Weitere Informationen finden Sie unter [Abfragen von JSON-Dateien mit einem serverlosen SQL-Pool in Azure Synapse Analytics](../../synapse-analytics/sql/query-json-files.md).

   Hier sehen Sie ein Beispiel:

   ```sql
    select
        JSON_VALUE(doc, '$.time') AS time,
        JSON_VALUE(doc, '$.properties.accountName') AS accountName,
        JSON_VALUE(doc, '$.identity.type') AS identityType,    
        JSON_VALUE(doc, '$.identity.requester.objectId') AS requesterObjectId,
        JSON_VALUE(doc, '$.operationName') AS operationName,
        JSON_VALUE(doc, '$.callerIpAddress') AS callerIpAddress,
        JSON_VALUE(doc, '$.uri') AS uri
        doc
    from openrowset(
            bulk 'https://demo2uswest4log.blob.core.windows.net/insights-logs-storageread/resourceId=/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/demo2uswest/blobServices/default/y=2021/m=03/d=19/h=*/m=*/PT1H.json',
            format = 'csv', fieldterminator ='0x0b', fieldquote = '0x0b'
        ) with (doc nvarchar(max)) as rows
    order by JSON_VALUE(doc, '$.time') desc

   ```

## <a name="see-also"></a>Weitere Informationen

- [Überwachen von Azure Queue Storage](monitor-queue-storage.md)
- [Überwachen von Daten in Azure Queue Storage – Referenz](monitor-queue-storage-reference.md)
- [Tutorial: Verwenden von Kusto-Abfragen in Azure Data Explorer und Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)
- [Get started with log queries in Azure Monitor (Erste Schritte mit Abfragen in Azure Monitor)](../../azure-monitor/logs/get-started-queries.md)

  

