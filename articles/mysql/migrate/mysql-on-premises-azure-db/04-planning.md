---
title: 'Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Planen'
description: Eine Azure-Zielzone ist die Umgebung, die als finaler Zielort für ein Cloudmigrationsprojekt definiert ist.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: e1ef403583082f1fff631392d7a4467fe253a516
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024181"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-planning"></a>Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Planen

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Bewertung](03-assessment.md)

## <a name="landing-zone"></a>Zielzone

Eine [Azure-Zielzone](/azure/cloud-adoption-framework/ready/landing-zone/) ist die Umgebung, die als letzter Zielort für ein Cloudmigrationsprojekt definiert ist. In den meisten Projekten sollte für das Anfangssetup zunächst ein Skript der Zielzone mithilfe von ARM-Vorlagen erstellt werden. Anschließend sollte die Zielzone mit PowerShell oder über das Azure-Portal angepasst werden, damit sie die Anforderungen der Workloads erfüllt.

Da sich der Sitz von WWI in San Francisco befindet, wurden alle Ressourcen für die Azure-Zielzone in der `US West 2`-Region erstellt. Die folgenden Ressourcen wurden zur Unterstützung der Migration erstellt:

- [Azure Database for MySQL](../../quickstart-create-mysql-server-database-using-azure-portal.md)

- [Azure Database Migration Service (DMS)](../../../dms/quickstart-create-data-migration-service-portal.md)

- [ExpressRoute](../../../expressroute/expressroute-introduction.md)

- [Azure Virtual Network](../../../virtual-network/quick-create-portal.md) mit [Hub-and-Spoke-Design](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) und einem entsprechenden [Peering virtueller Netzwerke](../../../virtual-network/virtual-network-peering-overview.md).

- [App Service](../../../app-service/overview.md)

- [Application Gateway](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md?tabs=option-1-create-internal-load-balancer-standard)

- [Private Endpunkte](../../../private-link/private-endpoint-overview.md) für die App Services- und MySQL-Instanz

> [!NOTE]
> Im Rahmen dieses Leitfadens werden zwei ARM-Vorlagen (eine mit und eine ohne private Endpunkte) zur Verfügung gestellt, um eine potenzielle Azure-Zielzone für ein MySQL-Migrationsprojekt bereitzustellen. Die ARM-Vorlage für private Endpunkte bietet ein sichereres und produktionsähnliches Szenario. Abhängig von den Anforderungen kann eine zusätzliche manuelle Konfiguration der Azure-Zielzone erforderlich sein.

## <a name="networking"></a>Netzwerk

Eine schnelle und optimierte Übertragung von Daten aus dem Quellsystem nach Azure Database for MySQL ist eine entscheidende Komponente, die bei einem Migrationsprojekt berücksichtigt werden muss. Bei kleinen unzuverlässigen Verbindungen müssen Administratoren die Migration möglicherweise mehrmals neu starten, bis Sie ein erfolgreiches Ergebnis erzielen. Das Neustarten von Migrationen aufgrund von Netzwerkproblemen kann mit unnötigem Aufwand verbunden sein.

Nehmen Sie sich Zeit, die Netzwerkkonnektivität zwischen Quell-, Tool- und Zielumgebungen genau zu verstehen und auszuwerten. In einigen Fällen kann es sinnvoll sein, die Internetverbindung zu aktualisieren oder eine ExpressRoute-Verbindung von der lokalen Umgebung zu Azure zu konfigurieren. Nachdem die Konnektivität zwischen den lokalen Standorten und Azure erstellt wurde, besteht der nächste Schritt darin, zu überprüfen, ob das ausgewählte Migrationstool eine Verbindung zwischen Quelle und Ziel herstellen kann.

Durch den Speicherort des Migrationstools werden die Netzwerkkonnektivitätsanforderungen festgelegt. Wie in der folgenden Tabelle dargestellt, muss das ausgewählte Migrationstool sowohl eine Verbindung mit dem lokalen Computer als auch mit Azure herstellen. Azure sollte so konfiguriert werden, dass nur Netzwerkdatenverkehr vom Speicherort des Migrationstools akzeptiert wird.

| Migrationstool | Typ | Standort | Eingehende Netzwerkanforderungen | Ausgehende Netzwerkanforderungen |
|----------------|------|----------|------------------------------|-------------------------------|
| **Database Migration Service (DMS)** | Offline | Azure| Zulassen von 3306 über eine externe IP-Adresse | Ein Pfad zum Herstellen einer Verbindung mit der Azure MySQL-Datenbankinstanz |
| **Import/Export (MySQL Workbench, mysqldump)** | Offline| Lokal | Zulassen von 3306 über eine interne IP-Adresse | Ein Pfad zum Herstellen einer Verbindung mit der Azure MySQL-Datenbankinstanz |
| **Import/Export (MySQL Workbench, mysqldump)** | Offline| Azure VM | Zulassen von 3306 über eine externe IP-Adresse | Ein Pfad zum Herstellen einer Verbindung mit der Azure MySQL-Datenbankinstanz |
| **mydumper/myloader** | Offline | Lokal | Zulassen von 3306 über eine interne IP-Adresse | Ein Pfad zum Herstellen einer Verbindung mit der Azure MySQL-Datenbankinstanz |
| **mydumper/myloader** | Offline | Azure VM | Zulassen von 3306 über eine externe IP-Adresse | Ein Pfad zum Herstellen einer Verbindung mit der Azure MySQL-Datenbankinstanz |
| **binlog**  | Offline | Lokal | Zulassen von 3306 über eine externe oder private IP-Adresse von privaten Endpunkten | Ein Pfad für jeden Replikationsserver zum Master |

Andere Netzwerküberlegungen beinhalten:

- DMS in einem VNET wird eine [dynamische öffentliche IP-Adresse](../../../dms/faq.yml) zugewiesen. Sie können den Dienst zum Zeitpunkt der Erstellung in einem virtuellen Netzwerk platzieren, das über eine [ExpressRoute](../../../expressroute/expressroute-introduction.md)-Verbindung oder über ein [Site-to-Site-VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md) verbunden ist.

- Wenn Sie einen virtuellen Azure-Computer für das Ausführen der Migrationstools verwenden, weisen Sie ihm eine öffentliche IP-Adresse zu und lassen Sie anschließend nur eine Verbindung mit der lokalen MySQL-Instanz zu.

- Ausgehende Firewalls müssen die ausgehende Konnektivität mit Azure Database for MySQL sicherstellen. Die IP-Adressen des MySQL-Gateways sind auf der Seite [Verbindungsarchitektur in Azure Database for MySQL](../../concepts-connectivity-architecture.md#azure-database-for-mysql-gateway-ip-addresses) verfügbar.

## <a name="ssltls-connectivity"></a>SSL/TLS-Konnektivität

Zusätzlich zu den Auswirkungen der Migration zur SSL-basierten Kommunikation auf Anwendungen müssen auch die SSL/TLS-Verbindungstypen berücksichtigt werden. Überprüfen Sie nach der Erstellung der Azure Database for MySQL-Datenbank die SSL-Einstellungen. Informationen zu den möglichen Auswirkung von TLS-Einstellungen auf den Sicherheitsstatus finden Sie im Artikel [SSL/TLS-Konnektivität in Azure Database for MySQL](../../concepts-ssl-connection-security.md).

> [!Important]
> Beachten Sie den Haftungsausschluss auf der Seite. Die Erzwingung der TLS-Version ist standardmäßig nicht aktiviert. Sobald TLS aktiviert ist, besteht die einzige Möglichkeit zum Deaktivieren darin, SSL erneut zu aktivieren.

## <a name="wwi-scenario"></a>WWI-Szenario

Das Cloudteam von WWI hat die erforderlichen Azure-Zielzonenressourcen in einer bestimmten Ressourcengruppe für die Azure Database for MySQL erstellt. Um die Zielzone zu erstellen, hat sich WWI dazu entschieden, mithilfe von ARM-Vorlagen ein Skript für das Setup und die Bereitstellung zu erstellen. Durch die Verwendung von ARM-Vorlagen kann das Unternehmen die Umgebung bei Bedarf schnell löschen und wieder neu erstellen.

Als Teil der ARM-Vorlage werden alle Verbindungen zwischen virtuellen Netzwerken in einer Hub-and-Spoke-Architektur mit Peering konfiguriert. Die Datenbank und die Anwendung werden in separaten virtuellen Netzwerken platziert. Ein Azure App Gateway wird vor dem App Service platziert, damit der App-Dienst vom Internet isoliert werden kann. Azure App Service stellt über einen privaten Endpunkt eine Verbindung mit der Azure Database for MySQL her.

WWI wollte ursprünglich eine Onlinemigration testen. Dies war aber wegen der erforderlichen Netzwerkeinrichtung für DMS zum Herstellen einer Verbindung mit der lokalen Umgebung nicht möglich. Also entschied sich WWI stattdessen für eine Offlinemigration. Das MySQL Workbench-Tool wurde zum Exportieren der lokalen Daten und dann zum Importieren der Daten in die Azure Database for MySQL-Instanz verwendet.

## <a name="planning-checklist"></a>Prüfliste für die Planung

- Bereiten Sie die Azure-Zielzone vor. Ziehen Sie die Bereitstellung einer ARM-Vorlage in Erwägung, für den Fall, dass die Umgebung schnell gelöscht und wieder neu erstellt werden muss.

- Überprüfen Sie das Netzwerk-Setup. Die Überprüfung sollte Folgendes umfassen: Konnektivität, Bandbreite, Latenz und Firewallkonfigurationen.

- Legen Sie fest, ob Sie eine Online- oder Offline-Datenmigrationsstrategie verwenden möchten.

- Entscheiden Sie sich für eine SSL-Zertifikatstrategie.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Migrationsmethoden](./05-migration-methods.md)
