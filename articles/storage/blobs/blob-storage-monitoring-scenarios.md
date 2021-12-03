---
title: Bewährte Methoden für die Überwachung von Azure Blob Storage
description: Informationen zu bewährten Methoden und deren Umsetzung bei der Überwachung von Azure Blob Storage über Metriken und Protokolle.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 07/30/2021
ms.custom: monitoring
ms.openlocfilehash: 222c78803e268cef95dc322952874f58346c2919
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180169"
---
# <a name="best-practices-for-monitoring-azure-blob-storage"></a>Bewährte Methoden für die Überwachung von Azure Blob Storage

Dieser Artikel enthält eine Sammlung allgemeiner Speicherüberwachungsszenarien sowie Richtlinien für bewährte Methoden zu deren Umsetzung.

## <a name="identify-storage-accounts-with-no-or-low-use"></a>Identifizieren von Speicherkonten mit keiner oder geringer Nutzung

Storage-Erkenntnisse ist ein Dashboard, das zusätzlich zu den Metriken und Protokollen von Azure Storage verwendet werden kann. Mit Storage-Erkenntnissen können Sie das Transaktionsvolumen und die verwendete Kapazität aller Ihrer Konten untersuchen. Anhand dieser Informationen können Sie entscheiden, welche Konten Sie eventuell einstellen. Informationen zur Konfiguration von Storage-Erkenntnissen finden Sie unter [Überwachen Ihres Speicherdiensts mit Azure Monitor Storage-Erkenntnissen](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

### <a name="analyze-transaction-volume"></a>Analysieren des Transaktionsvolumens

Sortieren Sie Ihre Konten in der [Ansicht „Storage-Erkenntnisse“ in Azure Monitor](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json#view-from-azure-monitor) in der Spalte **Transaktionen** in aufsteigender Reihenfolge. In der folgenden Abbildung ist ein Konto mit geringem Transaktionsvolumen im angegebenen Zeitraum dargestellt.

> [!div class="mx-imgBorder"]
> ![Transaktionsvolumen in „Storage-Erkenntnisse“](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-volume.png)

Klicken Sie auf den Kontolink, um mehr über diese Transaktionen zu erfahren. In diesem Beispiel werden die meisten Anforderungen an den Blob Storage-Dienst gesendet.

> [!div class="mx-imgBorder"]
> ![Transaktion nach Diensttyp](./media/blob-storage-monitoring-scenarios/storage-insights-transactions-by-storage-type.png)

Um festzustellen, welche Anforderungen ausgeführt werden, können Sie das Diagramm **Transactions by API name** (Transaktionen nach API-Name) genauer analysieren.

> [!div class="mx-imgBorder"]
> ![Storage, Transaktionen nach API](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-apis.png)

In diesem Beispiel sind alle Anforderungen Auflistungsvorgänge oder Anforderungen von Informationen zu Kontoeigenschaften. Lese- und Schreibvorgänge sind nicht enthalten. Dies kann Sie zu der Annahme verleiten, dass das Konto nicht in nennenswerter Weise verwendet wird.

### <a name="analyze-used-capacity"></a>Analysieren der verwendeten Kapazität

Sortieren Sie Ihre Konten auf der Registerkarte **Kapazität** der [Ansicht „Storage-Erkenntnisse“ in Azure Monitor](../common/storage-insights-overview.md#view-from-azure-monitor) in der Spalte **Verwendete Kontokapazität** in aufsteigender Reihenfolge. In der folgenden Abbildung ist ein Konto mit einem geringeren Kapazitätsvolumen als bei anderen Konten dargestellt.

> [!div class="mx-imgBorder"]
> ![Verwendete Speicherkapazität](./media/blob-storage-monitoring-scenarios/storage-insights-capacity-used.png)

Die Blobs, die dieser verwendeten Kapazität zugeordnet sind, können Sie mit Storage-Explorer untersuchen. Bei einer großen Anzahl von Blobs können Sie mithilfe einer [Blobbestandsrichtlinie](blob-inventory.md) einen Bericht generieren.

## <a name="monitor-the-use-of-a-container"></a>Überwachen der Verwendung eines Containers

Wenn Sie die Daten Ihrer Kunden nach Container partitionieren, können Sie überwachen, welche Kapazität die einzelnen Kunden verwenden. Mithilfe des Azure Storage-Blobbestands können Sie eine Inventur der Blobs mit Größeninformationen durchführen. Anschließend können Sie die Größe und Anzahl auf Containerebene aggregieren. Ein Beispiel finden Sie unter [Berechnen der Anzahl und Gesamtgröße von Blobs pro Container mit dem Azure Storage-Bestand](calculate-blob-count-size.md).

Außerdem können Sie den Datenverkehr auf Containerebene durch Abfragen von Protokollen auswerten. Informationen zum Erstellen von Log Analytics-Abfragen finden Sie unter [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md). Informationen zum Storage-Protokollschema finden Sie unter [Überwachen von Daten in Azure Blob Storage – Referenz](monitor-blob-storage-reference.md#resource-logs-preview).

Mit der folgenden Abfrage wird die Anzahl der Lesetransaktionen und die Anzahl der in jedem Container gelesenen Bytes abgerufen.

```kusto
StorageBlobLogs
| where OperationName  == "GetBlob"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize ReadSize = sum(ResponseBodySize), ReadCount = count() by tostring(ContainerName)
```

Mit der folgenden ähnlichen Abfrage werden Informationen zu Schreibvorgängen abgerufen.

```kusto
StorageBlobLogs
| where OperationName == "PutBlob" or
  OperationName == "PutBlock" or
  OperationName == "PutBlockList" or
  OperationName == "AppendBlock" or
  OperationName == "SnapshotBlob" or
  OperationName == "CopyBlob" or
  OperationName == "SetBlobTier"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize WriteSize = sum(RequestBodySize), WriteCount = count() by tostring(ContainerName)
```

Diese Abfrage referenziert die Namen mehrerer Vorgänge, da mehrere Vorgangstypen als Schreibvorgänge zählen können. Weitere Informationen dazu, welche Vorgänge als Lese- und Schreibvorgänge gelten, finden Sie unter [Azure Blob Storage – Preise](https://azure.microsoft.com/pricing/details/storage/blobs/) oder [Azure Data Lake Storage – Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="audit-account-activity"></a>Überwachen der Kontoaktivität

In vielen Fällen müssen Sie die Aktivitäten in Ihren Speicherkonten auf Sicherheit und Konformität überwachen. Die Vorgänge in Speicherkonten lassen sich in zwei Kategorien unterteilen: Vorgänge auf *Steuerungsebene* und Vorgänge auf *Datenebene*.

Ein Vorgang auf Steuerungsebene ist jede Azure Resource Manager-Anforderung zum Erstellen eines Speicherkontos oder zum Aktualisieren einer Eigenschaft eines vorhandenen Speicherkontos. Weitere Informationen finden Sie unter [Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Bei einem Vorgang auf Datenebene handelt es sich um einen Vorgang für die Daten in einem Speicherkonto, der sich aus einer Anforderung an den Speicherdienstendpunkt ergibt. Beispielsweise wird ein Vorgang auf Datenebene durchgeführt, wenn Sie ein Blob in ein Speicherkonto hochladen oder aus einem Speicherkonto herunterladen. Weitere Informationen finden Sie unter [Azure Storage-API](/rest/api/storageservices/).

In diesem Abschnitt erfahren Sie, wie Sie die Informationen zum „Wann“, „Wer“, „Was“ und „Wie“ für Vorgänge auf Steuerungs- und Datenebene ermitteln.

### <a name="auditing-control-plane-operations"></a>Überwachen von Vorgängen auf Steuerungsebene

Resource Manager-Vorgänge werden im [Azure-Aktivitätsprotokoll](../../azure-monitor/essentials/activity-log.md) erfasst. Öffnen Sie zum Anzeigen des Aktivitätsprotokolls Ihr Speicherkonto im Azure-Portal, und klicken Sie anschließend auf **Aktivitätsprotokoll**.

> [!div class="mx-imgBorder"]
> ![Aktivitätsprotokoll](./media/blob-storage-monitoring-scenarios/activity-log.png)

Öffnen Sie einen beliebigen Protokolleintrag, um den JSON-Code anzuzeigen, mit dem die Aktivität beschrieben wird. Mit dem folgenden JSON-Code werden die Informationen zum „Wann“, „Was“ und „Wie“ für einen Vorgang auf Steuerungsebene angegeben:

> [!div class="mx-imgBorder"]
> ![Aktivitätsprotokoll im JSON-Code](./media/blob-storage-monitoring-scenarios/activity-log-json.png)

Die Verfügbarkeit der Informationen zum „Wer“ hängt von der Authentifizierungsmethode ab, die zum Ausführen des Vorgangs auf Steuerungsebene verwendet wurde. Wurde die Autorisierung von einem Azure AD-Sicherheitsprinzipal durchgeführt, wird der Objektbezeichner des Sicherheitsprinzipals auch in dieser JSON-Ausgabe angezeigt (z. B. `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`). Da möglicherweise nicht immer weitere identitätsbezogene Informationen wie E-Mail-Adresse oder Name angezeigt werden, stellt der Objektbezeichner die beste Möglichkeit zur eindeutigen Identifizierung des Sicherheitsprinzipals dar.

Sie können den Anzeigenamen des Sicherheitsprinzipals ermitteln, indem Sie mithilfe des Werts des Objektbezeichners auf der Azure AD-Seite im Azure-Portal nach dem Sicherheitsprinzipal suchen. Im folgenden Screenshot sehen Sie ein Suchergebnis in Azure AD:

> [!div class="mx-imgBorder"]
> ![Suche in Azure Active Directory](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>Überwachen von Vorgängen auf Datenebene

Vorgänge auf Datenebene werden in [Azure-Ressourcenprotokollen für Queue Storage](monitor-blob-storage.md#analyzing-logs) erfasst. Sie können die [Diagnoseeinstellung](monitor-blob-storage.md#send-logs-to-azure-log-analytics) so konfigurieren, dass die Protokolle in den Log Analytics-Arbeitsbereich exportiert werden und so eine native Abfrage ermöglicht wird.

Mit der folgenden Log Analytics-Abfrage werden die Informationen zum „Wann“, „Wer“, „Was“ und „Wie“ in einer Liste von Protokolleinträgen abgerufen.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

Für das „Wann“ der Überwachung wird im Feld `TimeGenerated` der Zeitpunkt angezeigt, an dem der Protokolleintrag erfasst wurde.

Für das „Was“ der Überwachung wird im Feld `Uri` das Element angezeigt, das geändert oder gelesen wurde.

Für das „Wie“ der Überwachung wird im Feld `OperationName` der Vorgang angezeigt, der ausgeführt wurde.

Für das „Wer“ der Überwachung wird in `AuthenticationType` der Authentifizierungstyp angezeigt, der für eine Anforderung verwendet wurde. In diesem Feld können alle von Azure Storage unterstützten Authentifizierungstypen angezeigt werden, z. B. die Verwendung eines Kontoschlüssels oder eines SAS-Tokens sowie die Azure Active Directory-Authentifizierung (Azure AD).

#### <a name="identifying-the-security-principal-used-to-authorize-a-request"></a>Identifizierung des Sicherheitsprinzipals, der zur Autorisierung einer Anforderung verwendet wird

Wurde eine Anforderung über Azure AD authentifiziert, stellt das Feld `RequesterObjectId` die zuverlässigste Methode zur Identifizierung des Sicherheitsprinzipals dar. Sie können den Anzeigenamen des Sicherheitsprinzipals ermitteln, indem Sie mithilfe des Werts in Feld `RequesterObjectId` auf der Azure AD-Seite im Azure-Portal nach dem Sicherheitsprinzipal suchen. Im folgenden Screenshot sehen Sie ein Suchergebnis in Azure AD:

> [!div class="mx-imgBorder"]
> ![Durchsuchen von Azure Active Directory](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

In einigen Fällen wird in den Protokollen möglicherweise ein Benutzerprinzipalname (*UPN*, User Principal Name) angezeigt. Ist der Sicherheitsprinzipal beispielsweise ein Azure AD-Benutzer, wird wahrscheinlich der UPN angezeigt. Bei anderen Typen von Sicherheitsprinzipalen, z. B. benutzerseitig zugewiesene verwaltete Identitäten, oder in bestimmten Szenarien, z. B. bei der mandantenübergreifenden Azure AD-Authentifizierung, wird der UPN nicht in den Protokollen angezeigt.

Mit dieser Abfrage werden alle von OAuth-Sicherheitsprinzipalen ausgeführten Schreibvorgänge angezeigt.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "GetBlob"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

Die Authentifizierung über gemeinsam verwendete Schlüssel und die SAS-Authentifizierung ermöglichen keine Überwachung einzelner Identitäten. Wenn Sie also die Möglichkeit der identitätsbasierten Überwachung verbessern möchten, sollten Sie auf Azure AD umsteigen und die Authentifizierung über gemeinsam verwendete Schlüssel sowie die SAS-Authentifizierung verhindern. Informationen zum Verhindern dieser beiden Authentifizierungstypen finden Sie unter [Verhindern der Autorisierung mit gemeinsam verwendeten Schlüsseln für ein Azure Storage-Konto](../common/shared-key-authorization-prevent.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=portal). Die ersten Schritte mit Azure AD finden Sie unter [Zugriff auf Blobs mit Azure Active Directory autorisieren](authorize-access-azure-active-directory.md).

#### <a name="identifying-the-sas-token-used-to-authorize-a-request"></a>Identifizierung des SAS-Tokens, das zur Autorisierung einer Anforderung verwendet wird

Sie können nach Vorgängen suchen, die mit einem SAS-Token autorisiert wurden. Diese Abfrage gibt zum Beispiel alle Schreibvorgänge zurück, die mit einem SAS-Token autorisiert wurden.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutBlob"
  and AuthenticationType == "SAS"
| project TimeGenerated, AuthenticationType, AuthenticationHash, OperationName, Uri
```

Aus Sicherheitsgründen erscheinen SAS-Tokens nicht in den Protokollen. Der SHA-256-Hash des SAS-Tokens wird jedoch im Feld `AuthenticationHash` angezeigt, das von dieser Abfrage zurückgegeben wird. 

Wenn Sie mehrere SAS-Tokens verteilt haben und wissen wollen, welche SAS-Tokens verwendet werden, müssen Sie jedes Ihrer SAS-Tokens in einen SHA-256-Hash konvertieren und dann diesen Hash mit dem Hash-Wert vergleichen, der in den Protokollen erscheint.

Dekodieren Sie zunächst jede SAS-Token-Zeichenkette. Das folgende Beispiel dekodiert eine SAS-Token-Zeichenfolge mithilfe von PowerShell.

```powershell
[uri]::UnescapeDataString("<SAS token goes here>")
```

Anschließend können Sie diese Zeichenfolge an das [Get-FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) PowerShell-Cmdlet übergeben. Ein Beispiel finden Sie unter [Beispiel 4: Berechnung des Hashwertes einer Zeichenkette](/powershell/module/microsoft.powershell.utility/get-filehash#example-4--compute-the-hash-of-a-string).

Alternativ können Sie die entschlüsselte Zeichenfolge an die Funktion [hash_sha256()](/azure/data-explorer/kusto/query/sha256hashfunction) als Teil einer Abfrage übergeben, wenn Sie Azure Daten Explorer verwenden.

SAS-Tokens enthalten keine Identitätsinformationen. Eine Möglichkeit, die Aktivitäten von Benutzern oder Organisationen zu verfolgen, besteht darin, eine Zuordnung von Benutzern oder Organisationen zu verschiedenen SAS-Token-Hashes zu erstellen.

## <a name="optimize-cost-for-infrequent-queries"></a>Optimieren der Kosten bei seltenem Durchführen von Abfragen

Sie können Protokolle in Log Analytics exportieren, um dort die umfassenden nativen Abfragefunktionen zu nutzen. Bei umfangreichen Transaktionen in Ihrem Speicherkonto können die Kosten für die Verwendung von Protokollen in Log Analytics beträchtlich sein. Weitere Informationen finden Sie unter [Azure Log Analytics – Preise](https://azure.microsoft.com/pricing/details/monitor/). Wenn Sie Protokolle nur gelegentlich abfragen möchten (z. B. zur Konformitätsüberwachung), können Sie die Gesamtkosten reduzieren, indem Sie die Protokolle in ein Speicherkonto exportieren und dann zusätzlich zu den Protokolldaten eine serverlose Abfragelösung verwenden, wie etwa Azure Synapse.

Mit Azure Synapse können Sie einen serverlosen SQL-Pool erstellen, um Protokolldaten bei Bedarf abzufragen. Dadurch lassen sich die Kosten erheblich senken.

1. Exportieren Sie die Protokolle in ein Speicherkonto. Weitere Informationen finden Sie unter [Erstellen von Diagnoseeinstellungen](monitor-blob-storage.md#creating-a-diagnostic-setting).

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

- [Überwachen von Azure Blob Storage](monitor-blob-storage.md)
- [Überwachen von Daten in Azure Blob Storage – Referenz](monitor-blob-storage-reference.md)
- [Tutorial: Verwenden von Kusto-Abfragen in Azure Data Explorer und Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)
- [Get started with log queries in Azure Monitor (Erste Schritte mit Abfragen in Azure Monitor)](../../azure-monitor/logs/get-started-queries.md)
