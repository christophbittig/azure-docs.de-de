---
title: Sicherung und Notfallwiederherstellung für Azure Australien
description: Sicherung und Notfallwiederherstellung in Microsoft Azure für australische Regierungsbehörden gemäß ASD Essentials 8
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: c685de3050d35f568c8921b99a078e1b2bd68f74
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347996"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Sicherung und Notfallwiederherstellung für Azure Australien

Das Vorhandensein von Sicherungs- und Notfallwiederherstellungsplänen mit unterstützender Infrastruktur ist für alle Organisationen von entscheidender Bedeutung. Die Bedeutung einer Sicherungslösung wird durch ihre Einbeziehung in [Essential 8 des Australian Cyber Security Centre (ACSC)](https://acsc.gov.au/publications/protect/essential-eight-explained.htm) hervorgehoben.

Microsoft Azure bietet zwei Dienste, die Resilienz ermöglichen: Azure Backup und Azure Site Recovery. Diese Dienste ermöglichen es Ihnen, Ihre Daten für eine Vielzahl von Entwurfsszenarien sowohl lokal als auch in der Cloud zu schützen. Azure Backup und Azure Site Recovery verwenden eine allgemeine Speicher- und Verwaltungsressource: den Azure Recovery Services-Tresor. Dieser Tresor dient zum Verwalten, Überwachen und Trennen von Azure Backup- und Azure Site Recovery-Daten.

In diesem Artikel werden die wichtigsten Entwurfselemente für das Implementieren von Azure Backup und Azure Site Recovery in Einklang mit den [Kontrollen im ISM (Information Security Manual) des Australian Signals Directorate (ASD)](https://acsc.gov.au/infosec/ism/index.htm) ausführlich erläutert.

## <a name="azure-backup"></a>Azure Backup

![Azure Backup](media/backup-overview.png)

Azure Backup ähnelt einer herkömmlichen lokalen Sicherungslösung und bietet die Möglichkeit, sowohl lokale als auch in Azure gehostete Daten zu sichern. Azure Backup kann verwendet werden, um die folgenden Datentypen in Azure sichern:

* Dateien und Ordner
* Unterstützte Windows- und Linux-Betriebssysteme, gehostet auf:
  * Hyper-V- und VMWare-Hypervisors
  * Physische Hardware
* Unterstützte Microsoft-Anwendungen

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery repliziert Workloads, bestehend aus einem einzelnen virtuellen Computer oder Anwendungen mit mehreren Ebenen. Die Replikation wird von lokalen Standorten nach in Azure, zwischen Azure-Regionen oder zwischen lokalen, von Azure Site Recovery orchestrierten Standorten unterstützt. Lokale virtuelle Computer können nach Azure oder auf einen unterstützten lokalen Hypervisor repliziert werden. Nach der Konfiguration orchestriert Azure Site Recovery die Replikation, das Failover und das Failback.

## <a name="key-design-considerations"></a>Wichtige Entwurfsaspekte

Beim Implementieren einer Sicherungs- oder Notfallwiederherstellungslösung muss in der vorgeschlagenen Lösung Folgendes berücksichtigt werden:

* Der Umfang und die Menge der zu erfassenden Daten
* Die Dauer der Aufbewahrung der Daten
* Die sichere Speicherung und Verwaltung dieser Daten
* Die geografischen Standorte, an denen die Daten gespeichert werden
* Routinemäßige Systemtestverfahren

Das ISM bietet Anleitungen zu den Sicherheitsüberlegungen, die beim Entwerfen einer Lösung vorgenommen werden sollten. Microsoft Azure stellt die Mittel bereit, um diese Sicherheitsüberlegungen zu berücksichtigen.

### <a name="data-sovereignty"></a>Datenhoheit

Organisationen müssen sicherstellen, dass die Datenhoheit bei Verwendung cloudbasierter Speicherstandorte gewahrt bleibt. Azure Policy bietet die Möglichkeit, die zulässigen Speicherorte einzuschränken, an denen eine Azure-Ressource erstellt werden kann. Mit dem integrierten Azure Policy-Element „Zulässige Speicherorte“ kann sichergestellt werden, dass alle im Rahmen eines zugewiesenen Azure Policy-Elements erstellten Azure-Ressourcen nur an den aufgeführten geografischen Standorten erstellt werden können.

Die Azure Policy-Elemente für geografische Einschränkung für Azure-Ressourcen lauten wie folgt:

* allowedLocations
* allowedSingleLocation

Diese Richtlinien ermöglichen es Azure-Administratoren, die Erstellung auf eine Liste aufgeführter Standorte oder sogar einen einzigen geografischen Standort zu beschränken.

### <a name="redundant-and-geographically-dispersed-storage"></a>Redundanter und geografisch verteilter Speicher

Die im Azure Recovery Services-Tresor gespeicherten Daten werden immer in redundantem Speicher gespeichert. Standardmäßig verwendet der Recovery Services-Tresor georedundanten Azure-Speicher (GRS). Mithilfe von GRS gespeicherte Daten werden in andere Azure-Rechenzentren in der [sekundären gekoppelten Region](../best-practices-availability-paired-regions.md) des Recovery Services-Tresors repliziert. Diese replizierten Daten werden schreibgeschützt gespeichert, und der Schreibschutz wird nur im Falle eines Azure-Failoverereignis aufgehoben. Innerhalb des Azure-Rechenzentrums werden die Daten zwischen separaten Fehlerdomänen und Upgradedomänen repliziert, um die Wahrscheinlichkeit eines Hardware- oder wartungsbasierten Ausfalls zu minimieren. GRS bietet eine jährliche Verfügbarkeit von mindestens 99,99999999999999 %.

Der Azure Recovery Services-Tresor kann so konfiguriert werden, dass er lokal redundanten Speicher (LRS) nutzt. LRS ist eine kostengünstigere Speicheroption mit dem Nachteil der geringeren Verfügbarkeit. Dieses Redundanzmodell verwendet die gleiche Replikation zwischen separaten Fehlerdomänen und Upgradedomänen, die Replikation erfolgt jedoch nicht zwischen geografischen Regionen. Daten, die sich in LRS-Speicher befinden, bieten zwar nicht die gleiche Resilienz wie GRS, aber trotzdem eine Verfügbarkeit von mindestens 99,999999999 % jährlich.

Im Gegensatz zu herkömmlichen Offsitespeichertechnologien, wie z.B. Bandmedien, werden die zusätzlichen Kopien der Daten automatisch erstellt und erfordern keinen zusätzlichen Verwaltungsaufwand.

### <a name="restricted-access-and-activity-monitoring"></a>Eingeschränkter Zugriff und Aktivitätsüberwachung

Sicherungsdaten müssen vor Beschädigungen, Änderungen und nicht genehmigtem Löschungen geschützt werden. Sowohl Azure Backup als auch Azure Site Recovery nutzen das allgemeine Azure-Verwaltungsfabric. Dieses Fabric bietet ausführliche Überwachung, Protokollierung und rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) für in Azure befindliche Ressourcen. Der Zugriff auf Sicherungsdaten kann auf ausgewählte Administrationsmitarbeiter beschränkt werden, und alle Aktionen im Zusammenhang mit Sicherungsdaten können protokolliert und überwacht werden.

Sowohl Azure Backup als auch Azure Site Recovery verfügen über integrierte Protokollierungs- und Berichtsfunktionen. Alle Probleme, die bei der Sicherung oder Replikation auftreten, werden Administratoren unter Verwendung des Azure-Verwaltungsfabrics gemeldet.

Der Azure Recovery Services-Tresor setzt außerdem die folgenden zusätzlichen Sicherheitsmaßnahmen ein:

* Sicherungsdaten werden nach einem Löschvorgang 14 Tage lang aufbewahrt.
* Warnungen und Benachrichtigungen für kritische Vorgänge, z.B. das Beenden der Sicherung mit Löschen von Daten
* Sicherheits-PIN-Anforderungen für kritische Vorgänge
* Überprüfungen der minimalen Aufbewahrungsdauer sind vorhanden.

Diese Überprüfungen der minimalen Aufbewahrungsdauer umfassen Folgendes:

* Bei einer täglichen Aufbewahrung mindestens sieben Aufbewahrungstage
* Bei einer wöchentlichen Aufbewahrung mindestens vier Aufbewahrungswochen
* Bei einer monatlichen Aufbewahrung mindestens drei Aufbewahrungsmonate
* Bei einer jährlichen Aufbewahrung mindestens ein Aufbewahrungsjahr

Alle in Azure gespeicherten Sicherungsdaten werden im Ruhezustand mithilfe von Azure-Speicherdienstverschlüsselung (SSE) verschlüsselt. Dies ist für alle neuen und vorhandenen Speicherkonten standardmäßig aktiviert und kann nicht deaktiviert werden. Die verschlüsselten Daten werden beim Abrufen automatisch entschlüsselt. Standardmäßig werden mithilfe von SSE verschlüsselte Daten mit einem Schlüssel verschlüsselt, der von Microsoft bereitgestellt und verwaltet wird. Organisationen können sich für die Bereitstellung und Verwaltung Ihres eigenen Verschlüsselungsschlüssels für die Verwendung mit SSE entscheiden. Dadurch wird eine optionale zusätzliche Sicherheitsebene für die verschlüsselten Daten bereitstellt. Dieser Schlüssel kann vom Kunden lokal oder sicher im Azure-Schlüsseltresor gespeichert werden.

### <a name="secure-data-transport"></a>Sicherer Datentransport

Azure Backup-Daten werden bei der Übertragung mit AES 256 verschlüsselt. Diese Daten werden durch die Verwendung einer Passphrase geschützt, die bei der ersten Konfiguration der Sicherung von Administrationsmitarbeitern erstellt wird. Microsoft hat keinen Zugriff auf diese Passphrase, das bedeutet, dass der Kunde sicherstellen muss, dass diese Passphrase sicher aufbewahrt wird. Die Datenübertragung erfolgt dann zwischen der lokalen Umgebung und dem Azure Recovery Services-Tresor über eine sichere HTTPS-Verbindung.  Die Daten im Recovery Services-Tresor werden dann im Ruhezustand unter Verwendung von Azure SSE verschlüsselt.

Azure Site Recovery-Daten werden bei der Übertragung ebenfalls immer verschlüsselt. Alle replizierten Daten werden mithilfe von HTTPS und TLS sicher nach Azure übertragen. Wenn ein Azure-Kunde die Verbindung zu Azure über eine ExpressRoute-Verbindung herstellt, werden Azure Site Recovery-Daten über diese private Verbindung gesendet.  Wenn ein Azure-Kunde die Verbindung zu Azure über eine VPN-Verbindung herstellt, werden die Daten zwischen dem lokalen Standort und dem Recovery Services-Tresor sicher über das Internet repliziert.

Durch diese sichere Netzwerkdatenübertragung entfallen die Sicherheitsrisiken und Anforderungen zur Risikominderung für die Verwaltung herkömmlicher Offsite-Sicherungsspeicherlösungen, z.B. Bandmedien.

### <a name="data-retention-periods"></a>Datenaufbewahrungszeiträume

Es wird eine minimale Aufbewahrungszeitraum der Sicherung von drei Monaten empfohlen, aber häufig sind längere Aufbewahrungszeiträume erforderlich. Azure Backup kann bis zu 9999 Kopien einer Sicherung bereitstellen. Würde täglich eine einzelne Azure-Sicherung einer geschützten Instanz erstellt, könnten täglich ausgeführte Sicherungen 27 Jahre aufbewahrt werden. Einzelne monatliche Sicherungen einer geschützten Instanz können 833 Jahre aufbewahrt werden. Wenn Sicherungsdaten veraltet sind und im Laufe der Zeit weniger häufige Sicherungen aufbewahrt werden, wächst das gesamte Zeitfenster für die Aufbewahrung der Sicherungsdaten.  Azure beschränkt nicht die Dauer für die Aufbewahrung der Daten in einem Azure Recovery Services-Tresor, sondern nur die Gesamtanzahl der Sicherungen pro Instanz. Es gibt auch keinen Leistungsunterschied zwischen der Wiederherstellung von alten oder neuen Sicherungen. Jede Wiederherstellung dauert gleich lang.

Der Azure Recovery Services-Tresor verfügt über eine Reihe von standardmäßigen Sicherungs- und Aufbewahrungsrichtlinien.  Administrationsmitarbeitern können auch benutzerdefinierte Sicherungs- und Aufbewahrungsrichtlinien erstellen.

![Azure Backup-Richtlinie](media/create-policy.png)

Beim Konfigurieren von Azure Backup und Aufbewahrungsrichtlinien müssen Sie ein ausgewogenes Verhältnis zwischen der Sicherungshäufigkeit und den Anforderungen für die langfristige Aufbewahrung finden.

### <a name="backup-and-restore-testing"></a>Testen von Sicherung und Wiederherstellung

Im ISM wird empfohlen, Sicherungsdaten zu testen, um sicherzustellen, dass die geschützten Daten gültig sind, wenn eine Wiederherstellung oder ein Failover erforderlich wird. Azure Backup und Azure Site Recovery bieten die Möglichkeit, geschützte Daten zu testen, nachdem Sie gesichert oder repliziert wurden. Von Azure Backup verwaltete Daten können an einem angegebenen Speicherort wiederhergestellt werden, und die Konsistenz der Daten kann dann überprüft werden.

Azure Site Recovery verfügt über eine integrierte Funktion zum Ausführen von Failovertests. In den Recovery Services-Tresor replizierte Workloads können in einer angegebenen Azure-Umgebung wiederhergestellt werden. Die Zielumgebung für die Wiederherstellung kann vollständig von jeder Produktionsumgebung isoliert werden, um sicherzustellen, dass die Produktionssysteme beim Ausführen eines Tests nicht beeinträchtigt werden. Nach Abschluss des Tests können die Testumgebung und alle Ressourcen sofort gelöscht werden, um die Betriebskosten zu senken.

Failovertests und -überprüfung können mithilfe des in die Azure-Plattform integrierten Azure Automation-Diensts automatisiert werden. Dies ermöglicht die Planung der automatischen Ausführung von Failovertests, um eine erfolgreiche Replikation der Daten in Azure sicherzustellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im Artikel zum [Gewährleisten der Sicherheit mit Azure Policy](azure-policy.md).
