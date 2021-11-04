---
title: Integrieren von Azure Data Explorer für die langfristige Protokollaufbewahrung | Microsoft-Dokumentation
description: Senden Sie Azure Sentinel-Protokolle zur Langzeitaufbewahrung an Azure Data Explorer, um die Kosten für die Datenspeicherung zu senken.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: f906502ce03ffcad42f07cd951c5944eb3e55501
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009090"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integrieren von Azure Data Explorer für die langfristige Protokollaufbewahrung

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In Azure Sentinel erfasste Protokolle werden standardmäßig in Azure Monitor Log Analytics gespeichert. In diesem Artikel wird erläutert, wie Sie die Aufbewahrungskosten in Azure Sentinel senken, indem Sie sie zur Langzeitaufbewahrung an Azure Data Explorer senden.

Das Speichern von Protokollen in Azure Data Explorer reduziert die Kosten, aber bewahrt gleichzeitig die Möglichkeit, Ihre Daten abzufragen. Diese Option ist besonders nützlich, wenn Ihre Daten zunehmen. Während Sicherheitsdaten beispielsweise im Lauf der Zeit an Wert verlieren können, müssen Protokolle möglicherweise aufgrund gesetzlicher Vorgaben oder für die regelmäßige Untersuchung älterer Daten aufbewahrt werden.

## <a name="about-azure-data-explorer"></a>Informationen zu Azure Data Explorer

Azure Data Explorer ist eine Big Data-Analyseplattform, die speziell für Protokoll- und Datenanalysen optimiert ist. Da Azure Data Explorer als Abfragesprache die Kusto Query Language (KQL) verwendet, stellt er eine gute Alternative zur Datenspeicherung in Azure Sentinel dar. Mit Azure Data Explorer als Datenspeicher können Sie plattformübergreifende Abfragen ausführen und Daten sowohl in Azure Data Explorer als auch in Azure Sentinel visualisieren.

Weitere Informationen finden Sie in der [Dokumentation zu Azure Data Explorer](/azure/data-explorer/) und im [Blog](https://azure.microsoft.com/blog/tag/azure-data-explorer/).

### <a name="when-to-integrate-with-azure-data-explorer"></a>Anwendungsfälle für die Integration in Azure Data Explorer

Azure Sentinel bietet vollständige SIEM- und SOAR-Funktionen, schnelle Bereitstellung und Konfiguration sowie erweiterte, integrierte Sicherheitsfeatures für SOC-Teams. Allerdings kann die Speicherung der Sicherheitsdaten in Azure Sentinel nach einigen Monaten nachteilig werden, wenn SOC-Benutzer nicht mehr so oft darauf zugreifen müssen wie auf neuere Daten.

Wenn Sie auf bestimmte Tabellen nur noch gelegentlich zugreifen müssen, z. B. für regelmäßige Untersuchungen oder Überwachungen, ist die Aufbewahrung der Daten in Azure Sentinel nicht mehr kostengünstig. Es wird dann empfohlen, die Daten in Azure Data Explorer zu speichern. Dies kostet weniger, ermöglicht aber dennoch die gleichen KQL-Abfragen, die Sie auch in Azure Sentinel ausführen.

Sie können mithilfe des [Azure Data Explorer-Proxyfeatures für Log Analytics](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md) direkt aus Azure Sentinel auf die Daten in Azure Data Explorer zugreifen. Dazu verwenden Sie clusterübergreifende Abfragen in Ihrer Protokollsuche oder in Arbeitsmappen.

> [!IMPORTANT]
> Einige wichtige SIEM-Funktionen wie Analyseregeln, UEBA und das Untersuchungsdiagramm unterstützen in Azure Data Explorer gespeicherten Daten nicht.
>

> [!NOTE]
> Die Integration in Azure Data Explorer bietet Ihnen auch die vollständige Kontrolle und Granularität Ihrer Daten. Weitere Informationen finden Sie unter [Überlegungen zum Entwurf](#design-considerations).
>
## <a name="send-data-directly-to-azure-sentinel-and-azure-data-explorer-in-parallel"></a>Gleichzeitiges Senden von Daten an Azure Sentinel und Azure Data Explorer

Möglicherweise möchten Sie alle Daten, die für die *Sicherheit wichtig* sind, in Azure Sentinel aufbewahren, um sie für Erkennungen, Incidentuntersuchungen, die Bedrohungssuche, UEBA usw. einzusetzen. Von der Speicherung dieser Daten in Azure Sentinel profitieren in erster Linie SOC-Benutzer (Security Operations Center), denen in der Regel ein Aufbewahrungszeitraum von 3–12 Monaten ausreicht.

Sie können darüber hinaus konfigurieren, dass sämtliche Daten *unabhängig von ihrer Sicherheitsrelevanz* gleichzeitig auch an Azure Data Explorer gesendet werden, wo sie länger gespeichert werden können. Das gleichzeitige Senden von Daten an Azure Sentinel und Azure Data Explorer führt zu einer gewissen Duplizierung, die Kosteneinsparungen können aber erheblich sein, da Sie dadurch die Aufbewahrungskosten in Azure Sentinel reduzieren.

> [!TIP]
> Außerdem können Sie mit dieser Option auch Ihre Daten auf mehrere Datenspeicher verteilen, um z. B. die in Azure Sentinel gespeicherten Sicherheitsdaten mit operativen oder langfristig in Azure Data Explorer gespeicherten Daten zu erweitern. Weitere Informationen finden Sie unter [Ressourcenübergreifende Abfrage: Azure Data Explorer mithilfe von Azure Monitor](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md).
>

Die folgende Abbildung zeigt, wie Sie alle Ihre Daten in Azure Data Explorer beibehalten und gleichzeitig nur die Sicherheitsdaten für die tägliche Nutzung an Azure Sentinel senden können.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Paralleles Speichern von Daten in Azure Data Explorer und Azure Sentinel":::

Weitere Informationen zum Implementieren dieser Architekturoption finden Sie unter [Überwachung mit Azure Data Explorer](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer).

## <a name="export-data-from-log-analytics-into-azure-data-explorer"></a>Exportieren von Daten aus Log Analytics in Azure Data Explorer

Anstatt Ihre Daten direkt an Azure Data Explorer zu senden, können Sie sie auch über Azure Event Hub oder Azure Data Factory aus Log Analytics in Azure Data Explorer exportieren.

### <a name="data-export-architecture"></a>Datenexportarchitektur

Die folgende Abbildung zeigt einen Beispielflow exportierter Daten über die Azure Monitor-Erfassungspipeline. Ihre Daten werden standardmäßig an Log Analytics weitergeleitet, aber Sie können sie auch so konfigurieren, dass sie in ein Azure Storage-Konto oder einen Event Hub exportiert werden.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Exportieren von Daten aus Azure Monitor: Architektur":::

Wählen Sie beim Konfigurieren der Datenexportregeln die Protokolltypen aus, die Sie exportieren möchten. Nach der Konfiguration werden neue Daten, die am Log Analytics-Erfassungsendpunkt eintreffen und für die ausgewählten Tabellen in Ihrem Arbeitsbereich vorgesehen sind, in Ihr Speicherkonto oder Ihren Event Hub exportiert.

Beachten Sie beim Konfigurieren der Daten für den Export die folgenden Überlegungen:

|Aspekt  | Details |
|---------|---------|
|**Umfang der exportierten Daten**     |  Nachdem der Export für eine bestimmte Tabelle konfiguriert wurde, werden alle an diese Tabelle gesendeten Daten ohne Ausnahme exportiert. Das Exportieren einer gefilterten Teilmenge Ihrer Daten oder das Beschränken des Exports auf bestimmte Ereignisse wird nicht unterstützt.       |
|**Standortanforderungen**     |   Der Azure Monitor-/Azure Sentinel-Arbeitsbereich und der Zielstandort (Azure Storage-Konto oder Event Hub) müssen sich in derselben geografischen Region befinden.      |
|**Unterstützte Tabellen**     | Nicht alle Tabellen werden für den Export unterstützt, benutzerdefinierte Protokolltabellen etwa nicht. <br><br>Weitere Informationen finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor](../azure-monitor/logs/logs-data-export.md) in der Liste [Unterstützte Tabellen](../azure-monitor/logs/logs-data-export.md#supported-tables).         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Methoden und Verfahren für den Datenexport

Verwenden Sie eines der folgenden Verfahren, um Daten aus Azure Sentinel in Azure Data Explorer zu exportieren:

- **Über Azure Event Hub:** Exportieren Sie Daten aus Log Analytics in einen Event Hub, von wo aus sie in Azure Data Explorer erfasst werden können. Bei dieser Methode werden einige Daten (die ersten x Monate) sowohl in Azure Sentinel als auch in Azure Data Explorer gespeichert.

- **Über Azure Storage und Azure Data Factory:** Exportieren Sie die Daten aus Log Analytics in Azure Blob Storage. Führen Sie anschließend mit Azure Data Factory einen regelmäßigen Kopierauftrag aus, um die Daten weiter in Azure Data Explorer zu exportieren. Mit dieser Methode können Sie Daten erst dann aus Azure Data Factory kopieren, wenn sie sich dem Ende des Aufbewahrungszeitraums in Azure Sentinel/Log Analytics nähern. Damit vermeiden Sie eine Duplizierung.

### <a name="azure-event-hub"></a>[Azure Event Hub](#tab/adx-event-hub)

In diesem Abschnitt wird beschrieben, wie Sie Azure Sentinel-Daten aus Log Analytics in einen Event Hub exportieren, von wo aus sie in Azure Data Explorer erfasst werden können. Ähnlich wie beim [parallelen Senden von Daten an Azure Sentinel und Azure Data Explorer](#send-data-directly-to-azure-sentinel-and-azure-data-explorer-in-parallel) tritt bei dieser Methode eine Datenduplizierung auf, da die Daten direkt an Azure Data Explorer gestreamt werden, wenn sie in Log Analytics eintreffen.

Die folgende Abbildung zeigt einen Beispielflow exportierter Daten in einen Event Hub, von wo aus sie in Azure Data Explorer erfasst werden.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Exportieren von Daten in Azure Data Explorer über Azure Event Hub":::

Die in der vorherigen Abbildung gezeigte Architektur bietet die vollständige SIEM-Umgebung von Azure Sentinel, einschließlich Incidentmanagement, visuelle Untersuchungen, Bedrohungssuche, erweiterter Visualisierungen, UEBA und mehr, für Daten, auf die alle *X* Monate zugegriffen werden muss. Gleichzeitig können Sie mit dieser Architektur auch langfristig gespeicherte Daten abfragen, indem Sie direkt in Azure Data Explorer oder mithilfe des Azure Data Explorer-Proxyfeatures über Azure Sentinel darauf zugreifen. Abfragen des langfristigen Datenspeichers in Azure Data Explorer können ohne Änderungen aus Azure Sentinel in Azure Data Explorer übertragen werden.

> [!NOTE]
> Beachten Sie beim Exportieren mehrerer Datentabellen in Azure Data Explorer über Event Hub, dass für den Log Analytics-Datenexport Einschränkungen hinsichtlich der maximalen Anzahl von Event Hubs pro Namespace gelten. Weitere Informationen zum Exportieren von Daten finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor](../azure-monitor/logs/logs-data-export.md?tabs=portal).
>
> Für die meisten Kunden wird die Verwendung des Standard-Tarifs von Event Hub empfohlen. Abhängig von der Menge der Tabellen, die Sie exportieren müssen, und vom Umfang des Datenverkehrs zu diesen Tabellen müssen Sie möglicherweise den Event Hub-Tarif Dedicated verwenden. Weitere Informationen finden Sie in der [Dokumentation zu Event Hubs](../event-hubs/event-hubs-quotas.md).
>

> [!TIP]
> Weitere Informationen zu diesem Verfahren finden Sie unter [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](/azure/data-explorer/ingest-data-no-code).
>

**So exportieren Sie Daten über einen Event Hub in Azure Data Explorer**

1. **Konfigurieren Sie den Log Analytics-Datenexport in einen Event Hub.** Weitere Informationen finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor](../azure-monitor/logs/logs-data-export.md).

1. **Erstellen eines Azure Data Explorer-Clusters und einer Datenbank** Weitere Informationen finden Sie unter:

    - [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](/azure/data-explorer/create-cluster-database-portal)
    - [Auswählen der passenden Compute-SKU für Ihren Azure Data Explorer-Cluster](/azure/data-explorer/manage-cluster-choose-sku)

1. **Erstellen Sie die Zieltabellen.** Die Rohdaten werden zunächst in einer Zwischentabelle erfasst und dort gespeichert, bearbeitet und erweitert.

    Mithilfe einer Updaterichtlinie, ähnlich einer Funktion, die auf alle neuen Daten angewandt wird, werden die erweiterten Daten in der endgültigen Tabelle erfasst. Diese weist das gleiche Schema wie die ursprüngliche Tabelle in Azure Sentinel auf.

    Legen Sie die Aufbewahrungsdauer für die Rohdatentabelle auf **0** Tage fest. Die Daten werden nur in der formatierten Tabelle gespeichert und nach ihrer Transformation sofort in der Rohdatentabelle gelöscht.

    Weitere Informationen finden Sie unter [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Erstellen Sie eine Tabellenzuordnung.** Ordnen Sie die JSON-Tabellen zu, um die Erfassung der Datensätze vom Event Hub in der Rohdatentabelle zu definieren. Weitere Informationen finden Sie unter [Erstellen der Updaterichtlinie für Metrik- und Protokolldaten](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Erstellen Sie eine Updaterichtlinie, und fügen Sie sie an die Tabelle mit den unformatierten Datensätzen an.** In diesem Schritt erstellen Sie eine Funktion, die als Updaterichtlinie bezeichnet wird, und fügen sie an die Zieltabelle an, damit die Daten zum Zeitpunkt der Erfassung transformiert werden.

    > [!NOTE]
    > Dieser Schritt ist nur erforderlich, wenn die Datentabellen in Azure Data Explorer das gleiche Schema und Format wie in Azure Sentinel haben sollen.
    >

    Weitere Informationen finden Sie unter [Verbinden eines Event Hubs mit Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Erstellen Sie eine Datenverbindung zwischen dem Event Hub und der Rohdatentabelle in Azure Data Explorer.** Konfigurieren Sie in Azure Data Explorer die Details zum Exportieren der Daten in den Event Hub.

    Befolgen Sie die Anweisungen in der [Dokumentation zu Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs), und geben Sie die folgenden Informationen an:

    - **Ziel**: Geben Sie die Tabelle mit den Rohdaten an.
    - **Format:** Geben Sie als Tabellenformat `.json` an.
    - **Anzuwendende Zuordnung:** Geben Sie die in [Schritt 4](#mapping) erstellte Zuordnungstabelle an.


1. **Ändern Sie die Aufbewahrungsdauer für die Zieltabelle.** Die [Standardaufbewahrungsrichtlinie von Azure Data Explorer](/azure/data-explorer/kusto/management/retentionpolicy) gibt möglicherweise einen viel längeren Zeitraum als erforderlich an.

    Mit dem folgenden Befehl können Sie die Aufbewahrungsrichtlinie in ein Jahr ändern:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure Storage/Azure Data Factory](#tab/azure-storage-azure-data-factory)

In diesem Abschnitt wird beschrieben, wie Azure Sentinel-Daten aus Log Analytics in Azure Storage exportiert werden. Von dort aus kann Azure Data Factory einen regulären Auftrag ausführen, um die Daten in Azure Data Explorer zu exportieren.

Wenn Sie Azure Storage und Azure Data Factory verwenden, können Sie Daten auch nur dann aus Azure Storage kopieren, wenn das Ende des Aufbewahrungszeitraums in Azure Sentinel/Log Analytics bald erreicht ist. Dadurch kommt es nicht zu einer Datenduplizierung, und Azure Data Explorer wird *nur* für den Zugriff auf Daten verwendet, die älter als das Aufbewahrungslimit in Azure Sentinel sind.

> [!TIP]
> Die Architektur für die Verwendung von Azure Storage und Azure Data Factory für Ihre Legacydaten ist zwar komplexer, aber Sie können durch diese Methode insgesamt größere Kosteneinsparungen erzielen.
>
Die folgende Abbildung zeigt einen Beispielflow exportierter Daten in Azure Storage. Dort führt Azure Data Factory einen regelmäßigen Auftrag aus, um sie weiter in Azure Data Explorer zu exportieren.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Exportieren von Daten in Azure Data Explorer über Azure Storage und Azure Data Factory":::

**So exportieren Sie Daten in Azure Data Explorer über Azure Storage und Azure Data Factory**

1. **Konfigurieren Sie den Log Analytics-Datenexport in einen Event Hub.** Weitere Informationen finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor](../azure-monitor/logs/logs-data-export.md?tabs=portal#enable-data-export).

1. **Erstellen eines Azure Data Explorer-Clusters und einer Datenbank** Weitere Informationen finden Sie unter:

    - [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](/azure/data-explorer/create-cluster-database-portal)
    - [Auswählen der passenden Compute-SKU für Ihren Azure Data Explorer-Cluster](/azure/data-explorer/manage-cluster-choose-sku)

1. **Erstellen Sie die Zieltabellen.** Die Rohdaten werden zunächst in einer Zwischentabelle erfasst und dort gespeichert, bearbeitet und erweitert.

    Mithilfe einer Updaterichtlinie, ähnlich einer Funktion, die auf alle neuen Daten angewandt wird, werden die erweiterten Daten in der endgültigen Tabelle erfasst. Diese weist das gleiche Schema wie die ursprüngliche Tabelle in Azure Sentinel auf.

    Legen Sie die Aufbewahrungsdauer für die Rohdatentabelle auf **0** Tage fest. Die Daten werden nur in der formatierten Tabelle gespeichert und nach ihrer Transformation sofort in der Rohdatentabelle gelöscht.

    Weitere Informationen finden Sie unter [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Erstellen Sie eine Tabellenzuordnung.** Ordnen Sie die JSON-Tabellen zu, um die Erfassung der Datensätze vom Event Hub in der Rohdatentabelle zu definieren. Weitere Informationen finden Sie unter [Erstellen der Updaterichtlinie für Metrik- und Protokolldaten](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Erstellen Sie eine Updaterichtlinie, und fügen Sie sie an die Tabelle mit den unformatierten Datensätzen an.** In diesem Schritt erstellen Sie eine Funktion, die als Updaterichtlinie bezeichnet wird, und fügen sie an die Zieltabelle an, damit die Daten zum Zeitpunkt der Erfassung transformiert werden.

    > [!NOTE]
    > Dieser Schritt ist nur erforderlich, wenn die Datentabellen in Azure Data Explorer das gleiche Schema und Format wie in Azure Sentinel haben sollen.
    >

    Weitere Informationen finden Sie unter [Verbinden eines Event Hubs mit Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Erstellen Sie eine Datenverbindung zwischen dem Event Hub und der Rohdatentabelle in Azure Data Explorer.** Konfigurieren Sie in Azure Data Explorer die Details zum Exportieren der Daten in den Event Hub.

    Befolgen Sie die Anweisungen in der [Dokumentation zu Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs), und geben Sie die folgenden Informationen an:

    - **Ziel**: Geben Sie die Tabelle mit den Rohdaten an.
    - **Format:** Geben Sie als Tabellenformat `.json` an.
    - **Anzuwendende Zuordnung:** Geben Sie die in [Schritt 4](#mapping) erstellte Zuordnungstabelle an.

1. **Richten Sie die Azure Data Factory-Pipeline ein:**

    - Erstellen Sie verknüpfte Dienste für Azure Storage und Azure Data Explorer. Weitere Informationen finden Sie unter:

        - [Kopieren und Transformieren von Daten in Azure Blob Storage mithilfe von Azure Data Factory](../data-factory/connector-azure-blob-storage.md)
        - [Kopieren von Daten in oder aus Azure Data Explorer mithilfe von Azure Data Factory](../data-factory/connector-azure-data-explorer.md)

    - Erstellen Sie ein Dataset aus Azure Storage. Weitere Informationen finden Sie unter [Datasets in Azure Data Factory](../data-factory/concepts-datasets-linked-services.md).

    - Erstellen Sie basierend auf den **LastModifiedDate**-Eigenschaften eine Datenpipeline mit einem Kopiervorgang.

        Weitere Informationen finden Sie unter [Kopieren neuer und geänderter Dateien nach **LastModifiedDate** mit Azure Data Factory](../data-factory/solution-template-copy-new-files-lastmodifieddate.md).

---

## <a name="design-considerations"></a>Überlegungen zum Entwurf

Berücksichtigen Sie beim Speichern Ihrer Azure Sentinel-Daten in Azure Data Explorer Folgendes:

|Aspekt  |Beschreibung  |
|---------|---------|
|**Clustergröße und SKU**     | Planen Sie die Anzahl der Knoten und die VM-SKU in Ihrem Cluster sorgfältig. Diese Faktoren bestimmen die Verarbeitungsleistung und die Größe Ihres Caches für heiße Daten (SSD und Arbeitsspeicher). Je größer der Cache ist, desto mehr Daten können Sie mit höherer Leistung abfragen. <br><br>Es wird empfohlen, den [Azure Data Explorer-Größenrechner](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) zu verwenden, mit dem Sie verschiedene Konfigurationen durchspielen und die resultierenden Kosten einsehen können. <br><br>Azure Data Explorer verfügt auch über eine Funktion zur Autoskalierung, die intelligente Entscheidungen trifft, um Knoten nach Bedarf basierend auf der Clusterlast hinzuzufügen und zu entfernen. Weitere Informationen finden Sie unter [Verwalten des horizontalen Skalierens (Aufskalieren) eines Clusters in Azure Data Explorer zur Anpassung an sich ändernden Bedarf](/azure/data-explorer/manage-cluster-horizontal-scaling).      |
|**Cache für heiße/kalte Daten**     | Azure Data Explorer ermöglicht die Steuerung der Datentabellen im Cache für heiße Daten, damit Ergebnisse schneller zurückgegeben werden können. Wenn Sie in Ihrem Azure Data Explorer-Cluster über große Datenmengen verfügen, sollten Sie die Tabellen nach Monaten unterteilen, um die Granularität der Daten in Ihrem Cache für heiße Daten zu erhöhen. <br><br>Weitere Informationen finden Sie unter [Cache-Richtlinie (Cache für heiße und kalte Daten)](/azure/data-explorer/kusto/management/cachepolicy).       |
|**Vermerkdauer**     |   In Azure Data Explorer können Sie festlegen, wann Daten aus einer Datenbank oder einer einzelnen Tabelle entfernt werden. Dies ist ein wichtiger Beitrag zur Begrenzung der Speicherkosten. <br><br> Weitere Informationen finden Sie unter [Aufbewahrungsrichtlinie](/azure/data-explorer/kusto/management/retentionpolicy).       |
|**Security**     |  Es gibt verschiedene Azure Data Explorer-Einstellungen wie z. B. Identitätsverwaltung, Verschlüsselung usw., die Ihnen dabei helfen, Ihre Daten zu schützen. Insbesondere kann Azure Data Explorer mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) so konfiguriert werden, dass der Zugriff auf Datenbanken, Tabellen oder sogar Zeilen innerhalb einer Tabelle eingeschränkt wird. Weitere Informationen finden Sie unter [Sicherheit in Azure Data Explorer](/azure/data-explorer/security) und [Sicherheit auf Zeilenebene](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy).|
|**Datenfreigabe**     |   Mit Azure Data Explorer können Sie einzelne Datenelemente für andere Parteien, z. B. Partner oder Anbieter, verfügbar machen und sogar Daten von anderen Parteien erwerben. Weitere Informationen finden Sie unter [Verwenden von Azure Data Share zum Freigeben von Daten für Azure Data Explorer](/azure/data-explorer/data-share).      |
| **Weitere Kostenkomponenten** | Berücksichtigen Sie die anderen Kostenkomponenten für die folgenden Methoden: <br><br>**Exportieren von Daten über einen Azure Event Hub:** <br>- Log Analytics-Datenexportkosten (Berechnung nach exportierten GB) <br>- Event Hub-Kosten (Berechnung nach Durchsatzeinheit)  <br><br>**Exportieren von Daten über Azure Storage und Azure Data Factory:** <br>- Log Analytics-Datenexportkosten (Berechnung nach exportierten GB) <br>- Azure Storage (Berechnung nach gespeicherten GB) <br>- Azure Data Factory (Berechnung nach Kopien der ausgeführten Aktivitäten)
|     |         |

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie unabhängig davon, wo Sie Ihre Daten speichern, mit dem Hunting und der Untersuchung mit Azure Sentinel fort.

Weitere Informationen finden Sie unter:

- [Tutorial: Untersuchen von Vorfällen mit Azure Sentinel](investigate-cases.md)
- [Suchen nach Bedrohungen mit Azure Sentinel](hunting.md)
