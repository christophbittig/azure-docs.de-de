---
title: Sichern Ihrer Daten in Azure mit Veeam
titleSuffix: Azure Storage
description: Sie erhalten bietet einen Überblick über die zu berücksichtigenden Faktoren und die auszuführenden Schritte, um Azure als Speicherziel und Wiederherstellungsstandort für Veeam Backup and Recovery zu verwenden
author: karauten
ms.author: karauten
ms.date: 05/12/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: e55a9d0bb21a3eded7454f74787f8be9f2f02ece
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273626"
---
# <a name="backup-to-azure-with-veeam"></a>Sicherung in Azure mit Veeam

Dieser Artikel unterstützt Sie bei der Integration einer Veeam-Infrastruktur in Azure Blob Storage. Er enthält Informationen zu Voraussetzungen, Überlegungen sowie Anleitungen zu Implementierung und Betrieb. In diesem Artikel geht es um die Nutzung von Azure als Offsitesicherungsziel und als Wiederherstellungsstandort bei einem Notfall, durch den ein normaler Betrieb an Ihrem primären Standort nicht möglich ist.

> [!NOTE]
> Veeam bietet auch eine Lösung mit einer niedrigeren RTO (Recovery Time Objective) namens „Veeam Backup & Replication“, die Azure VMware Solution-Workloads unterstützt. Mit dieser Lösung können Sie einen virtuellen Standbycomputer bereitstellen, mit dem das System in einer Azure-Produktionsumgebung im Notfall schneller wieder hergestellt werden kann. Veeam bietet auch eine direkte Wiederherstellung in Microsoft Azure und andere dedizierte Tools zum Sichern von Azure- und Office 365-Ressourcen. Diese Funktionen werden im vorliegenden Dokument nicht behandelt.

## <a name="reference-architecture"></a>Referenzarchitektur

Referenzarchitektur für Sicherungsbereitstellungen vom lokalen Standort zu Azure und für Azure-interne Bereitstellungen.

![Diagramm der Referenzarchitektur: Veeam zu Azure.](../media/veeam-architecture.png)

Ihre vorhandene Veeam-Bereitstellung lässt sich problemlos in Azure integrieren, indem Sie einfach eines oder mehrere Azure- Speicherkonten als Cloudsicherungsrepository hinzufügen. Veeam ermöglicht darüber hinaus die Wiederherstellung von Sicherungen vom lokalen Standort in Azure und bietet damit einen Standort zur bedarfsbasierten Wiederherstellung in Azure.

## <a name="veeam-interoperability-matrix"></a>Interoperabilitätsmatrix für Veeam

| Workload | GPv2 und Blob Storage | Unterstützung für kalte Speicherebene | Unterstützung für Archivspeicherebene | Unterstützung für die Data Box-Produktfamilie |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokale VMs/Daten | v10a | v10a | – | 10a<sup>*</sup> |
| Virtuelle Azure-Computer | v10a | v10a | – | 10a<sup>*</sup> |
| Azure Blob | v10a | v10a | – | 10a<sup>*</sup> |
| Azure Files | v10a | v10a | – | 10a<sup>*</sup> |

Auch in älteren Produktversionen bietet Veeam eine Unterstützung der oben erwähnten Azure-Features, für eine optimale Nutzung wird jedoch dringend die aktuelle Version empfohlen.

<sup>*</sup>Veeam Backup and Replication unterstützen die REST-API nur für Azure Data Box. Daher wird Azure Data Box Disk nicht unterstützt. Weitere Informationen zur Unterstützung von Data Box finden Sie [hier](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=110).


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

Es stehen mehrere Bewertungsoptionen zur Verfügung, mit denen Sie die Änderungsrate und die Gesamtgröße des Sicherungssatzes für die Übertragung der anfänglichen Baseline an Azure ermitteln können. Nachfolgend finden Sie einige Beispiele für Bewertungs- und Berichterstellungstools:

- [MiTrend](https://mitrend.com/)
- [APT are](https://www.veritas.com/insights/aptare-it-analytics)
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
| **Mindestzeitraum für Datenaufbewahrung erforderlich** | Nicht verfügbar | 30 Tage | 180 Tage |
| **Latenz (Zeit bis zum ersten Byte)** | Millisekunden | Millisekunden | Stunden |

#### <a name="sample-backup-to-azure-cost-model"></a>Beispielkostenmodell für die Sicherung in Azure

Nutzungsbasierte Bezahlung kann für Kunden, die noch nicht mit der Cloud vertraut sind, zunächst etwas abschreckend wirken. Sie zahlen zwar nur für die tatsächlich genutzte Kapazität, aber auch für Transaktionen (Lesen und Schreiben) sowie für [ausgehende Daten](https://azure.microsoft.com/pricing/details/bandwidth/), die wieder in Ihre lokale Umgebung eingelesen werden, wenn der [Azure ExpressRoute Direct Local-Plan oder der ExpressRoute-Plan mit Datenflatrate](https://azure.microsoft.com/pricing/details/expressroute/) verwendet werden, bei denen ausgehende Daten von Azure inbegriffen sind. Mit dem [Azure- Preisrechner](https://azure.microsoft.com/pricing/calculator/) können Sie eine Was-wäre-wenn-Analyse durchführen. Sie können die Analyse auf der Grundlage der Listenpreise oder der [Preise für reservierte Kapazität in Azure Storage ](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)durchführen, wodurch bis zu 38 % eingespart werden können. Im folgenden finden Sie eine beispielhafte Übung für die Preisberechnung, mit der die monatlichen Kosten für die Sicherung in Azure modelliert werden. Dies ist nur ein Beispiel. *Ihre Preise können aufgrund von Aktivitäten, die hier nicht erfasst werden, abweichen.*

|Kostenfaktor  |Monatliche Kosten  |
|---------|---------|
|100 TB Sicherungsdaten auf der kalten Speicherebene     |USD 1556,48         |
|2 TB neu geschriebene Daten pro Tag × 30 Tage     |42 USD für Transaktionen          |
|Geschätzter monatlicher Gesamtbetrag     |1\.598,48 USD         |
|---------|---------|
|Einmalige Wiederherstellung von 5 TB am lokalen Standort über das öffentliche Internet   | USD 527,26         |

> [!Note]
> Diese Schätzung wurde mit dem Azure-Preisrechner unter Verwendung der nutzungsbasierten Bezahlung in der Region „USA, Osten“ generiert und basiert auf dem Veeam-Standard von 512 KB großen Blöcken für Übertragungen in WANs. Dieses Beispiel ist auf Ihre Anforderungen möglicherweise nicht anwendbar.

## <a name="implementation-guidance"></a>Implementierungsleitfaden

Dieser Abschnitt bietet eine kurze Übersicht über das Hinzufügen von Azure Storage zu einer lokalen Veeam-Bereitstellung. Für ausführliche Anleitungen und Planungsüberlegungen empfiehlt es sich, den folgenden Veeam-Leitfaden für die [Kapazitätsebene](https://helpcenter.veeam.com/docs/backup/vsphere/capacity_tier.html?ver=110) anzusehen.

1. Öffnen Sie das Azure-Portal und suchen Sie nach **Speicherkonten**. Sie können auch auf das Standard-Dienstsymbol klicken.

      ![Zeigt das Hinzufügen von Speicherkonten im Azure-Portal.](../media/azure-portal.png)

      ![Zeigt, wo Sie im Suchfeld des Azure-Portals das Wort „Speicher“ eingegeben haben.](../media/locate-storage-account.png)

2. Wählen Sie **Erstellen** aus, um ein Konto hinzuzufügen. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine neue. Geben Sie einen eindeutigen Namen an. Wählen Sie die Region aus. Wählen Sie **Standard** als Leistungsebene aus. Behalten Sie den Kontotyp **Storage V2** bei. Wählen Sie die Replikationsstufe aus, die Ihren SLAs entspricht. Wählen Sie die Standardebene aus, die von Ihrer Sicherungssoftware verwendet wird. Azure Storage stellt die Speicherebenen „Heiß“, „Kalt“ und „Archiv“ innerhalb eines einzelnen Kontos zur Verfügung, und die Veeam-Richtlinien ermöglichen es Ihnen, mehrere Ebenen zu nutzen, um den Lebenszyklus Ihrer Daten effektiv zu verwalten.

    ![Zeigt die Speicherkontoeinstellungen im Azure-Portal](../media/account-create-1.png)

3. Behalten Sie vorerst die standardmäßig eingestellten Netzwerk- und Datenschutzoptionen bei. Aktivieren Sie das vorläufige Löschen **nicht** für Speicherkonten, in denen Veeam-Kapazitätsebenen gespeichert sind.

 4. Wir empfehlen, die Standardeinstellungen aus dem Bildschirm **Erweitert** für Sicherungen in Azure zu übernehmen.

    ![Zeigt die Registerkarte „Erweiterte Einstellungen“ im Portal an.](../media/account-create-3.png)

5. Fügen Sie Tags zum Organisieren hinzu und erstellen Sie Ihr Konto.

6. Sie müssen nur noch zwei schnelle Schritte ausführen, dann können Sie das Konto zu Ihrer Veeam-Umgebung hinzufügen. Navigieren Sie im Azure-Portal zu dem gerade erstellten Konto, und wählen Sie im Menü **Blob-Dienst** die Option **Container** aus. Fügen Sie einen Container hinzu, und geben Sie einen aussagekräftigen Namen an. Navigieren Sie dann in den **Einstellungen** zu **Zugriffsschlüssel**, und kopieren Sie den **Namen des Speicherkontos** und einen der beiden Zugriffsschlüssel. In den nächsten Schritten benötigen Sie den Namen des Containers, den Namen des Kontos und den Zugriffsschlüssel.

    ![Zeigt die Erstellung eines Containers im Portal an.](../media/container-b.png)

    ![Zeigt die Einstellungen für Zugriffsschlüssel im Portal an.](../media/access-key.png)

    > [!Note]
    > Veeam Backup and Replication bietet zusätzliche Optionen zum Herstellen einer Verbindung mit Azure. Für den Anwendungsfall in diesem Artikel mit Microsoft Azure Blob Storage als Sicherungsziel wird die oben beschriebene Methode als Best Practice empfohlen.

7. *(Optional)* Sie können zu Ihrer Bereitstellung weitere Sicherheitsschichten hinzufügen.

     1. Konfigurieren Sie den rollenbasierten Zugriff, um den Kreis der Personen einzuschränken, die Änderungen an Ihrem Speicherkonto vornehmen dürfen. Weitere Informationen finden Sie unter [Integrierte Rollen für Verwaltungsvorgänge](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Schränken Sie den Kontozugriff mithilfe der [Einstellungen für die Storage-Firewall](../../../../common/storage-network-security.md) auf bestimmte Netzwerksegmente ein, um Zugriffe von Punkten außerhalb Ihres Unternehmensnetzwerks zu verhindern.

        ![Zeigt Storage Firewall-Einstellungen im Portal an.](../media/storage-firewall.png)

     1. Legen Sie eine [Löschsperre](../../../../../azure-resource-manager/management/lock-resources.md) für das Storage-Konto fest, um ein versehentliches Löschen des Kontos zu verhindern.

        ![Ressourcensperre](../media/resource-lock.png)

     1. Konfigurieren Sie [zusätzliche Best Practices für die Sicherheit](../../../../../storage/blobs/security-recommendations.md).

8. Navigieren Sie in der Veeam Backup and Replication Management Console zu **Backup Infrastructure** (Sicherungsinfrastruktur), klicken Sie mit der rechten Maustaste auf den Übersichtsbereich, und wählen Sie **Sicherungsrepository hinzufügen** aus, um den Konfigurations-Assistenten zu öffnen. Wählen Sie im Dialogfeld **Object Storage (Objektspeicher)**  ->  **Microsoft Azure Blob Storage** -> **Azure Blob Storage** aus.

    ![Zeigt die Auswahl von Objektspeicher im Veeam Repository-Assistenten an.](../media/veeam-repo-a.png)

    ![Zeigt die Auswahl von Microsoft Azure Blob Storage im Veeam Repository-Assistenten an.](../media/veeam-repo-b.png)

    ![Zeigt die Auswahl von Azure Blob Storage im Veeam Repository-Assistenten an.](../media/veeam-repo-c.png)

9. Geben Sie als nächstes einen Namen und eine Beschreibung Ihres neuen Blob Storage-Repository an.

    ![Zeigt die Eingabe eines Namens für das Repository im Veeam Repository-Assistenten an.](../media/veeam-repo-d.png)

10. Im nächsten Schritt fügen Sie die Anmeldeinformationen für den Zugriff auf Ihr Azure Storage-Konto hinzu. Wählen Sie im Cloud Credential Manager die Option **Microsoft Azure Storage Account (Microsoft Azure Storage-Konto)** aus, und geben Sie den Namen des Storage-Kontos und den Zugriffsschlüssel ein. Wählen Sie als Region **Azure Global** und ggf. einen Gatewayserver aus.

    ![Zeigt das Festlegen eines Kontos im Veeam Repository-Assistenten an.](../media/veeam-repo-e.png)

    > [!Note]
    > Wenn Sie keinen Veeam-Gatewayserver verwenden möchten, stellen Sie sicher, dass alle aufskalierten Repositoryerweiterungen über direkten Internetzugriff verfügen.

11. Wählen Sie im Containerregister Ihren Azure Storage-Container aus. Wählen Sie dann einen Ordner aus, in dem die Sicherungen gespeichert werden sollen, oder erstellen Sie einen neuen Ordner. Sie können auch eine „weiche“ Obergrenze für die Gesamtspeicherkapazität definieren, die von Veeam verwendet werden darf. Dieses Vorgehen wird empfohlen. Überprüfen Sie die angezeigten Informationen in der Zusammenfassung, und schließen Sie das Konfigurationstool ab. Sie können jetzt das neue Repository in der Konfiguration ihres Sicherungsauftrags auswählen.

    ![Zeigt das Festlegen eines Containers im Veeam Repository-Assistenten an.](../media/veeam-repo-f.png)

    ![Zeigt das Erstellen eines Ordners im Veeam Repository-Assistenten an.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Leitfaden

### <a name="azure-alerts-and-performance-monitoring"></a>Warnungen und Leistungsüberwachung in Azure

Es ist ratsam, sowohl Ihre Azure-Ressourcen also auch die Fähigkeit von Veeam zur Nutzung dieser Ressourcen zu überwachen – genauso wie bei jedem anderen Speicherziel auch, auf das Sie sich bei der Speicherung Ihrer Sicherungen verlassen. Mit einer Kombination aus Azure Monitor und den Überwachungsfunktionen von Veeam (die Registerkarte **Statistik** im Knoten **Auftrag** der Veeam Management Console oder erweiterte Optionen wie Veeam One Reporter) können Sie die Integrität und den Schutz Ihrer Umgebung sicherstellen.

#### <a name="azure-portal"></a>Azure-Portal

Mit [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md) bietet Azure eine stabile Überwachungslösung. Sie können [Azure Monitor konfigurieren](../../../../blobs/monitor-blob-storage.md), um die Kapazität, Transaktionen, Verfügbarkeit, Authentifizierung und viele weitere Aspekte von Azure Storage nachzuverfolgen. Eine vollständige Referenz der nachverfolgten Metriken finden Sie [hier](../../../../blobs/monitor-blob-storage-reference.md). Folgende Metriken sind nützlich: BlobCapacity zum Sicherstellen, dass das [Kapazitätslimit des Storage-Kontos](../../../../common/scalability-targets-standard-account.md) nicht überschritten wird; der eingehende und ausgehende Datenverkehr zum Nachverfolgen der Menge an Daten, die in Ihr Azure Storage-Konto geschrieben und daraus gelesen werden; SuccessE2ELatency zum Nachverfolgen der Roundtripzeit für Anforderungen an und von Azure Storage und Ihrem Medien-Agent.

Sie können auch [Protokollwarnungen](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) erstellen, um die Integrität des Azure Storage-Diensts nachzuverfolgen, und sich jederzeit das [Azure-Statusdashboard](https://status.azure.com/status) ansehen.

#### <a name="veeam-reporting"></a>Berichterstellung in Veeam

- [Configuring Veeam One Reporting](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100) (Konfigurieren der Berichterstellung in Veeam One)
- [Veeam Backup and Replication Alarms](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) (Warnungen von Veeam Backup and Replication)

### <a name="how-to-open-support-cases"></a>Eröffnen von Supportfällen

Wenn Sie Hilfe bei der Lösung für die Sicherung in Azure benötigen, sollten Sie einen Fall sowohl mit Veeam als auch mit Azure öffnen. Dadurch können unsere Support-Organisationen bei Bedarf zusammenarbeiten.

#### <a name="to-open-a-case-with-veeam"></a>So eröffnen Sie einen Supportfall bei Veeam

Melden Sie sich auf der [Website des Veeam-Kundensupports](https://www.veeam.com/support.html)an, und eröffnen Sie einen Fall.

Informationen zum Verstehen der Supportoptionen, die Ihnen von Veeam zur Verfügung gestellt werden, finden Sie in der [Veeam Customer Support Policy](https://www.veeam.com/veeam_software_support_policy_ds.pdf) (Richtlinie für den Veeam-Kundensupport)

Sie können auch anrufen, um einen Fall zu eröffnen: [weltweite Supportnummern](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>So eröffnen Sie einen Supportfall bei Azure

Suchen Sie im [Azure-Portal](https://portal.azure.com) auf der Suchleiste oben nach **Support**. Wählen Sie **Hilfe und Support** -> **Neue Supportanfrage**.

> [!NOTE]
> Geben Sie beim Eröffnen des Falls an, dass Sie Unterstützung für „Azure Storage“ oder „Azure-Netzwerk“ benötigen. Geben Sie nicht Azure Backup an. Azure Backup ist ein nativer Dienst von Microsoft Azure, und Ihr Fall würde falsch weitergeleitet.

### <a name="links-to-relevant-veeam-documentation"></a>Links zu relevanter Veeam-Dokumentation

Weitere Details finden Sie in der folgenden Veeam-Dokumentation:

- [Veeam User Guide](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100) (Veeam-Benutzerhandbuch)
- [Veeam Architecture Guide](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100) (Veeam-Architekturleitfaden)

### <a name="marketplace-offerings"></a>Angebote im Marketplace

Sie können zum Schützen Ihrer in Azure ausgeführten Workloads weiterhin die Veeam-Lösung verwenden, die Sie kennen und der Sie vertrauen. Veeam hat die Bereitstellung der Veeam-Lösung in Azure zum Schutz von Azure-VMs und vielen weiteren Azure-Diensten sehr einfach gestaltet.

- [Bereitstellen von Veeam Backup and Replication über den Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Veeam-Website für die Azure-Sicherung und -Wiederherstellung](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Nächste Schritte

Informationen zu speziellen Verwendungsszenarien finden Sie bei den folgenden Ressourcen auf der Veeam-Website:

- [Veeam How to Videos](https://www.veeam.com/how-to-videos.html?ad=menu-resources) (Anleitungsvideos von Veeam)
- [Veeam Technical Documentations](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources) (Technische Dokumentation von Veeam)
- [Veeam Knowledge Base and FAQ](https://www.veeam.com/knowledge-base.html?ad=menu-resources) (Wissensdatenbank und häufig gestellte Fragen von Veeam)