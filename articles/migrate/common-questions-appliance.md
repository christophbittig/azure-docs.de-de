---
title: Häufig gestellte Fragen zur Azure Migrate-Appliance
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zur Azure Migrate-Appliance.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 446a59d6234219425bb24e62ecfc4796cd7fec6d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131429582"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate-Appliance: Häufig gestellte Fragen

In diesem Artikel werden häufig gestellte Fragen zur Azure Migrate-Appliance beantwortet. Wenn Sie weitere Fragen haben, sehen Sie sich die folgenden Ressourcen an:

- [Allgemeine Fragen](resources-faq.md) zu Azure Migrate
- Fragen zur [Ermittlung, Bewertung und Abhängigkeitsvisualisierung](common-questions-discovery-assessment.md)
- Fragen zur [Servermigration](common-questions-server-migration.md)
- Antworten auf Fragen im [Azure Migrate-Forum](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Was ist die Azure Migrate-Appliance?

Die Azure Migrate-Appliance ist eine einfache Appliance, die vom Azure Migrate: Ermittlungs- und Bewertungstool zum Ermitteln und Bewerten physischer oder virtueller Server von lokalen Standorten oder beliebigen Clouds aus verwendet wird. Azure Migrate: verwendet die Appliance auch für die Migration ohne Agent für lokale Server in einer VMware-Umgebung.

Weitere Informationen zur Azure Migrate-Appliance:

- Die Appliance wird lokal als physischer Server oder als virtualisierter Server bereitgestellt.
- Die Appliance ermittelt lokale Server und sendet kontinuierlich Servermetadaten und Leistungsdaten an Azure Migrate.
- Die Applianceermittlung erfolgt ohne Agent. Auf ermittelten Servern wird nichts installiert.

[Weitere Informationen](migrate-appliance.md) zur Appliance.

## <a name="how-can-i-deploy-the-appliance"></a>Wie kann ich die Appliance bereitstellen?

Die Appliance kann mithilfe verschiedener Methoden bereitgestellt werden:

- Die Appliance kann mithilfe einer Vorlage für Server bereitgestellt werden, die in einer VMware- oder Hyper-V-Umgebung ausgeführt werden ([OVA-Vorlage für VMware](how-to-set-up-appliance-vmware.md) oder [VHD für Hyper-V](how-to-set-up-appliance-hyper-v.md)).
- Wenn Sie keine Vorlage verwenden möchten, können Sie die Appliance für eine VMware- oder Hyper-V-Umgebung mithilfe eines [PowerShell-Installationsskripts](deploy-appliance-script.md) bereitstellen.
- In Azure Government sollten Sie die Appliance mithilfe eines PowerShell-Installationsskripts bereitstellen. Die Schritte zur Bereitstellung finden Sie [hier](deploy-appliance-script-government.md).
- Für physische oder virtualisierte Server in der lokalen Umgebung oder in einer beliebigen anderen Cloud stellen Sie die Appliance immer mithilfe eines PowerShell-Installationsskripts bereit. Die Schritte zur Bereitstellung finden Sie [hier](how-to-set-up-appliance-physical.md).

## <a name="how-does-the-appliance-connect-to-azure"></a>Wie stellt die Appliance eine Verbindung mit Azure her?

Die Appliance kann eine Verbindung über das Internet oder mithilfe von Azure ExpressRoute herstellen. 

- Vergewissern Sie sich, dass die Appliance eine Verbindung mit diesen [Azure-URLs](./migrate-appliance.md#url-access) herstellen kann. 
- Sie können ExpressRoute mit Microsoft-Peering verwenden. Öffentliches Peering ist veraltet und steht für neue ExpressRoute-Leitungen nicht mehr zur Verfügung.
- Das rein private Peering wird nicht unterstützt.

## <a name="does-appliance-analysis-affect-performance"></a>Wirkt sich die Applianceanalyse auf die Leistung aus?

Die Azure Migrate-Appliance erstellt fortlaufend Profile von lokalen Servern zur Messung von Leistungsdaten. Diese Profilerstellung hat fast keinen Einfluss auf die Leistung von Servern, für die das Profil erstellt wird.

## <a name="can-i-harden-the-appliance"></a>Kann ich die Appliance härten?

Wenn Sie die heruntergeladene Vorlage zum Erstellen der Appliance verwenden, können Sie der Vorlage Komponenten (beispielsweise ein Antivirenprogramm) hinzufügen. Stellen Sie sicher, dass Sie über Azure Firewall Zugriff auf die richtigen [URLs](migrate-appliance.md#public-cloud-urls) gewährt haben und dass der Ordner *%ProgramData%\MicrosoftAzure* von der Antivirenprüfung ausgeschlossen ist.

## <a name="what-network-connectivity-is-required"></a>Welche Netzwerkkonnektivität ist erforderlich?

Die Appliance benötigt Zugriff auf Azure-URLs. [Überprüfen](migrate-appliance.md#url-access) Sie die URL-Liste.

## <a name="what-data-does-the-appliance-collect"></a>Welche Daten werden von der Appliance erfasst?

In den folgenden Artikeln finden Sie Informationen zu den Daten, die von der Azure Migrate-Appliance auf Servern erfasst werden:

- **Server in der VMware-Umgebung**: [überprüfen](migrate-appliance.md#collected-data---vmware) gesammelter Daten.
- **Server in der Hyper-V-Umgebung**: [überprüfen](migrate-appliance.md#collected-data---hyper-v) gesammelter Daten.
- **Physische oder virtuelle Server**:[Überprüfen](migrate-appliance.md#collected-data---physical) gesammelter Daten.

## <a name="how-is-data-stored"></a>Wie werden die Daten gespeichert?

Die von der Azure Migrate-Appliance erfassten Daten werden an dem Azure-Speicherort gespeichert, an dem Sie das Projekt erstellt haben.

Weitere Informationen zur Speicherung von Daten:

- Die erfassten Daten werden sicher in CosmosDB in einem Microsoft-Abonnement gespeichert. Die Daten werden gelöscht, wenn Sie das Projekt löschen. Die Speicherung wird von Azure Migrate durchgeführt. Sie können nicht speziell ein Speicherkonto für erfasste Daten auswählen.
- Wenn Sie die [Visualisierung von Abhängigkeiten](concepts-dependency-visualization.md) verwenden, werden die erfassten Daten in einem Azure Log Analytics-Arbeitsbereich gespeichert, der in Ihrem Azure-Abonnement erstellt wurde. Die Daten werden gelöscht, wenn Sie den Log Analytics-Arbeitsbereich in Ihrem Abonnement löschen.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Wie viele Daten werden bei der kontinuierlichen Profilerstellung hochgeladen?

Das an Azure Migrate gesendete Datenvolumen hängt von mehreren Parametern ab. Beispielsweise übermittelt ein Projekt mit 10 Servern (jeweils mit einem Datenträger und einer NIC) pro Tag rund 50 MB Daten. Dieser ist ein ungefährer Wert. Der tatsächliche Wert ist je nach Anzahl der Datenpunkte für die Datenträger und NICs unterschiedlich. Wenn sich die Anzahl der Server, Datenträger oder NICs erhöht, steigt die Menge der gesendeten Daten nicht linear.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Werden die Daten im Ruhezustand und bei der Übertragung verschlüsselt?

Ja, in beiden Fällen:

- Metadaten werden sicher über das Internet per HTTPS an den Azure Migrate-Dienst gesendet.
- Metadaten werden in einer [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md)-Datenbank und in [Azure Blob Storage](../storage/common/storage-service-encryption.md) in einem Microsoft-Abonnement gespeichert. Die Metadaten werden im Ruhezustand für die Speicherung verschlüsselt.
- Die für die Abhängigkeitsanalyse gesammelten Daten werden ebenfalls während der Übertragung verschlüsselt (durch sicheres HTTPS). Sie werden in einem Log Analytics-Arbeitsbereich in Ihrem Abonnement gespeichert. Die Daten werden im Ruhezustand für die Abhängigkeitsanalyse verschlüsselt.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Wie stellt die Appliance eine Verbindung mit vCenter Server her?

In den folgenden Schritten wird beschrieben, wie die Appliance eine Verbindung mit VMware vCenter Server herstellt:

1. Die Appliance stellt mithilfe der von Ihnen bei der Einrichtung der Appliance angegebenen Anmeldeinformationen eine Verbindung mit vCenter Server (Port 443) her.
2. Die Appliance nutzt VMware PowerCLI, um vCenter Server abzufragen und Metadaten über die von vCenter Server verwalteten Server zu sammeln.
3. Die Appliance erfasst Konfigurationsdaten zu Servern (Kerne, Arbeitsspeicher, Datenträger, NICs) sowie den Leistungsverlauf für jeden Server für den letzten Monat.
4. Die gesammelten Metadaten werden zur Bewertung an das Azure Migrate-Ermittlungs- und Bewertungstool (über das Internet per HTTPS) gesendet.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Kann die Azure Migrate-Appliance eine Verbindung mit mehreren vCenter Server-Instanzen herstellen?

Nein. Zwischen einer [Azure Migrate-Appliance](migrate-appliance.md) und vCenter Server besteht eine 1: 1-Zuordnung. Wenn Sie Server in mehreren vCenter Server-Instanzen ermitteln möchten, müssen Sie mehrere Appliances bereitstellen.

## <a name="can-a-project-have-multiple-appliances"></a>Kann ein Projekt über mehrere Appliances verfügen?

Bei einem Projekt können mehrere Appliances registriert sein. Eine Appliance kann jedoch nur mit einem Projekt registriert werden.

## <a name="how-do-i-find-the-azure-migrate-appliances-registered-to-the-project"></a>Wie sind die für das Projekt registrierten Azure Migrate Anwendungen zu finden?
1. Navigieren Sie im Azure-Portal zur [Azure Migrate Startseite](https://portal.azure.com/?feature.customportal=false&feature.showassettypes=Microsoft_Azure_Migrate_AzureMigrationHub&feature.smsMigrationTool=true&feature.cloudamizeAssessmentTool=true&feature.sasAssessmentTool=true&feature.firstPartyDiscoveredMachines=true#blade/Microsoft_Azure_Migrate/AmhResourceMenuBlade/getStarted) und wählen Sie im Menü links **Server, Datenbanken und Web-Apps** aus.
1. Wählen Sie in der oberen rechten Ecke **Ändern** aus, um Ihr Projekt auszuwählen.
1. Wählen Sie im Projekt Azure Migrate die Option **Übersicht** aus „Azure Migrate: Ermittlung & Bewertung“ aus.
1. Wählen Sie unter **Übersicht** im linken Menü **Anwendungen (Appliances)** aus, um die für das Projekt registrierten Anwendungen und den Konnektivitätsstatus der Agents auf der Anwendung anzuzeigen.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Kann die Azure Migrate-Appliance/Replikations-Appliance eine Verbindung mit demselben vCenter herstellen?

Ja. Sie können sowohl die Azure Migrate-Appliance (wird für die Bewertung und die VMware-Migration ohne Agent verwendet) als auch die Replikations-Appliance (wird für die Agent-basierte Migration von Servern unter VMware verwendet) demselben vCenter-Server hinzufügen. Stellen Sie jedoch sicher, dass Sie nicht beide Appliances auf demselben Server einrichten und dass diese derzeit nicht unterstützt wird.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Wie viele Server können mit einer Appliance ermittelt werden?

Sie können in der VMware-Umgebung bis zu 10.000, in der Hyper-V-Umgebung bis zu 5.000 und mit einer einzelnen Appliance bis zu 1.000 physische Server ermitteln. Wenn Sie in Ihrer lokalen Umgebung mehr Server haben, informieren Sie sich über die Skalierung der [Hyper-V-](scale-hyper-v-assessment.md), [VMware-](scale-vmware-assessment.md) und [physischen](scale-physical-assessment.md) Bewertung.

## <a name="can-i-delete-an-appliance"></a>Kann ich eine Appliance löschen?

Das Löschen einer Appliance aus dem Projekt wird derzeit nicht unterstützt.

Die einzige Möglichkeit zum Löschen der Appliance besteht darin, die Ressourcengruppe zu löschen, die das Projekt enthält, das mit der Appliance verknüpft ist.

Beim Löschen der Ressourcengruppe werden jedoch auch andere registrierte Appliances, der ermittelte Bestand, Bewertungen und alle anderen Azure-Komponenten in der Ressourcengruppe, die dem Projekt zugeordnet sind, gelöscht.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Kann ich die Appliance mit einem anderen Abonnement oder Projekt verwenden?

Um die Appliance mit einem anderen Abonnement oder Projekt zu verwenden, müssen Sie die vorhandene Appliance neu konfigurieren, indem Sie das PowerShell-Installationsskript für das spezifische Szenario (VMware/Hyper-V/Physisch) auf der Appliance ausführen. Das Skript wird die vorhandenen Appliancekomponenten und Einstellungen bereinigen, um eine neue Appliance bereitzustellen. Stellen Sie sicher, dass der Browsercache gelöscht wird, bevor Sie den neu bereitgestellten Appliance-Konfigurations-Manager verwenden.

Außerdem können Sie einen vorhandenen Projektschlüssel auf einer neu konfigurierten Appliance nicht wiederverwenden. Stellen Sie sicher, dass Sie aus dem gewünschten Abonnement/Projekt einen neuen Schlüssel generieren, um die Registrierung der Appliance abzuschließen.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Kann ich die Appliance auf einem virtuellen Azure-Computer einrichten?

Nein. Diese Option wird derzeit nicht unterstützt.

## <a name="can-i-discover-on-an-esxi-host"></a>Kann die Ermittlung auf einem ESXi-Host durchgeführt werden?

Nein. Um Server in einer VMware-Umgebung zu ermitteln, müssen Sie über vCenter Server verfügen.

## <a name="how-do-i-update-the-appliance"></a>Wie aktualisiere ich die Appliance?

Standardmäßig werden die Appliance und die zugehörigen installierten Agents automatisch aktualisiert. Die Appliance prüft alle 24 Stunden, ob Updates verfügbar sind. Fehlerhafte Updates werden wiederholt.

Nur die Appliance und die Appliance-Agents werden durch diese automatischen Updates aktualisiert. Das Betriebssystem wird durch automatische Azure Migrate-Updates nicht aktualisiert. Verwenden Sie Windows-Updates, um das Betriebssystem auf dem neuesten Stand zu halten.

## <a name="can-i-check-agent-health"></a>Kann ich die Agent-Integrität überprüfen?

Ja. Navigieren Sie im Portal zur Seite **Agent-Integrität** für das Azure Migrate: Ermittlungs- und Bewertungstool oder Azure Migrate: Servermigrationstool. Dort können Sie den Verbindungsstatus zwischen Azure und den Ermittlungs- und Bewertungs-Agents auf der Appliance überprüfen.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Kann ich auf der VMware-Appliance mehrere Serveranmeldeinformationen hinzufügen?

Ja, wir unterstützen jetzt mehrere Serveranmeldeinformationen, um Softwareinventur (Ermittlung installierter Anwendungen), Abhängigkeitsanalyse ohne Agent und Ermittlung von SQL Server-Instanzen und -Datenbanken durchzuführen. [Weitere Informationen](tutorial-discover-vmware.md#provide-server-credentials) zum Bereitstellen von Anmeldeinformationen finden Sie im Konfigurations-Manager für Appliances.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Welche Art von Serveranmeldeinformationen kann ich auf der VMware-Appliance hinzufügen?

Sie können Anmeldeinformationen für die Authentifizierung für eine Domäne, Windows (ohne Domäne), Linux (ohne Domäne) oder SQL Server im Konfigurations-Manager für Appliances angeben. [Erfahren Sie mehr](add-server-credentials.md) zur Bereitstellung von Anmeldeinformationen und zu deren Handhabung.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Welche Art von SQL Server-Verbindungseigenschaften wird von Azure Migrate für die SQL-Ermittlung unterstützt?

Azure Migrate verschlüsselt die Kommunikation zwischen der Azure Migrate-Appliance und den SQL Server-Quellinstanzen (durch auf TRUE festgelegte Eigenschaft „Verbindung verschlüsseln“). Diese Verbindungen sind mit (auf TRUE festgelegter Option) [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) verschlüsselt. Die Transportschicht verwendet SSL, um den Kanal zu verschlüsseln und die Zertifikatskette zum Überprüfen der Vertrauenswürdigkeit zu umgehen. Der Server der Appliance muss so eingerichtet sein, dass er die [Stammzertifizierungsstelle des Zertifikats als vertrauenswürdig einstuft](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Wenn auf dem Server kein Zertifikat bereitgestellt wurde, erstellt SQL Server beim Starten ein selbst signiertes Zertifikat zum Verschlüsseln von Anmeldepaketen. [Weitere Informationen](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Übersicht zu Azure Migrate](migrate-services-overview.md) an.
