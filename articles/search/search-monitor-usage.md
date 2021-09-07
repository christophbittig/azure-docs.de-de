---
title: Überwachen von Vorgängen und Aktivitäten
titleSuffix: Azure Cognitive Search
description: Aktivieren der Protokollierung, Abrufen von Abfrageaktivitätsmetriken, der Ressourcennutzung und anderer Daten von einem Dienst der kognitiven Azure-Suche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5e9e09508591e2b6e58b6bae17df281c23a5129d
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414789"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Überwachen von Vorgängen und Aktivitäten von Azure Cognitive Search

Dieser Artikel bietet eine Übersicht über die Überwachungskonzepte und -tools für Azure Cognitive Search. Für eine ganzheitliche Überwachung sollten Sie eine Kombination aus integrierten Funktionen und ergänzenden Diensten wie Azure Monitor verwenden.

Alles in allem können Sie Folgendes nachverfolgen:

* Suchdienst: Integrität und Änderungen an der Dienstkonfiguration.
* Speicherverbrauch: Verwendet und verfügbar.
* Objektgrenzwerte für Indizes, Indexer und andere Objekte mit Zählern für jeden Typ, relativ zum [zulässigen Höchstwert](search-limits-quotas-capacity.md) für die Dienstebene.
* Abfrageaktivität: Volumen, Latenz und gedrosselt oder verworfene Abfragen. Protokollierte Abfrageanforderungen erfordern [Azure Monitor](#add-azure-monitor).
* Indexaktivität: erfordert die [Diagnoseprotokollierung](#add-azure-monitor) von Azure Monitor.

Ein Suchdienst unterstützt keine benutzerspezifische Authentifizierung, sodass keine Benutzeridentitätsinformationen in den Protokollen gefunden werden.

## <a name="built-in-monitoring"></a>Integrierte Überwachung

Die integrierte Überwachung bezieht sich auf Aktivitäten, die von einem Suchdienst protokolliert werden. Mit Ausnahme der Diagnose ist für diese Überwachungsebene keine Konfiguration erforderlich.

Azure Cognitive Search verwaltet interne Daten in einem rollierenden 30-Tage-Zeitplan für die Berichterstellung zur Dienstintegrität und Abfragemetriken, die Sie im Portal oder über diese [Rest-APIs](#monitoring-apis) finden können.

Auf dem folgenden Screenshot sehen Sie, wie Sie Überwachungsinformationen im Portal finden. Die Daten werden verfügbar, sobald Sie mit der Verwendung des Diensts beginnen. Portalseiten werden alle paar Minuten aktualisiert.

* Auf der Registerkarte **Überwachen** auf der Hauptübersichtsseite werden das Abfragevolumen, die Latenz und die Dienstauslastung angezeigt.
* Das **Aktivitätsprotokoll** im linken Navigationsbereich ist mit Azure Resource Manager verbunden. Im Aktivitätsprotokoll werden die Aktionen von Resource Manager erfasst: Dienstverfügbarkeit und Status, Kapazitätsänderungen (Replikate und Partitionen) sowie auf den API-Schlüssel bezogene Aktivitäten.
* In den **Überwachungseinstellungen** weiter unten finden Sie konfigurierbare Warnungen, Metrikvisualisierungen und Diagnoseprotokolle. Erstellen Sie diese, wenn Sie sie benötigen. Nachdem die Daten gesammelt und gespeichert wurden, können Sie sie abfragen oder visualisieren, um Erkenntnisse zu erhalten.

  ![Azure Monitor-Integration mit einem Suchdienst](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor-Integration mit einem Suchdienst")

> [!NOTE]
> Da die Portalseiten alle paar Minuten aktualisiert werden, sind die angegebenen Zahlen ungefähre Werte, die Ihnen eine allgemeine Übersicht darüber geben sollen, wie gut Anforderungen in Ihrem System verarbeitet werden. Die tatsächlichen Metriken, z. B. die Abfragen pro Sekunde (QPS), können höher oder niedriger als die auf dieser Seite angezeigte Zahl sein. Wenn die Genauigkeit eine Anforderung ist, sollten Sie unter Umständen APIs einsetzen.

<a name="monitoring-apis"> </a>

### <a name="rest-apis-useful-for-monitoring"></a>Für die Überwachung nützliche REST-APIs

Mithilfe von [Postman](search-get-started-rest.md) und der folgenden APIs können Sie die gleichen Informationen abrufen, die auf den Registerkarten „Überwachen“ und „Verwendung“ im Portal angezeigt werden. Sie müssen einen [Administrator-API-Schlüssel](search-security-api-keys.md) bereitstellen, um Systeminformationen zu erhalten.

* [Abrufen von Dienststatistiken](/rest/api/searchservice/get-service-statistics)
* [Abrufen von Indexstatistiken](/rest/api/searchservice/get-index-statistics)
* [Abrufen der Dokumentanzahl](/rest/api/searchservice/count-documents)
* [Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Aktivitätsprotokolle und Dienstintegrität

Auf der Seite [**Aktivitätsprotokoll**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) im Portal werden Informationen aus Azure Resource Manager und zu Änderungen an der Dienstintegrität erfasst. Sie können das Aktivitätsprotokoll auf kritische, Fehler- und Warnungsbedingungen im Zusammenhang mit der Dienstintegrität überwachen.

Zu den üblichen Einträgen zählen Verweise auf API-Schlüssel, d. h. generische informative Benachrichtigungen wie *Get Admin Key* (Administratorschlüssel abrufen) und *Get Query Keys* (Abfrageschlüssel abrufen). Diese Aktivitäten weisen auf Anforderungen hin, die mithilfe des Administratorschlüssels (Objekte erstellen oder löschen) oder des Abfrageschlüssels durchgeführt wurden, jedoch nicht die Anforderung selbst anzeigen. Um weitere Informationen auf dieser Ebene zu erhalten, müssen Sie die Diagnoseprotokollierung konfigurieren.

Auf das **Aktivitätsprotokoll** können Sie im linken Navigationsbereich, über „Benachrichtigungen“ auf der Befehlsleiste oben im Fenster oder über die Seite **Probleme diagnostizieren und beheben** zugreifen.

### <a name="monitor-storage-in-the-usage-tab"></a>Überwachen des Speichers auf der Registerkarte „Verwendung“

Für die visuelle Überwachung im Portal wird auf der Registerkarte **Verwendung** die Ressourcenverfügbarkeit relativ zu den aktuellen [Limits](search-limits-quotas-capacity.md) angezeigt, die durch die Dienstebene vorgegeben sind. Wenn Sie Entscheidungen dazu treffen möchten, [welcher Tarif für Produktionsworkloads verwendet werden soll](search-sku-tier.md) oder ob [die Anzahl der aktiven Replikate und Partitionen angepasst wird](search-capacity-planning.md), können Sie diese Metriken heranziehen. Anhand der Metriken können Sie sehen, wie schnell Ressourcen verbraucht werden und wie die vorhandene Last in der aktuellen Konfiguration verarbeitet wird.

Die folgende Abbildung bezieht sich auf den kostenlosen Dienst, der auf 3 Objekte pro Typ und auf 50 MB Speicher begrenzt ist. Für einen Dienst mit dem Tarif „Basic“ oder „Standard“ gelten höhere Grenzwerte. Wenn Sie die Anzahl der Partitionen erhöhen, steigt der maximale Speicher proportional an.

![Verwendungsstatus relativ zu den Tarifgrenzwerten](./media/search-monitor-usage/usage-tab.png
 "Verwendungsstatus relativ zu den Tarifgrenzwerten")

> [!NOTE]
> Warnungen im Zusammenhang mit dem Speicher sind derzeit nicht verfügbar. Der Speicherverbrauch wird nicht aggregiert oder in der Tabelle **AzureMetrics** in Azure Monitor protokolliert. Damit Sie Speicherbenachrichtigungen erhalten, müssen Sie [eine benutzerdefinierte Lösung erstellen](../azure-monitor/insights/solutions.md), die ressourcenbezogene Benachrichtigungen ausgibt und in der Ihr Code die Speichergröße überprüft und die Antwort verarbeitet.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Zusätzliches Überwachen mit Azure Monitor

Viele Dienste, einschließlich Azure Cognitive Search, können mit [Azure Monitor](../azure-monitor/index.yml) integriert werden, um zusätzliche Benachrichtigungen zu erhalten, Metriken zu erfassen und Diagnosedaten zu protokollieren. 

[Aktivieren Sie die Diagnoseprotokollierung](search-monitor-logs.md) für einen Suchdienst, wenn Sie die Datenerfassung und den Speicher steuern können möchten. Protokollierte Ereignisse, die von Azure Monitor aufgezeichnet werden, werden in der Tabelle **AzureDiagnostics** gespeichert und bestehen aus operativen Daten zu Abfragen und der Indizierung.

Azure Monitor bietet mehrere Speicheroptionen. Ihre Auswahl bestimmt, wie Sie die Daten nutzen können:

* Wählen Sie Azure Blob Storage, wenn Sie [Protokolldaten in einem Power BI-Bericht visualisieren möchten](search-monitor-logs-powerbi.md).
* Wählen Sie Log Analytics, wenn Sie Daten mithilfe von Kusto-Abfragen durchsuchen möchten.

Azure Monitor verfügt über eine eigene Abrechnungsstruktur, und für die in diesem Abschnitt angesprochenen Diagnoseprotokolle fallen Kosten an. Weitere Informationen finden Sie unter [Nutzung und geschätzte Kosten in Azure Monitor](../azure-monitor//usage-estimated-costs.md).

## <a name="monitor-user-access"></a>Überwachen des Benutzerzugriffs

Da Suchindizes eine Komponente einer größeren Clientanwendung sind, gibt es keine integrierte Methode, um den Zugriff einzelner Benutzer auf einen Index zu steuern oder zu überwachen. Es wird davon ausgegangen, dass Anforderungen von einer Clientanwendung stammen, die entweder eine Administrator- oder Abfrageanforderung darstellen. Lese-/Schreibvorgänge von Administratoren umfassen das Erstellen, Aktualisieren und Löschen von Objekten für den gesamten Dienst. Schreibgeschützte Vorgänge sind Abfragen der Dokumentensammlung, die auf einen einzelnen Index bezogen sind. 

Daher werden in den Aktivitätsprotokollen Verweise auf Aufrufe mithilfe von Administrator- oder Abfrageschlüsseln angezeigt. Der zugehörige Schlüssel ist in Anforderungen enthalten, die aus Clientcode stammen. Der Dienst ist nicht für die Verarbeitung von Identitätstoken oder Identitätswechseln ausgelegt.

Wenn die Geschäftsanforderungen eine benutzerspezifische Autorisierung erfordern, wird die Integration mit Azure Active Directory empfohlen. Sie können $filter und Benutzeridentitäten verwenden, um Suchergebnisse von Dokumenten, die einem Benutzer nicht angezeigt werden sollen, zu [kürzen](search-security-trimming-for-azure-search-with-aad.md). 

Diese Informationen können nicht getrennt von der Abfragezeichenfolge protokolliert werden, die den $filter-Parameter enthält. Ausführliche Informationen zur Berichterstellung für Abfragezeichenfolgen finden Sie unter [Überwachen von Abfragen](search-monitor-queries.md).

## <a name="next-steps"></a>Nächste Schritte

Kenntnisse von Azure Monitor sind für die Überwachung aller Azure-Dienste, einschließlich Ressourcen wie Azure Cognitive Search, von entscheidender Bedeutung. Wenn Sie mit Azure Monitor nicht vertraut sind, nehmen Sie sich die Zeit, die Artikel zu den Ressourcen zu lesen. Zusätzlich zu den Tutorials stellt der folgende Artikel einen guten Ausgangspunkt dar.

> [!div class="nextstepaction"]
> [Überwachen von Azure-Ressourcen mit Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md)