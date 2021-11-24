---
title: Integrieren von Azure Data Explorer für die langfristige Protokollaufbewahrung | Microsoft-Dokumentation
description: Senden Sie Microsoft Sentinel-Protokolle zur langfristigen Speicherung an Azure Daten-Explorer, um die Kosten für die Datenspeicherung zu senken.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06fa0559d2ce0796cd8031b06b8ff4034d943675
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523902"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integrieren von Azure Data Explorer für die langfristige Protokollaufbewahrung

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Standardmäßig werden in Microsoft Sentinel aufgenommene Protokolle in Azure Monitor Log Analytics gespeichert. In diesem Artikel wird erläutert, wie Sie die Aufbewahrungskosten in Microsoft Sentinel reduzieren können, indem Sie sie zur langfristigen Aufbewahrung an Azure Daten-Explorer senden.

Das Speichern von Protokollen in Azure Data Explorer reduziert die Kosten, aber bewahrt gleichzeitig die Möglichkeit, Ihre Daten abzufragen. Diese Option ist besonders nützlich, wenn Ihre Daten zunehmen. Während Sicherheitsdaten beispielsweise im Lauf der Zeit an Wert verlieren können, müssen Protokolle möglicherweise aufgrund gesetzlicher Vorgaben oder für die regelmäßige Untersuchung älterer Daten aufbewahrt werden.

## <a name="about-azure-data-explorer"></a>Informationen zu Azure Data Explorer

Azure Data Explorer ist eine Big Data-Analyseplattform, die speziell für Protokoll- und Datenanalysen optimiert ist. Da Azure Daten-Explorer die Kusto Query Language (KQL) als Abfragesprache verwendet, ist es eine gute Alternative für die Microsoft Sentinel-Datenspeicherung. Wenn Sie Azure Daten-Explorer für Ihre Datenspeicherung verwenden, können Sie plattformübergreifende Abfragen durchführen und Daten sowohl in Azure Data Explorer als auch in Microsoft Sentinel visualisieren.

Weitere Informationen finden Sie in der [Dokumentation zu Azure Data Explorer](/azure/data-explorer/) und im [Blog](https://azure.microsoft.com/blog/tag/azure-data-explorer/).

### <a name="when-to-integrate-with-azure-data-explorer"></a>Anwendungsfälle für die Integration in Azure Data Explorer

Microsoft Sentinel bietet umfassende SIEM- und SOAR-Funktionen, eine schnelle Bereitstellung und Konfiguration sowie erweiterte, integrierte Sicherheitsfunktionen für SOC-Teams. Der Wert der Speicherung von Sicherheitsdaten in Microsoft Sentinel kann jedoch nach einigen Monaten sinken, wenn die SOC-Benutzer nicht mehr so oft auf diese Daten zugreifen müssen wie auf neuere Daten.

Wenn Sie nur gelegentlich auf bestimmte Tabellen zugreifen müssen, z. B. für regelmäßige Untersuchungen oder Audits, ist die Aufbewahrung Ihrer Daten in Microsoft Sentinel möglicherweise nicht mehr wirtschaftlich. An dieser Stelle empfehlen wir die Speicherung von Daten im Azure Daten-Explorer, der weniger kostet, aber dennoch die gleichen KQL-Abfragen ermöglicht, die Sie in Microsoft Sentinel ausführen.

Sie können auf die Daten im Azure Daten-Explorer direkt von Microsoft Sentinel aus zugreifen, indem Sie die [Log Analytics Azure Data Explorer Proxy-Funktion](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md) verwenden. Dazu verwenden Sie clusterübergreifende Abfragen in Ihrer Protokollsuche oder in Arbeitsmappen.

> [!IMPORTANT]
> Einige wichtige SIEM-Funktionen wie Analyseregeln, UEBA und das Untersuchungsdiagramm unterstützen in Azure Data Explorer gespeicherten Daten nicht.
>

> [!NOTE]
> Die Integration in Azure Data Explorer bietet Ihnen auch die vollständige Kontrolle und Granularität Ihrer Daten. Weitere Informationen finden Sie unter [Überlegungen zum Entwurf](#design-considerations).
>
## <a name="send-data-directly-to-microsoft-sentinel-and-azure-data-explorer-in-parallel"></a>Daten direkt und parallel an Microsoft Sentinel und Azure Daten-Explorer senden

Möglicherweise möchten Sie alle Daten mit *Sicherheitswert* in Microsoft Sentinel aufbewahren, um sie für Erkennungen, Vorfalluntersuchungen, Bedrohungssuche, UEBA usw. zu verwenden. Die Aufbewahrung dieser Daten in Microsoft Sentinel kommt vor allem den Nutzern des Security Operations Center (SOC) zugute, bei denen in der Regel eine Speicherung von 3-12 Monaten ausreicht.

Sie können darüber hinaus konfigurieren, dass sämtliche Daten *unabhängig von ihrer Sicherheitsrelevanz* gleichzeitig auch an Azure Data Explorer gesendet werden, wo sie länger gespeichert werden können. Während das gleichzeitige Senden von Daten an Microsoft Sentinel und Azure Daten-Explorer zu einer gewissen Duplizierung führt, können die Kosteneinsparungen erheblich sein, da Sie die Aufbewahrungskosten in Microsoft Sentinel reduzieren.

> [!TIP]
> Mit dieser Option können Sie auch Daten, die über verschiedene Datenspeicher verteilt sind, korrelieren, z. B. um die in Microsoft Sentinel gespeicherten Sicherheitsdaten mit den in Azure Daten-Explorer gespeicherten operativen oder langfristigen Daten anzureichern. Weitere Informationen finden Sie unter [Ressourcenübergreifende Abfrage: Azure Data Explorer mithilfe von Azure Monitor](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md).
>

Die folgende Abbildung zeigt, wie Sie alle Ihre Daten in Azure Daten-Explorer aufbewahren können, während Sie nur Ihre Sicherheitsdaten zur täglichen Verwendung an Microsoft Sentinel senden.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Daten parallel in Azure Daten-Explorer und Microsoft Sentinel speichern.":::

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
|**Standortanforderungen**     |   Sowohl der Azure Monitor-/Microsoft Sentinel-Arbeitsbereich als auch der Zielort (ein Azure Storage Account oder Event Hub) müssen sich in der gleichen geografischen Region befinden.      |
|**Unterstützte Tabellen**     | Nicht alle Tabellen werden für den Export unterstützt, benutzerdefinierte Protokolltabellen etwa nicht. <br><br>Weitere Informationen finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor](../azure-monitor/logs/logs-data-export.md) in der Liste [Unterstützte Tabellen](../azure-monitor/logs/logs-data-export.md#supported-tables).         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Methoden und Verfahren für den Datenexport

Verwenden Sie eines der folgenden Verfahren, um Daten aus Microsoft Sentinel in Azure Daten-Explorer zu exportieren:

- **Über Azure Event Hub:** Exportieren Sie Daten aus Log Analytics in einen Event Hub, von wo aus sie in Azure Data Explorer erfasst werden können. Diese Methode speichert einige Daten (die ersten X Monate) sowohl in Microsoft Sentinel als auch in Azure Daten-Explorer.

- **Über Azure Storage und Azure Data Factory:** Exportieren Sie die Daten aus Log Analytics in Azure Blob Storage. Führen Sie anschließend mit Azure Data Factory einen regelmäßigen Kopierauftrag aus, um die Daten weiter in Azure Data Explorer zu exportieren. Diese Methode ermöglicht es Ihnen, Daten aus Azure Data Factory nur dann zu kopieren, wenn sie sich ihrem Aufbewahrungslimit in Microsoft Sentinel / Log Analytics nähern, um eine Duplizierung zu vermeiden.

### <a name="azure-event-hub"></a>[Azure Event Hub](#tab/adx-event-hub)

In diesem Abschnitt wird beschrieben, wie Sie Microsoft Sentinel-Daten aus Log Analytics in einen Event Hub exportieren, von wo aus Sie sie in Azure Daten-Explorer einlesen können. Ähnlich wie beim [direkten parallelen Senden von Daten an Microsoft Sentinel und Azure Data Explorer](#send-data-directly-to-microsoft-sentinel-and-azure-data-explorer-in-parallel) beinhaltet diese Methode eine gewisse Datenduplizierung, da die Daten in Azure Daten-Explorer gestreamt werden, sobald sie in Log Analytics ankommen.

Die folgende Abbildung zeigt einen Beispielflow exportierter Daten in einen Event Hub, von wo aus sie in Azure Data Explorer erfasst werden.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Exportieren von Daten in Azure Data Explorer über Azure Event Hub":::

Die in der vorherigen Abbildung gezeigte Architektur bietet das volle Microsoft Sentinel SIEM-Erlebnis, einschließlich Incident Management, visuelle Untersuchungen, Threat Hunting, erweiterte Visualisierungen, UEBA und mehr, für Daten, auf die häufig zugegriffen werden muss, alle *X* Monate. Gleichzeitig ermöglicht diese Architektur auch die Abfrage langfristiger Daten durch direkten Zugriff im Azure Daten-Explorer oder über Microsoft Sentinel dank der Proxy-Funktion des Azure Data Explorer. Abfragen zur langfristigen Datenspeicherung in Azure Daten-Explorer können ohne Änderungen von Microsoft Sentinel auf Azure Data Explorer portiert werden.

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

    Eine Aktualisierungsrichtlinie, die einer Funktion ähnelt, die auf alle neuen Daten angewendet wird, wird verwendet, um die erweiterten Daten in die endgültige Tabelle aufzunehmen, die dasselbe Schema wie die Originaltabelle in Microsoft Sentinel hat.

    Legen Sie die Aufbewahrungsdauer für die Rohdatentabelle auf **0** Tage fest. Die Daten werden nur in der formatierten Tabelle gespeichert und nach ihrer Transformation sofort in der Rohdatentabelle gelöscht.

    Weitere Informationen finden Sie unter [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Erstellen Sie eine Tabellenzuordnung.** Ordnen Sie die JSON-Tabellen zu, um die Erfassung der Datensätze vom Event Hub in der Rohdatentabelle zu definieren. Weitere Informationen finden Sie unter [Erstellen der Updaterichtlinie für Metrik- und Protokolldaten](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Erstellen Sie eine Updaterichtlinie, und fügen Sie sie an die Tabelle mit den unformatierten Datensätzen an.** In diesem Schritt erstellen Sie eine Funktion, die als Updaterichtlinie bezeichnet wird, und fügen sie an die Zieltabelle an, damit die Daten zum Zeitpunkt der Erfassung transformiert werden.

    > [!NOTE]
    > Dieser Schritt ist nur erforderlich, wenn Sie Datentabellen in Azure Daten-Explorer mit demselben Schema und Format wie in Microsoft Sentinel haben möchten.
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

In diesem Abschnitt wird beschrieben, wie Sie Microsoft Sentinel-Daten aus Log Analytics in Azure Storage exportieren, wo Azure Data Factory einen regulären Job ausführen kann, um die Daten in Azure Daten-Explorer zu exportieren.

Die Verwendung von Azure Storage und Azure Data Factory ermöglicht es Ihnen, Daten aus Azure Storage nur dann zu kopieren, wenn sie sich dem Aufbewahrungslimit in Microsoft Sentinel / Log Analytics nähern. Es gibt keine Datenduplizierung und Azure Daten-Explorer wird *nur* verwendet, um auf Daten zuzugreifen, die älter als das Aufbewahrungslimit in Microsoft Sentinel sind.

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

    Eine Aktualisierungsrichtlinie, die einer Funktion ähnelt, die auf alle neuen Daten angewendet wird, wird verwendet, um die erweiterten Daten in die endgültige Tabelle aufzunehmen, die dasselbe Schema wie die Originaltabelle in Microsoft Sentinel hat.

    Legen Sie die Aufbewahrungsdauer für die Rohdatentabelle auf **0** Tage fest. Die Daten werden nur in der formatierten Tabelle gespeichert und nach ihrer Transformation sofort in der Rohdatentabelle gelöscht.

    Weitere Informationen finden Sie unter [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Erstellen Sie eine Tabellenzuordnung.** Ordnen Sie die JSON-Tabellen zu, um die Erfassung der Datensätze vom Event Hub in der Rohdatentabelle zu definieren. Weitere Informationen finden Sie unter [Erstellen der Updaterichtlinie für Metrik- und Protokolldaten](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Erstellen Sie eine Updaterichtlinie, und fügen Sie sie an die Tabelle mit den unformatierten Datensätzen an.** In diesem Schritt erstellen Sie eine Funktion, die als Updaterichtlinie bezeichnet wird, und fügen sie an die Zieltabelle an, damit die Daten zum Zeitpunkt der Erfassung transformiert werden.

    > [!NOTE]
    > Dieser Schritt ist nur erforderlich, wenn Sie Datentabellen in Azure Daten-Explorer mit demselben Schema und Format wie in Microsoft Sentinel haben möchten.
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

Wenn Sie Ihre Microsoft Sentinel-Daten in Azure Daten-Explorer speichern, sollten Sie die folgenden Elemente berücksichtigen:

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

Unabhängig davon, wo Sie Ihre Daten speichern, setzen Sie die Jagd und Untersuchung mit Microsoft Sentinel fort.

Weitere Informationen finden Sie unter

- [Tutorial: Untersuchen von Vorfällen mit Microsoft Sentinel](investigate-cases.md)
- [Jagd auf Bedrohungen mit Microsoft Sentinel](hunting.md)
