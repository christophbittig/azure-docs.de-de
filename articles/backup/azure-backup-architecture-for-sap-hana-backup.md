---
title: Azure Backup-Architektur für SAP HANA-Sicherungen
description: Weitere Informationen zur Azure Backup-Architektur für SAP HANA-Sicherungen.
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: df8bb6adfeff88eafba19bcc459f887f075aa75b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244576"
---
# <a name="azure-backup-architecture-for-sap-hana-backup"></a>Azure Backup-Architektur für SAP HANA-Sicherungen

Der [Azure Backup-Dienst](./backup-overview.md) ermöglicht es Ihnen, Daten aus SAP HANA-Datenbanken in einer Anwendung konsistent zu sichern. In diesem Artikel werden die Architektur, die Komponenten und die Prozesse von Azure Backup beschrieben.

## <a name="how-does-azure-backup-work-with-sap-hana-databases"></a>Wie funktioniert Azure Backup mit SAP HANA-Datenbanken?

Azure Backup stellt eine Streamingsicherungslösung zum Sichern von auf einem virtuellen Azure-Computer ausgeführten SAP HANA-Datenbanken zur Verfügung. Für dieses Sicherungsangebot ist ein Setup ohne Infrastruktur erforderlich. Es muss somit keine Sicherungsinfrastruktur bereitgestellt und verwaltet werden.

Azure Backup ist SAP [Backint-zertifiziert](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) und bietet native Sicherungsunterstützung durch Verwendung der nativen APIs von SAP HANA. Mit dieser Lösung können Sie SAP HANA-Datenbanken, die auf virtuellen Azure-Computern ausgeführt werden, nahtlos sichern und wiederherstellen und die von Azure Backup bereitgestellten Unternehmensverwaltungsfunktionen nutzen.

[Erfahren Sie mehr über den zusätzlichen Wert, den Azure Backup für SAP HANA bietet.](./sap-hana-db-about.md#added-value)

## <a name="where-is-the-data-backed-up"></a>Wo werden die Daten gesichert?

Azure Backup speichert die gesicherten Daten in einem Recovery Services-Tresor. Ein Tresor ist eine Onlinespeicherentität in Azure, die zum Speichern von Daten wie Sicherungskopien, Wiederherstellungspunkten und Sicherungsrichtlinien verwendet wird.

[Erfahren Sie mehr zum Recovery Services-Tresor.](./backup-azure-backup-faq.yml)

## <a name="backup-agents"></a>Sicherungs-Agents

Zum Sichern von SAP HANA-Datenbanken, die auf virtuellen Azure-Computer ausgeführt werden, müssen Sie die Installation des Plug-Ins (SAP HANA Backup-Agent) auf dem virtuellen Azure-Computer zulassen. Dieses Plug-In stellt eine Verbindung mit der HANA Backint-Schnittstelle her und unterstützt den Azure Backup-Dienst beim Verschieben von Daten in den Tresor. Außerdem ermöglicht es Azure Backup, Wiederherstellungen durchzuführen.

## <a name="backup-types"></a>Sicherungstypen

[Weitere Informationen](./backup-architecture.md#sap-hana-backup-types) zu SAP HANA-Sicherungstypen.

## <a name="about-architecture"></a>Informationen zur Architektur

Weitere Informationen zur [allgemeinen Architektur von Azure Backup für SAP HANA Datenbanken](./sap-hana-db-about.md#backup-architecture). Ausführliche Informationen zum Sicherungsprozess finden Sie im folgenden Prozess:

:::image type="content" source="./media/sap-hana-db-about/backup-architecture.png" alt-text="Diagramm des Sicherungsprozesses für die SAP HANA-Datenbank.":::

1. Um den Sicherungsvorgang zu beginnen, [erstellen Sie einen Recovery Services-Tresor](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) in Azure. Dieser Tresor dient zum Speichern der Sicherungen und Wiederherstellungspunkte, die im Laufe der Zeit erstellt werden.

1. Der virtuelle Azure-Computer, auf einem der SAP HANA-Server ausgeführt wird, ist beim Tresor registriert, und die zu sichernden Datenbanken werden [ermittelt](./tutorial-backup-sap-hana-db.md#discover-the-databases). Um dem Azure Backup-Dienst das Ermitteln von Datenbanken zu ermöglichen, müssen Sie ein [Vorregistrierungsskript](https://go.microsoft.com/fwlink/?linkid=2173610) auf dem HANA-Server als root-Benutzer ausführen. 
   >[!Note]
   >Stellen Sie sicher, dass die HANA-Instanz während der Ermittlung der Datenbanken in dieser Instanz ausgeführt wird.

1. Stellen Sie außerdem sicher, [dass die übrigen Voraussetzungen](./tutorial-backup-sap-hana-db.md#prerequisites) erfüllt sind.

   >[!Important]
   >Stellen Sie sicher, dass die Voraussetzungen zum Einrichten der richtigen Netzwerkkonnektivität erfüllt sind. Weitere Informationen finden Sie in der Empfehlung zum [Einrichten von in SAP HANA ausgeführten virtuellen Azure-Computern mit zusätzlichen Netzwerkkomponenten zur Verwendung des Sicherungsangebots](./tutorial-backup-sap-hana-db.md#set-up-network-connectivity).

1. Sehen Sie sich die Einzelheiten über die [Funktionsweise des Vorregistrierungsskripts](./tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) an. Wenn Sie versuchen, die Sicherung für SAP HANA-Datenbanken zu konfigurieren, ohne dieses Skript auszuführen, wird möglicherweise der Fehler _UserErrorHanaScriptNotRun_ angezeigt.

1. Der Azure Backup-Dienst installiert jetzt das Azure Backup-Plug-In für HANA auf dem registrierten SAP HANA-Server. Dieses Plug-In verwendet den vom Vorregistrierungsskripts erstellten Sicherungsbenutzer zum Ausführen aller Sicherungs- und Wiederherstellungsvorgänge.

1. Zum [Konfigurieren der Sicherung](./tutorial-backup-sap-hana-db.md#configure-backup) auf den ermittelten Datenbanken wählen Sie die erforderliche Sicherungsrichtlinie aus, und aktivieren Sie Sicherungen.

1. Azure Backup für SAP HANA (eine Backint-zertifizierte Lösung) ist nicht von den zugrunde liegende Datenträger- oder VM-Typen abhängig. Für die Sicherung werden von SAP HANA generierte Datenströme verwendet.

## <a name="backup-flow"></a>Sicherungsflow

1. Die geplanten Sicherungen werden durch auf dem virtuellen HANA-Computer erstellte crontab-Einträge verwaltet, während die bedarfsgesteuerten Sicherungen direkt vom Azure Backup-Dienst ausgelöst werden.

1. Sobald das Sicherungsmodul bzw. die Backint-Schnittstelle von SAP HANA die Sicherungsanforderung empfängt, wird die SAP HANA-Datenbank auf eine Sicherung vorbereitet, indem ein Sicherungspunkt erstellt und Daten auf zugrunde liegende Speichervolumes verschoben werden.

1. Backint führt dann den Lesevorgang aus den zugrunde liegenden Datenvolumes aus – dem Indexserver und der XS-Engine für die Mandantendatenbank und dem Nameserver für SYSTEMDB. Premium SSD-Datenträger können einen optimalen E/A-Durchsatz für den Sicherungsstreamingvorgang bereitstellen. Die Verwendung von nicht zwischengespeicherten Datenträgern mit M64Is kann jedoch höhere Geschwindigkeiten bieten.

1. Zum Streamen der Sicherungsdaten erstellt Backint bis zu drei Pipes, die direkt in den Recovery Services-Tresor von Azure Backup schreiben.

   Wenn Sie in Ihrem Setup keine Firewall/NVA verwenden, wird der Sicherungsstream über das Azure-Netzwerk in den Recovery Services-Tresor übertragen. Darüber hinaus können Sie einen [virtuellen Netzwerkdienst-Endpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) oder einen [privaten Endpunkt](../private-link/private-endpoint-overview.md) einrichten, damit SAP HANA Sicherungsdatenverkehr direkt und ohne die NVA/Azure Firewall zu durchlaufen, an Azure Storage senden kann. Darüber hinaus wird bei Verwendung von Firewall/NVA der Datenverkehr an Azure Active Directory und den Recovery Services-Tresor durch die Firewall bzw. das NVA übertragen und wirkt sich nicht auf die Gesamtleistung der Sicherung aus. 

1. Azure Backup versucht, Geschwindigkeiten von bis zu 420 MB/s für nicht protokollbasierte Sicherungen und bis zu 100 MB/s für Protokollsicherungen zu erreichen. [Erfahren Sie mehr über die Durchsatzleistung bei der Sicherung und Wiederherstellung.](./tutorial-backup-sap-hana-db.md#understanding-backup-and-restore-throughput-performance)

1. Ausführliche Protokolle werden in die Dateien _backup.log_ und _backint.log_ auf der SAP HANA-Instanz geschrieben.

1. Nach Abschluss des Sicherungsstreamings wird der Katalog an den Recovery Services-Tresor gestreamt. Wenn sowohl die Sicherung (vollständig/differenziell/inkrementell/Protokoll) als auch der Katalog für diese Sicherung erfolgreich gestreamt und im Recovery Services-Tresor gespeichert wurden, betrachtet Azure Backup den Sicherungsvorgang als erfolgreich.

Sehen Sie sich die folgenden SAP HANA-Setups und die Ausführung des oben erwähnten Sicherungsvorgangs an:

**SAP HANA-Setupszenario: Azure-Netzwerk – ohne NVA/Azure Firewall**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-without-nva-or-azure-firewall.png" alt-text="Diagramm des SAP HANA-Setups mit einem Azure-Netzwerk ohne NVA/Azure Firewall.":::

**SAP HANA-Setupszenario: Azure-Netzwerk – mit UDR + NVA/Azure Firewall**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall.png" alt-text="Diagramm des SAP HANA-Setups mit einem Azure-Netzwerk mit UDR und NVA/Azure Firewall.":::

>[!Note]
>NVA/Azure Firewall kann einen Mehraufwand verursachen, wenn SAP HANA die Sicherung an Azure Storage bzw. in einen Recovery Services-Tresor streamt (Datenebene). Siehe _Punkt 6_ im obigen Diagramm.

**SAP HANA-Setupszenario: Azure-Netzwerk – mit UDR + NVA/Azure Firewall + privatem Endpunkt oder Dienstendpunkt**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall-and-private-endpoint-or-service-endpoint.png" alt-text="Diagramm des SAP HANA-Setups mit Azure-Netzwerk mit UDR und NVA/Azure Firewall und privatem Endpunkt oder Dienstendpunkt.":::

## <a name="next-steps"></a>Nächste Schritte

[Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern](./backup-azure-sap-hana-database.md).
