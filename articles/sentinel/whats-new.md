---
title: Neuerungen in Azure Sentinel
description: In diesem Artikel werden neue Azure Sentinel-Features der letzten Monate beschrieben.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 10/11/2021
ms.openlocfilehash: 07e48fba8bfb554604d875f9f4a041fed466dbe7
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754708"
---
# <a name="whats-new-in-azure-sentinel"></a>Neuerungen in Azure Sentinel

In diesem Artikel werden die neuesten Features für Azure Sentinel sowie neue Features in verwandten Diensten aufgeführt, die Azure Sentinel noch benutzerfreundlicher machen.

Elemente, die älter als sechs Monate sind, finden Sie im [Archiv zu den Neuerungen in Azure Sentinel](whats-new-archive.md). Informationen zu älteren bereitgestellten Features finden Sie in unseren [Tech Community-Blogs](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

> [!IMPORTANT]
> Entsprechend gekennzeichnete Features sind derzeit als VORSCHAUVERSION verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Die Microsoft-Teams für die Bedrohungssuche steuern Abfragen, Playbooks, Arbeitsmappen und Notebooks zur [Azure Sentinel-Community](https://github.com/Azure/Azure-Sentinel) bei, darunter auch spezifische [Hunting-Abfragen](https://github.com/Azure/Azure-Sentinel), die Ihre Teams anpassen und verwenden können.
>
> Und auch Sie selbst können sich beteiligen! Werden Sie Teil der [GitHub-Community für Azure Sentinel-Bedrohungsspezialisten](https://github.com/Azure/Azure-Sentinel/wiki).
>

## <a name="october-2021"></a>Oktober 2021

- [Spielbuchvorlagen und Galerie jetzt verfügbar (Öffentliche Vorschau)](#playbook-templates-and-gallery-now-available-public-preview)
- [Verwaltung von Vorlagenversionen für Ihre geplanten Analyseregeln (Öffentliche Vorschau)](#manage-template-versions-for-your-scheduled-analytics-rules-public-preview)
- [DHCP-Normalisierungsschema (Öffentliche Vorschau)](#dhcp-normalization-schema-public-preview)

### <a name="playbook-templates-and-gallery-now-available-public-preview"></a>Playbook-Vorlagen und Galerie jetzt verfügbar (Öffentliche Vorschau)

Eine Playbook-Vorlage ist ein vorgefertigter, getesteter und gebrauchsfertiger Workflow, der an Ihre Bedürfnisse angepasst werden kann. Vorlagen können auch als Referenz für Best Practices bei der Entwicklung von Playbooks von Grund auf oder als Inspiration für neue Automatisierungsszenarien dienen.

Playbook-Vorlagen wurden von der Azure Sentinel-Community, unabhängigen Softwareanbietern (ISVs) und Microsoft-Experten entwickelt. Sie finden sie auf der Registerkarte **Playbook-Vorlagen** (unter **Automatisierung**), als Teil einer [**Azure Sentinel-Lösung**](sentinel-solutions.md) oder im [**Azure Sentinel GitHub-Repository**](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks). 

Weitere Informationen zur Bereitstellung einer Playbook-Vorlage finden Sie unter [Erstellen und Anpassen von Playbooks aus integrierten Vorlagen](use-playbook-templates.md) auf der Registerkarte **Playbook-Vorlagen** unter **Automatisierung**.

### <a name="manage-template-versions-for-your-scheduled-analytics-rules-public-preview"></a>Verwalten von Vorlagenversionen für Ihre geplanten Analyseregeln (Öffentliche Vorschau)

Wenn Sie Analyseregeln aus [eingebauten Azure Sentinel-Regelvorlagen](detect-threats-built-in.md) erstellen, erstellen Sie effektiv eine Kopie der Vorlage. Nach diesem Zeitpunkt wird die aktive Regel ***nicht*** dynamisch aktualisiert, um alle Änderungen an der Ursprungsvorlage zu berücksichtigen.

Allerdings merken sich Regeln, die aus Vorlagen ***merken*** sich, aus welchen Vorlagen sie stammen, was Ihnen zwei Vorteile bietet:

- Wenn Sie bei der Erstellung einer Regel aus einer Vorlage (oder zu einem beliebigen späteren Zeitpunkt) Änderungen an der Regel vorgenommen haben, können Sie die Regel jederzeit auf ihre ursprüngliche Version (als Kopie der Vorlage) zurücksetzen.

- Sie können sich benachrichtigen lassen, wenn eine Vorlage aktualisiert wird, und haben dann die Wahl, Ihre Regeln auf die neue Version ihrer Vorlagen zu aktualisieren oder sie so zu belassen, wie sie sind.

[Erfahren Sie, wie Sie diese Aufgaben bewältigen](manage-analytics-rule-templates.md) und was Sie dabei beachten sollten. Diese Verfahren gelten für alle [Geplante](detect-threats-built-in.md#scheduled) Analyseregeln, die aus Vorlagen erstellt wurden.

### <a name="dhcp-normalization-schema-public-preview"></a>DHCP-Normalisierungsschema (Öffentliche Vorschau)

Das Azure Sentinel Information Model (ASIM) unterstützt jetzt ein DHCP-Normalisierungsschema, das zur Beschreibung von Ereignissen dient, die von einem DHCP-Server gemeldet werden, und von Azure Sentinel verwendet wird, um quellenunabhängige Analysen zu ermöglichen. 

Zu den Ereignissen, die im DHCP-Normalisierungsschema beschrieben werden, gehören die Bereitstellung von Anforderungen für DHCP-IP-Adressen, die von Client-Systemen geleast werden, und die Aktualisierung eines DNS-Servers mit den gewährten Leases.

Weitere Informationen finden Sie unter:

- [Azure Sentinel DHCP-Normalisierungsschema-Referenz (öffentliche Vorschau)](dhcp-normalization-schema.md)
- [Normalisierung und das Azure Sentinel-Informationsmodell (ASIM)](normalization.md)

## <a name="september-2021"></a>September 2021

- [Neu in der Dokumentation: Dokumentation zur Datenconnectorskalierung](#new-in-docs-scaling-data-connector-documentation)
- [Änderungen am Azure Storage-Kontoconnector](#azure-storage-account-connector-changes)

### <a name="new-in-docs-scaling-data-connector-documentation"></a>Neu in der Dokumentation: Dokumentation zur Datenconnectorskalierung

Da wir immer mehr integrierte Datenconnectors für Azure Sentinel hinzufügen, haben wir unsere Datenconnector-Dokumentation neu organisiert, um diese Skalierung widerzuspiegeln.

Für die meisten Datenconnectors haben wir vollständige Artikel, in denen ein einzelner Connector beschrieben wird, durch eine Reihe generischer Prozeduren und eine vollständige Referenz aller derzeit unterstützten Connectors ersetzt.

Ausführliche Informationen zu Ihrem Connector finden Sie in der [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md), einschließlich Verweisen auf die relevante generische Prozedur sowie zusätzliche Informationen und Konfigurationen, die erforderlich sind.

Weitere Informationen finden Sie unter:

- **Konzeptionelle Informationen**: [Verbinden von Datenquellen](connect-data-sources.md)

- **Allgemeine Gewusst-wie-Artikel**:

   - [Verbinden mit Azure-, Windows-, Microsoft- und Amazon-Diensten](connect-azure-windows-microsoft-services.md)
   - [Verbinden Ihrer Datenquelle mit der Datensammler-API von Azure Sentinel zur Datenerfassung](connect-rest-api-template.md)
   - [Abrufen von Protokollen im CEF-Format von Ihrem Gerät oder Ihrer Appliance in Azure Sentinel](connect-common-event-format.md)
   - [Sammeln von Daten aus Linux-basierten Quellen mithilfe von Syslog](connect-syslog.md)
   - [Sammeln von Daten in benutzerdefinierten Protokollformaten für Azure Sentinel mit dem Log Analytics-Agent](connect-custom-logs.md)
   - [Verwenden von Azure Functions zum Verbinden Ihrer Datenquelle mit Azure Sentinel](connect-azure-functions-template.md)
   - [Ressourcen zum Erstellen benutzerdefinierter Azure Sentinel-Connectors](create-custom-connector.md)

### <a name="azure-storage-account-connector-changes"></a>Änderungen am Azure Storage-Kontoconnector

Aufgrund einiger Änderungen, die innerhalb der Ressourcenkonfiguration des Azure Storage-Kontos selbst vorgenommen wurden, muss der Connector ebenfalls neu konfiguriert werden.
Die (übergeordnete) Speicherkontoressource enthält andere (untergeordnete) Ressourcen für jeden Speichertyp: Dateien, Tabellen, Warteschlangen und Blobs.

Beim Konfigurieren von Diagnosen für ein Speicherkonto müssen Sie wiederum Folgendes auswählen und konfigurieren:
- Die übergeordnete Kontoressource, die die **Transaktions** metrik exportiert.
- Alle untergeordneten Speichertypressourcen, die alle Protokolle und Metriken exportieren (siehe Tabelle oben).

Es werden nur die Speichertypen angezeigt, für die Sie tatsächlich Ressourcen definiert haben.

:::image type="content" source="media/whats-new/storage-diagnostics.png" alt-text="Screenshot der Azure Storage-Diagnosekonfiguration.":::

## <a name="august-2021"></a>August 2021

- [Erweiterte Incidentsuche (öffentliche Vorschau)](#advanced-incident-search-public-preview)
- [Fusion-Erkennung für Ransomware (öffentliche Vorschau)](#fusion-detection-for-ransomware-public-preview)
- [Watchlistvorlagen für UEBA-Daten](#watchlist-templates-for-ueba-data-public-preview)
- [Schema zur Dateiereignisnormalisierung (öffentliche Vorschau)](#file-event-normalization-schema-public-preview)
- [Neues in der Dokumentation: Anleitung für bewährte Methoden](#new-in-docs-best-practice-guidance)

### <a name="advanced-incident-search-public-preview"></a>Erweiterte Incidentsuche (öffentliche Vorschau)

Standardmäßig werden Incidentsuchvorgänge nur für die Werte **Incident-ID**, **Titel**, **Tags**, **Besitzer** und **Produktname** ausgeführt. Azure Sentinel bietet jetzt [erweiterte Suchoptionen](investigate-cases.md#search-for-incidents) für die Suche in größeren Datenmengen, einschließlich Warnungsdetails, Beschreibungen, Entitäten, Taktiken und mehr.

Beispiel:

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="Screenshot der Seite „Incidents“ mit erweiterten Suchoptionen":::

Weitere Informationen finden Sie unter [Suchen nach Incidents](investigate-cases.md#search-for-incidents).

### <a name="fusion-detection-for-ransomware-public-preview"></a>Fusion-Erkennung für Ransomware (öffentliche Vorschau)

Azure Sentinel bietet jetzt neue Fusion-Erkennungen für mögliche Ransomware-Aktivitäten und generiert Incidents mit dem Titel **Mehrere Warnungen möglicherweise im Zusammenhang mit erkannter Ransomware-Aktivität**.

Incidents werden für Warnungen generiert, die möglicherweise mit Ransomware-Aktivitäten in Verbindung stehen, wenn sie innerhalb eines bestimmten Zeitrahmens auftreten und den Ausführungs- und Verteidigungsumgehungsphasen eines Angriffs zugeordnet sind. Anhand der im Incident aufgeführten Warnungen können Sie die Techniken analysieren, die Angreifer möglicherweise einsetzen, um einen Host bzw. ein Gerät zu gefährden und die Erkennung zu umgehen.

Zu den unterstützten Datenkonnektoren gehören:

- [Azure Defender (Azure Security Center)](connect-azure-security-center.md)
- [Microsoft Defender für den Endpunkt](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-cloud-app-security-mcas)
- [Azure Sentinel-Regeln für geplante Analysen](detect-threats-built-in.md#scheduled)

Weitere Informationen finden Sie unter [Mehrere Warnungen möglicherweise im Zusammenhang mit erkannter Ransomware-Aktivität](fusion.md#multiple-alerts-possibly-related-to-ransomware-activity-detected-public-preview).

### <a name="watchlist-templates-for-ueba-data-public-preview"></a>Watchlistvorlagen für UEBA-Daten (öffentliche Vorschau)

Azure Sentinel bietet jetzt integrierte Watchlistvorlagen für UEBA-Daten, die Sie an Ihre Umgebung anpassen und bei Untersuchungen verwenden können.

Nachdem die UEBA-Watchlists mit Daten gefüllt wurden, können Sie diese Daten mit Analyseregeln korrelieren, sie auf den Entitätsseiten und Untersuchungsdiagrammen als Erkenntnisse anzeigen, benutzerdefinierte Verwendungen erstellen, z. B. zum Nachverfolgen von VIP- oder sensiblen Benutzern und mehr.

Watchlistvorlagen umfassen derzeit Folgendes:

- **VIP-Benutzer** Eine Liste der Benutzerkonten der Mitarbeiter, die einen hohen Einflusswert in der Organisation haben
- **Ausgeschiedene Mitarbeiter** Eine Liste der Benutzerkonten der Mitarbeitern, die ausgeschieden sind oder demnächst ausscheiden
- **Dienstkonten** Eine Liste der Dienstkonten und deren Besitzer
- **Identitätskorrelation** Eine Liste verknüpfter Benutzerkonten, die der gleichen Person gehören
- **Anlagen mit hohem Wert** Eine Liste von Geräten, Ressourcen oder anderen Anlagen, die für die Organisation von großem Wert sind
- **Netzwerkzuordnung** Eine Liste der IP-Subnetze und ihrer jeweiligen Organisationskontexte

Weitere Informationen finden Sie unter [Erstellen einer neuen Watchlist mithilfe einer Vorlage](watchlists.md#create-a-new-watchlist-using-a-template-public-preview) und [Integrierte Watchlistschemas](watchlist-schemas.md).



### <a name="file-event-normalization-schema-public-preview"></a>Schema zur Dateiereignisnormalisierung (öffentliche Vorschau)

Das Azure Sentinel Information Model (ASIM) unterstützt jetzt ein Schema zur Dateiereignisnormalisierung, das zum Beschreiben von Dateiaktivitäten wie das Erstellen, Ändern oder Löschen von Dateien oder Dokumenten verwendet wird. Dateiereignisse werden von Betriebssystemen, Dateispeichersystemen wie Azure Files und Dokumentenverwaltungssystemen wie Microsoft SharePoint gemeldet.

Weitere Informationen finden Sie unter:

- [Azure Sentinel: Referenz zum Dateiereignis-Normalisierungsschema (öffentliche Vorschau)](file-event-normalization-schema.md)
- [Normalisierung und das Azure Sentinel-Informationsmodell (ASIM)](normalization.md)


### <a name="new-in-docs-best-practice-guidance"></a>Neues in der Dokumentation: Anleitung für bewährte Methoden

Auf vielfachen Wunsch von Kunden und unseren Supportteams haben wir unserer Dokumentation eine Reihe von Anleitungen für bewährte Methoden hinzugefügt.

Weitere Informationen finden Sie unter:

- [Voraussetzungen für die Bereitstellung von Azure Sentinel](prerequisites.md)
- [Bewährte Methoden für Azure Sentinel](best-practices.md)
- [Bewährte Methoden für die Azure Sentinel-Arbeitsbereichsarchitektur](best-practices-workspace-architecture.md)
- [Entwerfen der Azure Sentinel-Arbeitsbereichsarchitektur](design-your-workspace-architecture.md)
- [Beispiele für Azure Sentinel-Arbeitsbereichsentwürfe](sample-workspace-designs.md)
- [Bewährte Methoden für die Datensammlung](best-practices-data.md)

> [!TIP]
> Weitere der Dokumentation hinzugefügte Anleitungen finden Sie in den entsprechenden konzeptionellen Artikeln und Schrittanleitungen. Weitere Informationen finden Sie unter [Bewährte Methoden: Referenzen](best-practices.md#best-practice-references).
>

## <a name="july-2021"></a>Juli 2021

- [Microsoft Threat Intelligence Matching Analytics (öffentliche Vorschau)](#microsoft-threat-intelligence-matching-analytics-public-preview)
- [Verwenden von Azure AD-Daten mit der Azure Sentinel IdentityInfo-Tabelle (öffentliche Vorschau)](#use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview)
- [Anreichern von Entitäten mit Geolocationdaten über die API (öffentliche Vorschau)](#enrich-entities-with-geolocation-data-via-api-public-preview)
- [Unterstützung für ressourcenübergreifende ADX-Abfragen (öffentliche Vorschau)](#support-for-adx-cross-resource-queries-public-preview)
- [Watchlists sind allgemein verfügbar](#watchlists-are-in-general-availability)
- [Unterstützung für Datenresidenz in mehr geografischen Standorten](#support-for-data-residency-in-more-geos)
- [Bidirektionale Synchronisierung in Azure Defender Connector (öffentliche Vorschau)](#bidirectional-sync-in-azure-defender-connector-public-preview)


### <a name="microsoft-threat-intelligence-matching-analytics-public-preview"></a>Microsoft Threat Intelligence Matching Analytics (öffentliche Vorschau)

Azure Sentinel bietet jetzt die integrierte **Microsoft Threat Intelligence-Abgleichsanalyse**-Regel, die von Microsoft generierte Threat Intelligence-Daten mit Ihren Protokollen abgleicht. Diese Regel generiert Warnungen und Incidents mit hoher Genauigkeit mit entsprechenden Schweregraden basierend auf dem Kontext der erkannten Protokolle. Wenn eine Übereinstimmung gefunden wird, wird der Indikator auch in Ihrem Threat Intelligence-Repository in Azure Sentinel veröffentlicht.

Die Regel **Microsoft Threat Intelligence-Abgleichsanalyse** gleicht derzeit Domänenindikatoren mit den folgenden Protokollquellen ab:

- [CEF](connect-common-event-format.md)
- [DNS](./data-connectors-reference.md#domain-name-server)
- [Syslog](connect-syslog.md)

Weitere Informationen finden Sie unter [Erkennen von Bedrohungen mithilfe von Abgleichsanalysen (öffentliche Vorschau)](work-with-threat-indicators.md#detect-threats-using-matching-analytics-public-preview).

### <a name="use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview"></a>Verwenden von Azure AD-Daten mit der Azure Sentinel IdentityInfo-Tabelle (öffentliche Vorschau)

Da Angreifer häufig die eigenen Benutzer- und Dienstkonten der Organisation verwenden, sind Daten über diese Benutzerkonten wie etwa Benutzeridentifikation und Berechtigungen für die Analysten bei der Untersuchung von entscheidender Bedeutung.

Nachdem Sie [UEBA](enable-entity-behavior-analytics.md) in Ihrem Azure Sentinel-Arbeitsbereich aktiviert haben, werden Azure AD-Daten auch mit der neuen **IdentityInfo**-Tabelle in Log Analytics synchronisiert. Bei Synchronisierungen zwischen Azure AD und der **IdentityInfo**-Tabelle wird eine Momentaufnahme Ihrer Benutzerprofildaten erstellt. Diese umfassen Benutzermetadaten, Gruppeninformationen und Azure AD-Rollen, die den einzelnen Benutzern zugewiesen sind.

Verwenden Sie die **IdentityInfo**-Tabelle während Untersuchungen und bei der Feinabstimmung von Analyseregeln für Ihre Organisation, um falsch positive Ergebnisse zu reduzieren.

Weitere Informationen finden Sie in der Referenz zu UEBA-Anreicherungen unter [Tabelle „IdentityInfo“](ueba-enrichments.md#identityinfo-table-public-preview) und [Verwenden von UEBA-Daten zum Analysieren falsch positiver Ergebnisse](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives).

### <a name="enrich-entities-with-geolocation-data-via-api-public-preview"></a>Anreichern von Entitäten mit Geolocationdaten über die API (öffentliche Vorschau)

Azure Sentinel bietet jetzt eine API zum Anreichern Ihrer Daten mit Geolocationinformationen. Die Geolocationdaten können dann zur Analyse und Untersuchung von Sicherheitsincidents verwendet werden.

Weitere Informationen finden Sie unter [Anreichern von Entitäten in Azure Sentinel mit Geolocationdaten über die REST-API (öffentliche Vorschau)](geolocation-data-api.md) und [Klassifizieren und Analysieren von Daten mithilfe von Entitäten in Azure Sentinel](entities-in-azure-sentinel.md).


### <a name="support-for-adx-cross-resource-queries-public-preview"></a>Unterstützung für ressourcenübergreifende ADX-Abfragen (öffentliche Vorschau)

Die Hunting-Benutzeroberfläche in Azure Sentinel unterstützt jetzt [ressourcenübergreifende ADX-Abfragen](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer).
 
Obwohl Log Analytics der primäre Datenspeicherort für die Analyse mit Azure Sentinel bleibt, gibt es Fälle, in denen aufgrund von Kosten, Aufbewahrungszeiträumen oder anderen Faktoren ADX zum Speichern von Daten erforderlich ist. Mit dieser Funktion können Kunden einen größeren Satz von Daten durchsuchen und die Ergebnisse auf der [Hunting-Benutzeroberfläche in Azure Sentinel](hunting.md) anzeigen, einschließlich Hunting- und [Livestream](livestream.md)-Abfragen und der Log Analytics-Suchseite.

Verwenden Sie zum Abfragen von in ADX-Clustern gespeicherten Daten die Adx()-Funktion, um den ADX-Cluster, den Datenbanknamen und die gewünschte Tabelle anzugeben. Anschließend können Sie die Ausgabe wie jede andere Tabelle abfragen. Weitere Informationen finden Sie auf den oben verlinkten Seiten.




### <a name="watchlists-are-in-general-availability"></a>Watchlists sind allgemein verfügbar

Die [Watchlists](watchlists.md)-Funktion ist jetzt allgemein verfügbar. Verwenden Sie Watchlists zum Anreichern von Warnungen mit Geschäftsdaten, um Zulässigkeits- oder Blocklisten zu erstellen, anhand derer Zugriffsereignisse überprüft werden können, und um die Untersuchung von Bedrohungen zu unterstützen und die Anzahl der Warnmeldungen zu reduzieren.

### <a name="support-for-data-residency-in-more-geos"></a>Unterstützung für Datenresidenz in mehr geografischen Standorten

Azure Sentinel unterstützt jetzt die vollständige Datenresidenz in den folgenden zusätzlichen geografischen Standorten:

Brasilien, Norwegen, Südafrika, Südkorea, Deutschland, Vereinigte Arabische Emirate und Schweiz.

Die vollständige Liste der unterstützten geografischen Standorte finden Sie in [diesem Artikel](quickstart-onboard.md#geographical-availability-and-data-residency).

### <a name="bidirectional-sync-in-azure-defender-connector-public-preview"></a>Bidirektionale Synchronisierung in Azure Defender Connector (öffentliche Vorschau)

Der Azure Defender Connector unterstützt jetzt die bidirektionale Synchronisierung des Warnungsstatus zwischen Defender und Azure Sentinel. Wenn Sie einen Sentinel-Incident schließen, der eine Defender-Warnung enthält, wird die Warnung automatisch auch im Defender-Portal geschlossen.

Eine vollständige Beschreibung des aktualisierten Azure Defender Connectors [finden Sie hier](connect-azure-security-center.md).

## <a name="june-2021"></a>Juni 2021

- [Upgrades für die Normalisierung und das Azure Sentinel-Informationsmodell (ASIM)](#upgrades-for-normalization-and-the-azure-sentinel-information-model)
- [Aktualisierte Dienst-zu-Dienst-Connectors](#updated-service-to-service-connectors)
- [Exportieren und Importieren von Analyseregeln (öffentliche Vorschau)](#export-and-import-analytics-rules-public-preview)
- [Warnungsanreicherung: Warnungsdetails (öffentliche Vorschau)](#alert-enrichment-alert-details-public-preview)
- [Weitere Hilfe zu Playbooks!](#more-help-for-playbooks)
- [Neuorganisation der Dokumentation](#new-documentation-reorganization)

### <a name="upgrades-for-normalization-and-the-azure-sentinel-information-model"></a>Upgrades für die Normalisierung und das Azure Sentinel-Informationsmodell (ASIM)

Mit dem Azure Sentinel-Informationsmodell können Sie quellenunabhängige Inhalte verwenden und erstellen, um die Analyse der Daten in Ihrem Azure Sentinel-Arbeitsbereich zu vereinfachen.

In diesem Monat haben wir unsere Normalisierungsdokumentation verbessert und um neue Detailebenen und vollständige DNS-, Prozessereignis- und Authentifizierungsnormalisierungsschemas erweitert.

Weitere Informationen finden Sie unter:

- [Normalisierung und das Azure Sentinel-Informationsmodell (ASIM)](normalization.md) (aktualisiert)
- [Azure Sentinel: Referenz zum Authentifizierungsnormalisierungsschema (öffentliche Vorschau)](authentication-normalization-schema.md) (neu!)
- [Azure Sentinel: Schemareferenz zur Datennormalisierung](normalization-schema.md)
- [Referenz zum DNS-Normalisierungsschema von Azure Sentinel (öffentliche Vorschau)](dns-normalization-schema.md) (neu!)
- [Azure Sentinel: Referenz zum Prozessereignis-Normalisierungsschema (öffentliche Vorschau)](process-events-normalization-schema.md) (neu!)
- [Azure Sentinel: Referenz zum Registrierungsereignis-Normalisierungsschema (öffentliche Vorschau)](registry-event-normalization-schema.md) (neu!)


### <a name="updated-service-to-service-connectors"></a>Aktualisierte Dienst-zu-Dienst-Connectors

Zwei unserer am häufigsten verwendeten Connectors wurden umfassend überarbeitet.

- Der [Windows Connector für Sicherheitsereignisse (öffentliche Vorschau)](connect-windows-security-events.md) basiert jetzt auf dem neuen Azure Monitor-Agent (AMA), der Ihnen weitaus mehr Flexibilität bei der Auswahl der zu erfassenden Daten bietet und Ihnen maximale Transparenz zu minimalen Kosten ermöglicht.

- Der [Connector für Azure-Aktivitätsprotokolle](./data-connectors-reference.md#azure-activity) basiert jetzt auf der Diagnoseeinstellungspipeline, sodass Sie vollständigere Daten, eine deutlich geringere Verzögerung bei der Erfassung und eine bessere Leistung und Zuverlässigkeit erhalten.

Die Upgrades erfolgen nicht automatisch. Benutzern dieser Connectors wird empfohlen, die neuen Versionen zu aktivieren.

### <a name="export-and-import-analytics-rules-public-preview"></a>Exportieren und Importieren von Analyseregeln (öffentliche Vorschau)

Sie können Ihre Analyseregeln jetzt in ARM-Vorlagendateien (Azure Resource Manager) im JSON-Format exportieren und Regeln aus diesen Dateien importieren, um Ihre Azure Sentinel-Bereitstellungen in Form von Code zu verwalten und zu steuern. Alle Arten von [Analyseregel](detect-threats-built-in.md) – nicht nur **geplante Regeln** können in eine ARM-Vorlage exportiert werden. Die Vorlagendatei enthält alle Informationen der Regel, von der Abfrage bis zu den zugewiesenen MITRE ATT&CK-Taktiken.

Weitere Informationen finden Sie unter [Exportieren und Importieren von Analyseregeln in und aus ARM-Vorlagen](import-export-analytics-rules.md).

### <a name="alert-enrichment-alert-details-public-preview"></a>Warnungsanreicherung: Warnungsdetails (öffentliche Vorschau)

Sie können nicht nur den Inhalt Ihrer Warnungen mit Entitätszuordnung und benutzerdefinierten Details anreichern, sondern auch die Art und Weise anpassen, wie Warnmeldungen – und damit auch Incidents – basierend auf ihrem jeweiligen Inhalt präsentiert und angezeigt werden. Wie bei den anderen Funktionen zur Warnungsanreicherung ist dies im [Analyseregel-Assistenten](detect-threats-custom.md) konfigurierbar.

Weitere Informationen finden Sie unter [Anpassen von Warnungsdetails in Azure Sentinel](customize-alert-details.md).


### <a name="more-help-for-playbooks"></a>Weitere Hilfe zu Playbooks!

Zwei neue Dokumente erleichtern Ihnen den Einstieg in die Erstellung und Verwendung von Playbooks.
- Unter [Authentifizieren von Playbooks für Azure Sentinel](authenticate-playbooks-to-sentinel.md) erfahren Sie, mit welchen verschiedenen Authentifizierungsmethoden Logic Apps-basierte Playbooks eine Verbindung zu Azure Sentinel herstellen und auf Informationen in Azure Sentinel zugreifen können und in welchen Fällen die Verwendung der jeweiligen Methode sinnvoll ist.
- Unter [Verwenden von Triggern und Aktionen in Playbooks](playbook-triggers-actions.md) wird der Unterschied zwischen dem **Incidenttrigger** und dem **Warnungstrigger** erläutert und beschrieben, welche Trigger wann zu verwenden sind. Außerdem werden einige Aktionen erläutert, die Sie als Reaktion auf Incidents in Playbooks ausführen können, einschließlich des Zugriffs auf die Informationen in [benutzerdefinierten Details](playbook-triggers-actions.md#work-with-custom-details).

Die Playbookdokumentation thematisiert auch ausführlich das MSSP-Szenario mit mehreren Mandanten.

### <a name="new-documentation-reorganization"></a>Neuorganisation der Dokumentation

In diesem Monat haben wir unsere [Azure Sentinel-Dokumentation](index.yml) neu strukturiert und in intuitive Kategorien unterteilt, die sich an gängigen Customer Journeys orientieren. Verwenden Sie den Filter für die Dokumentensuche und die aktualisierte Landing Page, um durch die Azure Sentinel-Dokumente zu navigieren.

:::image type="content" source="media/whats-new/new-docs.png" alt-text="Neuorganisation der Azure Sentinel-Dokumentation" lightbox="media/whats-new/new-docs.png":::


## <a name="may-2021"></a>Mai 2021

- [Azure Sentinel PowerShell-Modul](#azure-sentinel-powershell-module)
- [Verbesserungen der Warnungsgruppierung](#alert-grouping-enhancements)
- [Azure Sentinel-Lösungen (Public Preview)](#azure-sentinel-solutions-public-preview)
- [Kontinuierliche Bedrohungsüberwachung für SAP-Lösung (Public Preview)](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [Threat Intelligence-Integrationen (Public Preview)](#threat-intelligence-integrations-public-preview)
- [Fusion über geplante Warnungen (Public Preview)](#fusion-over-scheduled-alerts-public-preview)
- [SOC-ML-Anomalien (Public Preview)](#soc-ml-anomalies-public-preview)
- [IP-Entitätsseite (Public Preview)](#ip-entity-page-public-preview)
- [Aktivitätsanpassung (Public Preview)](#activity-customization-public-preview)
- [Hunting-Dashboard (Public Preview)](#hunting-dashboard-public-preview)
- [Incidentteams: Zusammenarbeiten in Microsoft Teams (Public Preview)](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [Arbeitsmappe „Zero Trust (TIC3.0)“](#zero-trust-tic30-workbook)


### <a name="azure-sentinel-powershell-module"></a>Azure Sentinel PowerShell-Modul

Das offizielle Azure Sentinel PowerShell-Modul zur Automatisierung täglicher operativer Aufgaben wurde als allgemein verfügbares Release freigegeben.

Sie können es hier herunterladen: [PowerShell-Katalog](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Weitere Informationen finden Sie in der PowerShell-Dokumentation [Az.SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="alert-grouping-enhancements"></a>Verbesserungen der Warnungsgruppierung

Sie können Ihre Analyseregel jetzt so konfigurieren, dass Warnungen in einem einzigen Incident gruppiert werden, und zwar nicht nur, wenn sie einem bestimmten Entitätstyp entsprechen, sondern auch, wenn sie mit einem bestimmten Warnungsnamen, Schweregrad oder anderen benutzerdefinierten Details für eine konfigurierte Entität übereinstimmen. 

Wählen Sie im Analyseregel-Assistenten auf der Registerkarte **Incidenteinstellungen** die Option zum Aktivieren der Warnungsgruppierung aus und dann die Option zum **Gruppieren von Warnungen in einem einzigen Incident, wenn die ausgewählten Entitäten und Details übereinstimmen**. 

Anschließend wählen Sie Ihren Entitätstyp und die jeweiligen Details aus, die Sie abgleichen möchten:

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="Gruppieren von Warnungen nach übereinstimmenden Entitätsdetails":::

Weitere Informationen finden Sie unter [Warnungsgruppierung](detect-threats-custom.md#alert-grouping).

### <a name="azure-sentinel-solutions-public-preview"></a>Azure Sentinel-Lösungen (Public Preview)

Azure Sentinel bietet jetzt [Lösungen](sentinel-solutions-catalog.md) mit **paketierten Inhalten**, die Kombinationen aus einem oder mehreren Datenconnectors, Arbeitsmappen, Analyseregeln, Playbooks, Hunting-Abfragen, Parsern, Watchlists und anderen Komponenten für Azure Sentinel umfassen.

Lösungen bieten verbesserte produktinterne Ermittlung, einstufige Bereitstellung und End-to-End-Produktszenarios. Weitere Informationen finden Sie unter [Ermitteln und Bereitstellen von Azure Sentinel-Lösungen](sentinel-solutions-deploy.md).

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>Kontinuierliche Bedrohungsüberwachung für SAP-Lösung (Public Preview)

Azure Sentinel-Lösungen umfassen jetzt **kontinuierliche Bedrohungsüberwachung für SAP**, sodass Sie SAP-Systeme auf komplexe Bedrohungen innerhalb der Geschäfts- und Anwendungsebene überwachen können.

Der SAP-Datenconnector streamt eine Vielzahl von 14 Anwendungsprotokollen aus der gesamten SAP-Systemlandschaft und sammelt Protokolle sowohl von Advanced Business Application Programming (ABAP) über NetWeaver RFC-Aufrufe als auch Dateispeicherdaten über die OSSAP-Kontrollschnittstelle. Der SAP-Datenconnector bietet Azure Sentinel die Möglichkeit, die zugrunde liegende SAP-Infrastruktur zu überwachen.

Um SAP-Protokolle in Azure Sentinel zu erfassen, muss der SAP-Datenconnector von Azure Sentinel in Ihrer SAP-Umgebung installiert sein. Nach der Bereitstellung des SAP-Datenconnectors stellen Sie die umfassenden Sicherheitsinhalte der SAP-Lösung bereit, um reibungslos Einblicke in die SAP-Umgebung Ihrer Organisation zu erhalten und alle zugehörigen Funktionen für Sicherheitsvorgänge zu verbessern.

Weitere Informationen finden Sie unter [Tutorial: Bereitstellen der Azure Sentinel-Lösung für SAP (öffentliche Vorschau)](sap-deploy-solution.md).

### <a name="threat-intelligence-integrations-public-preview"></a>Threat Intelligence-Integrationen (Public Preview)

Azure Sentinel bietet Ihnen verschiedene Möglichkeiten zur [Verwendung von Threat Intelligence-Feeds](./understand-threat-intelligence.md), damit Ihre Sicherheitsanalysten besser in der Lage sind, bekannte Bedrohungen zu erkennen und zu priorisieren.

Sie können jetzt eines von vielen neu verfügbaren Produkten der integrierten Threat Intelligence Platform (TIP) verwenden, eine Verbindung mit TAXII-Servern herstellen, um von STIX-kompatiblen Threat Intelligence-Quellen zu profitieren, und benutzerdefinierte Lösungen nutzen, die direkt mit der [Microsoft Graph-Sicherheits-API „tiIndicators“](/graph/api/resources/tiindicator) kommunizieren können.

Sie können auch über Playbooks eine Verbindung mit Threat Intelligence-Quellen herstellen, um Incidents mit TI-Informationen anzureichern, die direkte Untersuchungs- und Antwortaktionen unterstützen können.

Weitere Informationen finden Sie unter [Threat Intelligence-Integration in Azure Sentinel](threat-intelligence-integration.md).

### <a name="fusion-over-scheduled-alerts-public-preview"></a>Fusion über geplante Warnungen (Public Preview)

Die Machine Learning-basierte **Fusion**-Korrelations-Engine kann jetzt mehrstufige Angriffe mithilfe von Warnungen erkennen, die von einer Reihe [geplanter Analyseregeln](detect-threats-custom.md) in den zugehörigen Korrelationen generiert werden, zusätzlich zu den aus anderen Datenquellen importierten Warnungen.

Weitere Informationen finden Sie unter [Erweiterte Erkennung von mehrstufigen Angriffen in Azure Sentinel](fusion.md).

### <a name="soc-ml-anomalies-public-preview"></a>SOC-ML-Anomalien (Public Preview)

Mithilfe der Machine Learning-basierten SOC-ML-Anomalien in Azure Sentinel kann ungewöhnliches Verhalten erkannt werden, das andernfalls der Erkennung entgehen könnte.

SOC-ML verwendet Analyseregelvorlagen, die sofort einsatzbereit sind. Anomalien weisen zwar nicht zwangsläufig auf schädliches oder sogar verdächtiges Verhalten hin, können aber verwendet werden, um die Genauigkeit von Erkennungen, Untersuchungen und Bedrohungssuche zu verbessern.

Weitere Informationen finden Sie unter [Verwenden von SOC-ML-Anomalien zum Erkennen von Bedrohungen in Azure Sentinel](soc-ml-anomalies.md).

### <a name="ip-entity-page-public-preview"></a>IP-Entitätsseite (Public Preview)

Azure Sentinel unterstützt jetzt die IP-Adressentität, und Sie können nun IP-Entitätsinformationen auf der neuen IP-Entitätsseite anzeigen.

Wie die Benutzer- und Hostentitätsseiten enthält auch die IP-Entitätsseite allgemeine Informationen zur IP-Adresse, eine Liste von Aktivitäten, denen die IP-Adresse angehört, und vieles mehr, sodass Ihnen ein noch umfangreicherer Bestand an Informationen bereitsteht, um Ihre Untersuchung von Sicherheitsvorfällen zu verbessern.

Weitere Informationen finden Sie unter [Entitätsseiten](identify-threats-with-entity-behavior-analytics.md#entity-pages).

### <a name="activity-customization-public-preview"></a>Aktivitätsanpassung (Public Preview)

Es gibt noch eine Neuerung bei Entitätsseiten: Sie können jetzt neue benutzerdefinierte Aktivitäten für Ihre Entitäten erstellen, die auf den entsprechenden Entitätsseiten neben den bereits angezeigten vordefinierten Aktivitäten nachverfolgt und angezeigt werden.

Weitere Informationen finden Sie unter [Anpassen von Aktivitäten auf Zeitachsen der Entitätsseite](customize-entity-activities.md).

### <a name="hunting-dashboard-public-preview"></a>Hunting-Dashboard (Public Preview)

Das Blatt **Hunting** wurde aktualisiert. Mit dem neuen Dashboard können Sie alle Abfragen oder eine ausgewählte Teilmenge mit einem einzigen Klick ausführen.

Ermitteln Sie, wo mit der Suche begonnen werden soll, indem Sie sich die Ergebnisanzahl, Spitzen oder die Änderung der Ergebnisanzahl über einen Zeitraum von 24 Stunden ansehen. Sie können auch nach Favoriten, Datenquelle, MITRE ATT&CK-Taktik und --Verfahren, Ergebnissen oder Ergebnisdelta sortieren und filtern. Zeigen Sie die Abfragen an, mit denen die erforderlichen Datenquellen noch nicht verbunden sind, und erhalten Sie Empfehlungen zum Aktivieren dieser Abfragen.

Weitere Informationen finden Sie unter [Suchen nach Bedrohungen mit Azure Sentinel](hunting.md).

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Azure Sentinel-Incidentteam: Zusammenarbeit in Microsoft Teams (Public Preview)

Azure Sentinel unterstützt jetzt eine direkte Integration in Microsoft Teams, sodass Sie nahtlos in der gesamten Organisation und mit externen Beteiligten zusammenarbeiten können.

Erstellen Sie direkt aus dem Incident in Azure Sentinel ein neues *Incidentteam* für die zentrale Kommunikation und Koordination.

Incidentteams sind besonders als dedizierte Konferenzbrücke für laufende Incidents mit hohem Schweregrad hilfreich. Organisationen, die Microsoft Teams bereits für Kommunikation und Zusammenarbeit verwenden, können die Azure Sentinel-Integration nutzen, um Sicherheitsdaten direkt in ihre Konversationen und tägliche Arbeit einzubringen.

In Microsoft Teams enthält die Registerkarte **Incident page** (Incidentseite) des neuen Teams immer die aktuellen Daten aus Azure Sentinel. Dadurch wird sichergestellt, dass Ihre Teams stets die relevantesten Daten zur Hand haben.

[ ![Incidentseite in Microsoft Teams](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

Weitere Informationen finden Sie unter [Zusammenarbeiten in Microsoft Teams (Public Preview)](collaborate-in-microsoft-teams.md).

### <a name="zero-trust-tic30-workbook"></a>Arbeitsmappe „Zero Trust (TIC3.0)“

Die neue Arbeitsmappe „Zero Trust (TIC3.0)“ von Azure Sentinel bietet eine automatisierte Visualisierung von [Zero Trust](/security/zero-trust/)-Prinzipien, die an das [Trusted Internet Connections](https://www.cisa.gov/trusted-internet-connections)-Framework (TIC) übertragen werden.

Wir wissen, dass Compliance mehr als eine jährlich wiederkehrende Anforderung ist und Organisationen ihre Konfigurationen im Zeitverlauf ständig überwachen müssen. Die Zero Trust-Arbeitsmappe von Azure Sentinel nutzt die gesamte Bandbreite von Microsoft-Sicherheitsangeboten für Azure, Office 365, Teams, Intune, Windows Virtual Desktop und viele andere Produkte.

[ ![Zero Trust-Arbeitsmappe](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

**Die Zero Trust-Arbeitsmappe**:

- Ermöglicht es Implementierern, SecOps-Analysten, Gutachtern, Entscheidungsträgern für Sicherheit und Compliance, MSSPs und anderen Beteiligten, ein Situationsbewusstsein für den Sicherheitsstatus von Cloudworkloads zu erlangen.
- Umfasst über 75 Kontrollkarten, die an den TIC 3.0-Sicherheitsfunktionen ausgerichtet sind und über auswählbare GUI-Schaltflächen zur Navigation verfügen.
- Wurde entwickelt, um die Ressourcen der Mitarbeiter durch Automatisierung, künstliche Intelligenz, Machine Learning, Erzeugung von Abfragen und Warnungen, Visualisierungen, maßgeschneiderte Empfehlungen und entsprechende Dokumentationsverweise zu erweitern.

Weitere Informationen finden Sie unter [Visualisieren und Überwachen Ihrer Daten](monitor-your-data.md).

## <a name="april-2021"></a>April 2021

- [Azure Policy-basierte Datenconnectors](#azure-policy-based-data-connectors)
- [Zeitachse für Incidents (Public Preview)](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>Azure Policy-basierte Datenconnectors

Azure Policy kann verwendet werden, um eine allgemeine Gruppe von Diagnoseprotokolleinstellungen auf alle (aktuellen und zukünftigen) Ressourcen eines bestimmten Typs anzuwenden, deren Protokolle in Azure Sentinel erfasst werden sollen.

In Fortsetzung unserer Bemühungen, die Leistungsfähigkeit von [Azure Policy](../governance/policy/overview.md) für die Konfiguration von Datensammlungen zu nutzen, bieten wir nun einen weiteren, durch Azure Policy erweiterten Datensammler für [Azure Storage-Kontoressourcen](./data-connectors-reference.md#azure-storage-account) an, der zur öffentlichen Vorschau freigegeben wurde.

Außerdem wurden zwei unserer Connectors in der Vorschauversion für [Azure Key Vault](./data-connectors-reference.md#azure-key-vault) und [Azure Kubernetes Service](./data-connectors-reference.md#azure-kubernetes-service-aks)jetzt für die allgemeine Verfügbarkeit (GA) veröffentlicht, die unserem [Connector für Azure SQL-Datenbanken](./data-connectors-reference.md#azure-sql-databases) beitreten.

### <a name="incident-timeline-public-preview"></a>Zeitachse für Incidents (Public Preview)

Die erste Registerkarte auf einer Seite mit Incidentdetails ist jetzt die Registerkarte **Zeitachse**. Sie zeigt eine Zeitachse der Warnungen und Textmarken im Incident. Die Zeitachse eines Incidents kann Ihnen helfen, den Incident nachzuvollziehen und die Zeitachse der Angreiferaktivitäten anhand der zugehörigen Warnungen und Textmarken zu rekonstruieren.

- Wählen Sie ein Element auf der Zeitachse aus, um die Details anzuzeigen, ohne den Incidentkontext zu verlassen.
- Filtern Sie den Zeitachseninhalt, um nur Warnungen oder Textmarken oder nur Elemente eines bestimmten Schweregrads oder mit einer bestimmten MITRE-Taktik anzuzeigen.
- Wählen Sie den Link **ID der Systemwarnung** aus, um den gesamten Datensatz anzuzeigen, oder den Link **Ereignisse**, um die zugehörigen Ereignisse im Bereich **Protokolle** einzusehen.

Beispiel:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Registerkarte „Zeitachse für Incidents“":::

Weitere Informationen finden Sie im [Tutorial: Untersuchen von Incidents mit Azure Sentinel](investigate-cases.md).


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Schnellstart: Ausführen des Onboardings für Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Einblick in Warnungen](get-visibility.md)
