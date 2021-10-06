---
title: Sichern Ihrer Daten in Azure mit Commvault
titleSuffix: Azure Storage
description: Hier erhalten Sie einen Überblick über die zu berücksichtigenden Faktoren und die auszuführenden Schritte, um Azure als Speicherziel und Wiederherstellungsstandort für Commvault Complete Backup and Recovery zu verwenden
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 8d897c4e4c76b42e980c23f1f18789248f04909a
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129270503"
---
# <a name="backup-to-azure-with-commvault"></a>Sichern in Azure mit Commvault

Dieser Artikel unterstützt Sie bei der Integration einer Commvault-Infrastruktur in Azure Blob Storage. Er enthält Informationen zu Voraussetzungen, Überlegungen sowie Anleitungen zu Implementierung und Betrieb. In diesem Artikel geht es um die Nutzung von Azure als Offsitesicherungsziel und als Wiederherstellungsstandort bei einem Notfall, durch den ein normaler Betrieb an Ihrem primären Standort nicht möglich ist.

> [!NOTE]
> Commvault bietet außerdem eine Lösung für schnellere Wiederherstellung (RTO), Commvault Live Sync. Mit dieser Lösung können Sie einen virtuellen Standbycomputer bereitstellen, mit dem das System in einer Azure-Produktionsumgebung im Notfall schneller wieder hergestellt werden kann. Diese Funktionen werden im vorliegenden Dokument nicht behandelt.

## <a name="reference-architecture"></a>Referenzarchitektur

Referenzarchitektur für Sicherungsbereitstellungen vom lokalen Standort zu Azure und für Azure-interne Bereitstellungen.

![Referenzarchitektur: Commvault zu Azure](../media/commvault-diagram.png)

Ihre vorhandene Commvault-Bereitstellung lässt sich problemlos in Azure integrieren, indem Sie einfach eines oder mehrere Azure Storage-Konten als Cloudsicherungsziel hinzufügen. Commvault ermöglicht auch die Wiederherstellung von Sicherungen vom lokalen Standort in Azure und bietet damit einen Standort zur bedarfsbasierten Wiederherstellung in Azure.

## <a name="commvault-interoperability-matrix"></a>CommVault-Interoperabilitätsmatrix

| Workload | GPv2 und Blob Storage | Unterstützung für kalte Speicherebene | Unterstützung für Archivspeicherebene | Unterstützung für die Data Box-Produktfamilie |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokale VMs/Daten | v11.5 | v11.5 | v11.10 | v11.10 |
| Virtuelle Azure-Computer | v11.5 | v11.5 | v11.5 | – |
| Azure Blob | v11.6 | v11.6 | v11.6 | – |
| Azure Files | v11.6 | v11.6 | v11.6 | – |

## <a name="before-you-begin"></a>Voraussetzungen

Ein wenig Planung im Vorfeld hilft Ihnen dabei, Azure als Ziel für die Offsitesicherung und als Wiederherstellungssite zu verwenden.

### <a name="get-started-with-azure"></a>Erste Schritte mit Azure

Microsoft bietet ein Framework, das Ihnen den Einstieg in Azure erleichtert. Das [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) ist ein detailliertes Konzept für die digitale Transformation in Unternehmen und ein umfassender Leitfaden für die Planung der Cloudeinführung in einer Produktionsumgebung. Das CAF enthält eine Schrittanleitung zum [Einrichten von Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/), damit Sie es schnell und sicher in Betrieb nehmen können. Eine interaktive Version finden Sie im [Azure-Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Sie finden dort Beispielarchitekturen, spezielle Best Practices für die Bereitstellung von Anwendungen sowie kostenlose Trainingsressourcen, mit denen Sie die notwendigen Fachkenntnisse zu Azure erhalten.

### <a name="consider-the-network-between-your-location-and-azure"></a>Überlegungen zum Netzwerk zwischen Ihrem Standort und Azure

Unabhängig davon, ob Sie Cloudressourcen zum Ausführen von Produktions-, Test- oder Entwicklungsumgebungen oder als Sicherungsziel und Wiederherstellungsstandort verwenden – Sie müssen Ihre Bandbreitenanforderungen für das anfängliche Sicherungsseeding sowie für die täglichen Datenübertragungen verstehen.

Azure Data Box bietet eine Möglichkeit zum Übertragen Ihrer anfänglichen Sicherungsbaseline in Azure, ohne dass eine höhere Bandbreite erforderlich ist. Dies ist hilfreich, wenn die Baselineübertragung länger dauert, als Sie akzeptieren können. Wenn Sie ein Speicherkonto erstellen, können Sie das Tool zum Schätzen von Datenübertragungsanforderungen verwenden, um zu ermitteln, wie lange die Übertragung einer anfänglichen Sicherung dauern wird.

![Zeigt das Azure Storage Tool zum Schätzen der Datenübertragungsanforderungen an.](../media/az-storage-transfer.png)

Denken Sie daran: Sie benötigen genügend Netzwerkkapazität, um tägliche Datenübertragungen innerhalb des erforderlichen Übertragungsfensters (Sicherungsfenster) zu unterstützen, ohne die Produktionsanwendungen zu beeinträchtigen. In diesem Abschnitt werden die Tools und Techniken beschrieben, die für die Bewertung Ihrer Netzwerkanforderungen verfügbar sind.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Bestimmen Sie, wie viel Bandbreite Sie benötigen

Verwenden Sie die folgenden Ressourcen, um zu bestimmen, wie viel Bandbreite Sie benötigen:

- Berichte von Ihrer Sicherungssoftware.
- Commvault bietet Standardberichte, um die [Änderungsrate](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) und die [Gesamtgröße des Sicherungssatzes](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) für die anfängliche Baselineübertragung zu Azure zu bestimmen.
- Von der Sicherungsoftware unabhängige Bewertungs- und Berichterstellungstools wie:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Nicht ausgelastete Internetbandbreite ermitteln

Es ist wichtig zu wissen, wie viel Spielraum an ungenutzter Bandbreite (oder welchen *Toleranzbereich*) Sie täglich haben. Auf diese Weise können Sie bewerten, ob Sie Ihre Ziele für Folgendes erreichen können:

- anfängliche Zeit zum Hochladen, wenn Sie nicht Azure Data Box für das Offline-Seeding verwenden
- Abschließen täglicher Sicherungen basierend auf der zuvor identifizierten Änderungsrate und Ihrem Sicherungsfenster

Verwenden Sie die folgenden Methoden, um den Spielraum an Bandbreite zu ermitteln, der für Sicherungen nach Azure zur Verfügung steht.

- Wenn Sie ein vorhandener Azure ExpressRoute-Kunde sind, können Sie die [Inanspruchnahme](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) im Azure-Portal anzeigen.
- Wenden Sie sich an Ihren ISP. Dieser sollte Ihnen Berichte zur Verfügung stellen können, die Ihre derzeitige tägliche und monatliche Nutzung darstellen.
- Es gibt eine Reihe von Tools, die durch Überwachung des Netzwerkdatenverkehrs auf Ebene des Routers bzw. Switches die Nutzung messen können. Dazu gehören:
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Wählen Sie die richtigen Speicheroptionen aus

Wenn Sie Azure als Sicherungsziel verwenden, nutzen Sie [Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md). Blob Storage ist die Objektspeicherlösung von Microsoft. Blob Storage ist für die Speicherung großer Mengen an unstrukturierten Daten optimiert, also Daten, für die kein Datenmodell und keine Datendefinition zutrifft. Darüber hinaus ist Azure Storage beständig, hochverfügbar, sicher und skalierbar. Sie können den richtigen Speicher für ihre Workload auswählen, um die für Ihre internen SLAs notwendige [Resilienz](../../../../common/storage-redundancy.md) bereitzustellen. Blob Storage ist ein Dienst mit nutzungsbasierter Bezahlung. Ihnen wird Folgendes [monatlich in Rechnung gestellt](../../../../blobs/access-tiers-overview.md#pricing-and-billing): die Menge der gespeicherten Daten, der Zugriff auf diese Daten und bei den Speicherebenen „Kalt“ und „Archiv“ ein Mindestzeitraum für die Aufbewahrung. In den folgenden Tabellen sind die für Sicherungsdaten geltenden Resilienz- und Ebenenoptionen aufgeführt.

**Resilienzoptionen für Blob Storage**:

|  |Lokal redundant  |Zonenredundant  |Georedundant  |Geozonenredundant  |
|---------|---------|---------|---------|---------|
|**Effektive Anzahl von Kopien**     | 3         | 3         | 6         | 6 |
|**Anzahl von Verfügbarkeitszonen**     | 1         | 3         | 2         | 4 |
|**Anzahl von Regionen**     | 1         | 1         | 2         | 2 |
|**Manuelles Failover zu sekundärer Region**     | –         | –         | Ja         | Ja |

**Blob-Speicherebenen:**

|  | Heiße Zugriffsebene   |Kalte Zugriffsebene   | Archivzugriffsebene |
| ----------- | ----------- | -----------  | -----------  |
| **Verfügbarkeit** | 99,9 %         | 99 %         | Offline      |
| **Nutzungsgebühren** | Höhere Speicherkosten, geringere Zugriffs- und Transaktionskosten | Geringere Speicherkosten, höhere Zugriffs- und Transaktionskosten | Niedrigste Speicherkosten, höchste Zugriffs- und Transaktionskosten |
| **Mindestzeitraum für Datenaufbewahrung erforderlich**| – | 30 Tage | 180 Tage |
| **Latenz (Zeit bis zum ersten Byte)** | Millisekunden | Millisekunden | Stunden |

#### <a name="sample-backup-to-azure-cost-model"></a>Beispielkostenmodell für die Sicherung in Azure

Nutzungsbasierte Bezahlung kann für Kunden, die noch nicht mit der Cloud vertraut sind, zunächst etwas abschreckend wirken. Sie zahlen zwar nur für die tatsächlich genutzte Kapazität, aber auch für Transaktionen (Lesen und Schreiben) sowie für [ausgehende Daten](https://azure.microsoft.com/pricing/details/bandwidth/), die wieder in Ihre lokale Umgebung eingelesen werden, wenn der [Azure ExpressRoute Direct Local-Plan oder der ExpressRoute-Plan mit Datenflatrate](https://azure.microsoft.com/pricing/details/expressroute/) verwendet werden, bei denen ausgehende Daten von Azure inbegriffen sind. Mit dem [Azure- Preisrechner](https://azure.microsoft.com/pricing/calculator/) können Sie eine Was-wäre-wenn-Analyse durchführen. Sie können die Analyse auf der Grundlage der Listenpreise oder der [Preise für reservierte Kapazität in Azure Storage ](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)durchführen, wodurch bis zu 38 % eingespart werden können. Im folgenden finden Sie eine beispielhafte Übung für die Preisberechnung, mit der die monatlichen Kosten für die Sicherung in Azure modelliert werden. Dies ist nur ein Beispiel. *Ihre Preise können aufgrund von Aktivitäten, die hier nicht erfasst werden, abweichen.*

|Kostenfaktor  |Monatliche Kosten  |
|---------|---------|
|100 TB Sicherungsdaten auf der kalten Speicherebene     |USD 1556,48         |
|2 TB neu geschriebene Daten pro Tag × 30 Tage     |USD 39 für Transaktionen          |
|Geschätzter monatlicher Gesamtbetrag     |USD 1595,48         |
|---------|---------|
|Einmalige Wiederherstellung von 5 TB am lokalen Standort über das öffentliche Internet   | USD 491,26         |

> [!NOTE]
> Diese Schätzung wurde im Azure-Preisrechner mit nutzungsbasierter Bezahlung für „USA, Osten“ generiert und basiert auf dem Commvault-Standardwert von 32 MB Unterblockgröße, der 65.536 PUT-Anforderungen (Schreibtransaktionen) pro Tag generiert. Dieses Beispiel ist auf Ihre Anforderungen möglicherweise nicht anwendbar.

## <a name="implementation-guidance"></a>Implementierungsleitfaden

Dieser Abschnitt bietet eine kurze Übersicht über das Hinzufügen von Azure Storage zu einer lokalen Commvault-Bereitstellung. Ausführliche Anleitungen und Planungsüberlegungen finden Sie im [Handbuch für die Public Cloud-Architektur von Commvault für Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Öffnen Sie das Azure-Portal und suchen Sie nach **Speicherkonten**. Sie können auch auf das Symbol für **Standardspeicherkonten** klicken.

    ![Zeigt das Hinzufügen von Speicherkonten im Azure-Portal.](../media/azure-portal.png)
  
    ![Zeigt, wo Sie im Suchfeld des Azure-Portals das Wort „Speicher“ eingegeben haben.](../media/locate-storage-account.png)

2. Wählen Sie **Erstellen** aus, um ein Konto hinzuzufügen. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine neue. Geben Sie einen eindeutigen Namen an. Wählen Sie die Region aus. Wählen Sie **Standard** als Leistungsebene aus. Behalten Sie den Kontotyp **Storage V2** bei. Wählen Sie die Replikationsstufe aus, die Ihren SLAs entspricht. Wählen Sie die Standardebene aus, die von Ihrer Sicherungssoftware verwendet wird. Azure Storage stellt die Speicherebenen „Heiß“, „Kalt“ und „Archiv“ innerhalb eines einzelnen Kontos zur Verfügung, und die Commvault-Richtlinien ermöglichen Ihnen, mehrere Ebenen zu nutzen, um den Lebenszyklus Ihrer Daten effektiv zu verwalten.

    ![Zeigt die Speicherkontoeinstellungen im Azure-Portal](../media/account-create-1.png)

3. Behalten Sie die standardmäßigen Netzwerkoptionen zunächst bei, und fahren Sie mit **Datenschutz** fort. Hier können Sie die Option „Vorläufiges Löschen“ aktivieren, die Ihnen ermöglicht, eine versehentlich gelöschte Sicherungsdatei innerhalb des definierten Aufbewahrungszeitraums wiederherzustellen. Diese Option bietet Schutz vor versehentlichen oder böswilligen Löschvorgängen.

    ![Zeigt die Datenschutzeinstellungen im Portal an.](../media/account-create-2.png)

4. Wir empfehlen, die Standardeinstellungen aus dem Bildschirm **Erweitert** für Sicherungen in Azure zu übernehmen.

    ![Zeigt die Registerkarte „Erweiterte Einstellungen“ im Portal an.](../media/account-create-3.png)

5. Fügen Sie Tags zum Organisieren hinzu und erstellen Sie Ihr Konto.

6. Sie müssen nur noch zwei schnelle Schritte ausführen, dann können Sie das Konto Ihrer Commvault-Umgebung hinzufügen. Navigieren Sie im Azure-Portal zu dem gerade erstellten Konto, und wählen Sie im Menü **Blob-Dienst** die Option **Container** aus. Fügen Sie einen Container hinzu, und geben Sie einen aussagekräftigen Namen an. Navigieren Sie dann in den **Einstellungen** zu **Zugriffsschlüssel**, und kopieren Sie den **Namen des Speicherkontos** und einen der beiden Zugriffsschlüssel. In den nächsten Schritten benötigen Sie den Namen des Containers, den Namen des Kontos und den Zugriffsschlüssel.

    ![Zeigt die Erstellung eines Containers im Portal an.](../media/container.png)

    ![Zeigt die Einstellungen für Zugriffsschlüssel im Portal an.](../media/access-key.png)

7. (*Optional*) Sie können Ihrer Bereitstellung zusätzliche Sicherheitsschichten hinzufügen.

    1. Konfigurieren Sie den rollenbasierten Zugriff, um den Kreis der Personen einzuschränken, die Änderungen an Ihrem Speicherkonto vornehmen dürfen. Weitere Informationen finden Sie unter [Integrierte Rollen für Verwaltungsvorgänge](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Schränken Sie den Kontozugriff mithilfe der [Einstellungen für die Storage-Firewall](../../../../common/storage-network-security.md) auf bestimmte Netzwerksegmente ein, um Zugriffe von Punkten außerhalb Ihres Unternehmensnetzwerks zu verhindern.

        ![Zeigt Storage Firewall-Einstellungen im Portal an.](../media/storage-firewall.png)

    1. Legen Sie eine [Löschsperre](../../../../../azure-resource-manager/management/lock-resources.md) für das Storage-Konto fest, um ein versehentliches Löschen des Kontos zu verhindern.

        ![Zeigt das Festlegen einer Löschsperre im Portal.](../media/resource-lock.png)

    1. Konfigurieren Sie [zusätzliche Best Practices für die Sicherheit](../../../../../storage/blobs/security-recommendations.md).

1. Navigieren Sie im Commvault Command Center zu **Manage** -> **Security** -> **Credential Manager** (Verwalten > Sicherheit > Anmeldeinformationen-Manager). Wählen Sie ein **Cloudkonto**, **Microsoft Azure Storage** als **Anbietertyp**, wählen Sie den **Media Agent** aus, der Daten in und aus Azure überträgt und fügen Sie den Speicherkontonamen und den Zugriffsschlüssel hinzu.

    ![Zeigt das Hinzufügen von Anmeldeinformationen im Commvault Command Center.](../media/commvault-credential.png)

9. Navigieren Sie als Nächstes im Commvault Command Center zu **Storage** -> **Cloud** (Speicher > Cloud). Wählen Sie **Add** (Hinzufügen) aus. Geben Sie einen Anzeigenamen für das Speicherkonto ein und wählen Sie dann in der Liste **Type** (Typ) **Microsoft Azure Storage** aus. Wählen Sie einen Media Agent-Server aus, der zum Übertragen von Sicherungen zu Azure Storage verwendet wird. Fügen Sie den erstellten Container hinzu, wählen Sie den „Storage Tier“ (Speicherebene) aus, der im Azure Storage-Konto genutzt werden soll, und wählen Sie die in Schritt 8 erstellten Anmeldeinformationen aus. Wählen Sie zum Schluss aus, ob deduplizierte Sicherungen übertragen werden sollen oder nicht, und wählen Sie einen Speicherort für die Deduplizierungsdatenbank aus.

     ![Screenshot der Commvault-Benutzeroberfläche zum Hinzufügen einer Cloud. Im Dropdown Menü Archiv ist * * Archiv * * ausgewählt.](../media/commvault-add-storage.png)

10. Fügen Sie schließlich Ihre neue Azure Storage-Ressource einem vorhandenen oder neuen Plan im Commvault Command Center über **Manage** -> **Plans** (Verwalten > Pläne) als Sicherungsziel hinzu.

    ![Screenshot der Benutzeroberfläche des Commvault Command Centers. Im linken Navigationsbereich ist unter **Manage** (Verwalten) die Option **Plans** (Pläne) ist ausgewählt.](../media/commvault-plan.png)

11. *(Optional)* Wenn Sie Azure als Wiederherstellungsstandort oder Commvault zum Migrieren von Servern und Anwendungen zu Azure nutzen möchten, sollten Sie einen VSA-Proxy in Azure bereitzustellen. Detaillierte Anweisungen finden Sie [hier](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).

## <a name="operational-guidance"></a>Leitfaden

### <a name="azure-alerts-and-performance-monitoring"></a>Warnungen und Leistungsüberwachung in Azure

Es ist ratsam, sowohl Ihre Azure-Ressourcen also auch die Fähigkeit von Commvault zur Nutzung dieser Ressourcen zu überwachen – genauso wie bei jedem anderen Speicherziel auch, auf das Sie sich bei der Speicherung Ihrer Sicherungen verlassen. Eine Kombination aus Azure Monitor- und Commvault Command Center-Überwachung hilft Ihnen, Ihre Umgebung zu schützen.

#### <a name="azure-portal"></a>Azure-Portal

Mit [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md) bietet Azure eine stabile Überwachungslösung. Sie können [Azure Monitor konfigurieren](../../../../blobs/monitor-blob-storage.md), um die Kapazität, Transaktionen, Verfügbarkeit, Authentifizierung und viele weitere Aspekte von Azure Storage nachzuverfolgen. Die vollständige Referenz zu den gesammelten Metriken finden Sie [hier](../../../../blobs/monitor-blob-storage-reference.md). Folgende Metriken sind nützlich: BlobCapacity zum Sicherstellen, dass das [Kapazitätslimit des Storage-Kontos](../../../../common/scalability-targets-standard-account.md) nicht überschritten wird; der eingehende und ausgehende Datenverkehr zum Nachverfolgen der Menge an Daten, die in Ihr Azure Storage-Konto geschrieben und daraus gelesen werden; SuccessE2ELatency zum Nachverfolgen der Roundtripzeit für Anforderungen an und von Azure Storage und Ihrem Medien-Agent.

Sie können auch [Protokollwarnungen](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) erstellen, um die Integrität des Azure Storage-Diensts nachzuverfolgen, und sich jederzeit das [Azure-Statusdashboard](https://status.azure.com/status) ansehen.

#### <a name="commvault-command-center"></a>Commvault Command Center

- [Erstellen einer Warnung für Cloudspeicherpools](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Informationen zum Anzeigen von Leistungsberichten, zum Abschließen des Auftrags und zum Starten der grundlegenden Problembehandlung finden Sie unter [Dashboards](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Eröffnen von Supportfällen

Wenn Sie Hilfe bei der Lösung für die Sicherung in Azure benötigen, sollten Sie einen Fall sowohl mit Commvault als auch mit Azure öffnen. Dadurch können unsere Support-Organisationen bei Bedarf zusammenarbeiten.

#### <a name="to-open-a-case-with-commvault"></a>So eröffnen Sie einen Supportfall bei Commvault

Navigieren Sie zur [Commvault Support-Website](https://www.commvault.com/support), melden Sie sich an und eröffnen Sie einen Fall.

Um die für Sie verfügbaren Supportvertragsoptionen zu verstehen, finden Sie weitere Informationen unter [Commvault Support Options](https://ma.commvault.com/support) (Optionen für den Commvault Support).

Sie können einen Fall auch telefonisch eröffnen oder den Commvault Support per E-Mail erreichen:

- Gebührenfrei: +1 877-780-3077
- [Weltweite Support-Telefonnummern](https://ma.commvault.com/Support/TelephoneSupport)
- [Commvault Support per E-Mail](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>So eröffnen Sie einen Supportfall bei Azure

Suchen Sie im [Azure-Portal](https://portal.azure.com) auf der Suchleiste oben nach **Support**. Wählen Sie **Hilfe und Support** -> **Neue Supportanfrage**.

> [!NOTE]
> Geben Sie beim Eröffnen des Falls an, dass Sie Unterstützung für „Azure Storage“ oder „Azure-Netzwerk“ benötigen. Geben Sie nicht Azure Backup an. Azure Backup ist ein nativer Dienst von Microsoft Azure, und Ihr Fall würde falsch weitergeleitet.

### <a name="links-to-relevant-commvault-documentation"></a>Links zu relevanter Commvault-Dokumentation

Weitere Details finden Sie in der folgenden Commvault-Dokumentation:

- [Commvault-Benutzerhandbuch](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Commvault Azure Architecture Guide](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf) (Handbuch zur Commvault-Azure-Architektur)

### <a name="marketplace-offerings"></a>Angebote im Marketplace

Commvault hat die Bereitstellung der Commvault-Lösung in Azure zum Schutz von Azure-VMs und vielen weiteren Azure-Diensten sehr einfach gestaltet. Weitere Informationen finden Sie in den folgenden Referenzen:

- [Bereitstellen von Commvault über den Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Commvault für Azure-Datenblatt](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Commvault-Liste der unterstützten Azure-Features und -Dienste](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Verwenden von Commvault zum Schützen von SAP HANA in Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu spezialisierten Verwendungsszenarien finden Sie in diesen zusätzlichen Commvault-Ressourcen.

- [Verwenden von Commvault zum Migrieren von Servern und Anwendungen zu Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Schützen von SAP in Azure mit Commvault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Schützen von Office 365 mit Commvault](https://www.youtube.com/watch?v=dl3nvAacxZU)