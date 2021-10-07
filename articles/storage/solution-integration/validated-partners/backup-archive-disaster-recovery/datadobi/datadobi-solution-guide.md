---
title: Sichern Ihrer Daten in Azure mit DobiProtect
titleSuffix: Azure Storage
description: Überblick über die zu berücksichtigenden Faktoren und die auszuführenden Schritte, um Azure als Speicherziel und Wiederherstellungsstandort für DobiProtect zu verwenden
author: karauten
ms.author: karauten
ms.date: 04/12/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: cdeff15125a9af306e04baae5efaf758e8cf0b11
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278215"
---
# <a name="backup-to-azure-with-dobiprotect"></a>Sicherung in Azure mit DobiProtect

Dieser Artikel unterstützt Sie bei der Integration einer DobiProtect-Infrastruktur in Azure Blob Storage. Er enthält Informationen zu Voraussetzungen, Überlegungen sowie Anleitungen zu Implementierung und Betrieb. In diesem Artikel geht es um die Nutzung von Azure als Offsitesicherungsziel und als Wiederherstellungsstandort bei einem Notfall, durch den ein normaler Betrieb an Ihrem primären Standort nicht möglich ist.
DobiProtect umfasst DobiSync und DobiReplicate. Mit DobiSync können Sie Daten schützen, indem Sie in regelmäßigen Abständen eine Kopie erstellen und auf einem Objektspeicherserver speichern.  Mit DobiReplicate können Sie eine Kopie von Objekten oder Daten aus einem objekt- oder netzwerkgebundenen Primärspeicher in einen Sekundärspeicher erstellen und die Informationen und zugehörigen Metadaten synchron halten. In diesem Dokument werden nur Anweisungen für DobiSync aufgeführt.

## <a name="reference-architecture"></a>Referenzarchitektur

Referenzarchitektur für Sicherungsbereitstellungen vom lokalen Standort zu Azure und für Azure-interne Bereitstellungen.

![Referenzarchitektur: DobiProtect zu Azure](../media/dobiprotect-diagram.jpg)

Ihre vorhandene DobiProtect-Bereitstellung lässt sich problemlos in Azure integrieren, indem Sie einfach eines oder mehrere Azure Storage-Konten als Cloudspeicherziel hinzufügen. Mit DobiProtect können Sie außerdem lokale Sicherungen in Azure wiederherstellen, sodass Sie einen Standort für On-Demand-Wiederherstellungen in Azure erhalten.

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
> Diese Schätzung wurde im Azure-Preisrechner mit nutzungsbasierter Bezahlung für „USA, Osten“ generiert und basiert auf einer Unterblockgröße von 32 MB, die 65.536 PUT-Anforderungen (Schreibtransaktionen) pro Tag generiert. Dieses Beispiel spiegelt möglicherweise nicht die aktuellen Azure-Preise wider oder ist nicht auf Ihre Anforderungen anwendbar.

## <a name="implementation-guidance"></a>Implementierungsleitfaden

Dieser Abschnitt bietet eine kurze Übersicht über die Vorgehensweise zum Hinzufügen von Azure Storage zu einer lokalen DobiSync-Bereitstellung. 

1. Öffnen Sie das Azure-Portal und suchen Sie nach **Speicherkonten**. Sie können auch auf das Symbol für **Standardspeicherkonten** klicken.

    ![Zeigt das Hinzufügen von Speicherkonten im Azure-Portal.](../media/azure-portal.png)
  
    ![Zeigt, wo Sie im Suchfeld des Azure-Portals das Wort „Speicher“ eingegeben haben.](../media/locate-storage-account.png)

2. Wählen Sie **Erstellen** aus, um ein Konto hinzuzufügen. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine neue. Geben Sie einen eindeutigen Namen an. Wählen Sie die Region aus. Wählen Sie **Standard** als Leistungsebene aus. Behalten Sie den Kontotyp **Storage V2** bei. Wählen Sie die Replikationsstufe aus, die Ihren SLAs entspricht. Wählen Sie die Standardebene aus, die von Ihrer Sicherungssoftware verwendet wird. Azure Storage stellt die Speicherebenen „Heiß“, „Kalt“ und „Archiv“ innerhalb eines einzelnen Kontos zur Verfügung, und mithilfe der DobiSync-Richtlinien können Sie mehrere Ebenen nutzen, um den Lebenszyklus Ihrer Daten effizient zu verwalten.

    ![Zeigt die Speicherkontoeinstellungen im Azure-Portal](../media/account-create-1.png)

3. Behalten Sie die standardmäßigen Netzwerkoptionen zunächst bei, und fahren Sie mit **Datenschutz** fort. Hier können Sie die Option „Vorläufiges Löschen“ aktivieren, die Ihnen ermöglicht, eine versehentlich gelöschte Sicherungsdatei innerhalb des definierten Aufbewahrungszeitraums wiederherzustellen. Diese Option bietet Schutz vor versehentlichen oder böswilligen Löschvorgängen.

    ![Zeigt die Datenschutzeinstellungen im Portal an.](../media/account-create-2.png)

4. Wir empfehlen, die Standardeinstellungen aus dem Bildschirm **Erweitert** für Sicherungen in Azure zu übernehmen.

    ![Zeigt die Registerkarte „Erweiterte Einstellungen“ im Portal an.](../media/account-create-3.png)

5. Fügen Sie Tags zum Organisieren hinzu und erstellen Sie Ihr Konto.

6. Nun sind nur noch zwei einfache Schritte nötig, um das Konto in Ihre DobiSync-Umgebung aufzunehmen. Navigieren Sie im Azure-Portal zu dem gerade erstellten Konto, und wählen Sie im Menü **Blob-Dienst** die Option **Container** aus. Fügen Sie einen Container hinzu, und geben Sie einen aussagekräftigen Namen an. Navigieren Sie dann in den **Einstellungen** zu **Zugriffsschlüssel**, und kopieren Sie den **Namen des Speicherkontos** und einen der beiden Zugriffsschlüssel. In den nächsten Schritten benötigen Sie den Namen des Containers, den Namen des Kontos und den Zugriffsschlüssel.

    ![Zeigt die Erstellung eines Containers im Portal an.](../media/container-c.png)

    ![Zeigt die Einstellungen für Zugriffsschlüssel im Portal an.](../media/access-key.png)

7. (*Optional*) Sie können Ihrer Bereitstellung zusätzliche Sicherheitsschichten hinzufügen.

    1. Konfigurieren Sie den rollenbasierten Zugriff, um den Kreis der Personen einzuschränken, die Änderungen an Ihrem Speicherkonto vornehmen dürfen. Weitere Informationen finden Sie unter [Integrierte Rollen für Verwaltungsvorgänge](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Schränken Sie den Kontozugriff mithilfe der [Einstellungen für die Storage-Firewall](../../../../common/storage-network-security.md) auf bestimmte Netzwerksegmente ein, um Zugriffe von Punkten außerhalb Ihres Unternehmensnetzwerks zu verhindern.

        ![Zeigt Storage Firewall-Einstellungen im Portal an.](../media/storage-firewall.png)

    1. Legen Sie eine [Löschsperre](../../../../../azure-resource-manager/management/lock-resources.md) für das Storage-Konto fest, um ein versehentliches Löschen des Kontos zu verhindern.

        ![Zeigt das Festlegen einer Löschsperre im Portal.](../media/resource-lock.png)

    1. Konfigurieren Sie [zusätzliche Best Practices für die Sicherheit](../../../../../storage/blobs/security-recommendations.md).

1. Navigieren Sie in DobiSync zu **Configuration** -> **Object Storage** („Konfiguration“ > „Objektspeicher“), und fügen Sie einen Objektspeicher hinzu:

    ![Zeigt das Benutzeroberflächenelement zum Hinzufügen eines neuen Objektspeichers in DobiSync an.](../media/dobisync-new-storage.png)

9. Geben Sie einen **Account name** (Kontonamen) für das **Storage account** (Speicherkonto) und den **Account key** (Kontoschlüssel) ein: 

     ![Screenshot: Hinzufügen von Azure Blob-Anmeldeinformationen in DobiSync](../media/dobisync-add-storage.jpg)

10. Geben Sie einen Namen für die **Azure Blob Storage**-Verbindung ein: ![Screenshot: Benutzeroberflächenelement mit Details zum DobiSync-Synchronisierungsziel](../media/dobisync-target-details.jpg)
    

11. Im nächsten Schritt werden alle **Blob Containers** (Blobcontainer) angezeigt, die für dieses spezifische **Storage account** (Speicherkonto) konfiguriert sind. Wählen Sie das Protokoll und die Anmeldeinformationen für Container aus, die Sie als Synchronisierungsziele nutzen möchten. Beispiel: 

    ![Screenshot: Benutzeroberflächenelement „Containers“ in DobiSync](../media/dobisync-container-list.jpg)
 
12. Weisen Sie dieser Azure Blob-Verbindung die Proxys zu, und klicken Sie auf **Test connection** (Verbindung testen), um sicherzustellen, dass die Proxys mit den Containern kommunizieren können:

    ![Screenshot: Überprüfte DobiSync-Container in der Benutzeroberfläche](../media/dobisync-container-checked.jpg)

13. Die **Connection test results** (Ergebnisse des Verbindungstests) werden angezeigt: 

    ![Screenshot: Benutzeroberflächenelement für Verbindungstest in DobiSync](../media/dobisync-connection.jpg)

13. Klicken Sie auf „Finish“ (Fertig stellen), um die Azure Blob-Konfiguration abzuschließen. Sie können anschließend eine neue Synchronisierungssitzung einleiten.

## <a name="operational-guidance"></a>Leitfaden

#### <a name="azure-portal"></a>Azure-Portal

Mit [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md) bietet Azure eine stabile Überwachungslösung. Sie können [Azure Monitor konfigurieren](../../../../blobs/monitor-blob-storage.md), um die Kapazität, Transaktionen, Verfügbarkeit, Authentifizierung und viele weitere Aspekte von Azure Storage nachzuverfolgen. Die vollständige Referenz zu den gesammelten Metriken finden Sie [hier](../../../../blobs/monitor-blob-storage-reference.md). Folgende Metriken sind nützlich: BlobCapacity zum Sicherstellen, dass das [Kapazitätslimit des Storage-Kontos](../../../../common/scalability-targets-standard-account.md) nicht überschritten wird; der eingehende und ausgehende Datenverkehr zum Nachverfolgen der Menge an Daten, die in Ihr Azure Storage-Konto geschrieben und daraus gelesen werden; SuccessE2ELatency zum Nachverfolgen der Roundtripzeit für Anforderungen an und von Azure Storage und Ihrem Medien-Agent.

Sie können auch [Protokollwarnungen](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) erstellen, um die Integrität des Azure Storage-Diensts nachzuverfolgen, und sich jederzeit das [Azure-Statusdashboard](https://status.azure.com/status) ansehen.

#### <a name="dobisync-documentation"></a>DobiSync-Dokumentation

Weitere Informationen zur Konfiguration von DobiSync finden Sie im [DobiSync-Benutzerhandbuch](https://downloads.datadobi.com/NAS/olh/latest/dobisync.html).

### <a name="how-to-open-support-cases"></a>Eröffnen von Supportfällen

Wenn Sie Hilfe bei der Sicherung in der Azure-Lösung benötigen, sollten Sie sowohl bei Datadobi als auch bei Azure eine Supportanfrage erstellen. Dadurch können unsere Support-Organisationen bei Bedarf zusammenarbeiten.

#### <a name="to-open-a-case-with-datadobi"></a>So erstellen Sie eine Supportanfrage bei Datadobi

Navigieren Sie zur [Datadobi Support-Website](https://support.datadobi.com/s/), melden Sie sich an, und erstellen Sie eine Anfrage.

#### <a name="to-open-a-case-with-azure"></a>So eröffnen Sie einen Supportfall bei Azure

Suchen Sie im [Azure-Portal](https://portal.azure.com) auf der Suchleiste oben nach **Support**. Wählen Sie **Hilfe und Support** -> **Neue Supportanfrage**.

> [!NOTE]
> Geben Sie beim Eröffnen des Falls an, dass Sie Unterstützung für „Azure Storage“ oder „Azure-Netzwerk“ benötigen. Geben Sie nicht Azure Backup an. Azure Backup ist ein nativer Dienst von Microsoft Azure, und Ihr Fall würde falsch weitergeleitet.

### <a name="links-to-relevant-datadobi-documentation"></a>Links zu relevanter Datadobi-Dokumentation

Weitere Details finden Sie in der folgenden Datadobi-Dokumentation:

- [Leitfaden zu Voraussetzungen](https://downloads.datadobi.com/NAS/guides/latest/prerequisites.html)
- [Installationshandbuch für DobiProtect](https://downloads.datadobi.com/NAS/guides/latest/installguide.html)

## <a name="next-steps"></a>Nächste Schritte

Datadobi hat die Bereitstellung der Datadobi-Lösung in Azure zum Schutz von Azure-VMs und vielen weiteren Azure-Diensten sehr einfach gestaltet. Weitere Informationen finden Sie in der folgenden Referenz:

- [Schützen von Dateidaten in Azure mit DobiProtect](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datadobi1602192408529.datadobi-dobiprotect?tab=overview)