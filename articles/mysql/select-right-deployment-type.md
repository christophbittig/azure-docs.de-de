---
title: Auswählen des richtigen Bereitstellungstyps – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, welche Faktoren vor der Bereitstellung von Azure Database for MySQL als IaaS (Infrastructure-as-a-Service) oder PaaS (Platform-as-a-Service) zu berücksichtigen sind.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: f07cb6dc6e5ab01b4c172c660fa971b1ef2a3db9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286088"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Auswählen der richtigen MySQL Server-Option in Azure

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Mit Azure können Ihre MySQL-Serverworkloads auf einer gehosteten IaaS-VM (Infrastructure-as-a-Service) oder als gehostete PaaS-Instanz (Platform-as-a-Service) ausgeführt werden. PaaS stellt zwei Bereitstellungsoptionen und Dienstebenen für jede Bereitstellungsoption zur Verfügung. Bei der Auswahl zwischen IaaS und PaaS müssen Sie entscheiden, ob Sie Ihre Datenbank verwalten, Patches, Sicherungen, Sicherungsüberwachung, Skalierung anwenden oder diese Vorgänge an Azure delegieren möchten.

Berücksichtigen Sie bei der Entscheidung die beiden folgenden Optionen:

- **Azure Database for MySQL**: Diese Option ist eine vollständig verwaltete MySQL-Datenbank-Engine, die auf der stabilen Version der MySQL Community Edition basiert. Diese relationale DBaaS-Lösung (Database-as-a-Service), die auf der Azure-Cloudplattform gehostet wird, fällt in die Branchenkategorie PaaS. Mit einer verwalteten Instanz von MySQL in Azure können Sie die integrierten Features, d. h. automatisiertes Patchen, Hochverfügbarkeit, automatisierte Sicherungen, elastische Skalierung, Sicherheit auf Unternehmensniveau, Compliance und Governance, Überwachung und Warnungen, nutzen, für die ansonsten eine umfassende Konfiguration erforderlich ist, wenn MySQL Server lokal oder auf einem virtuellen Azure-Computer ausgeführt wird. Wenn Sie mit MySQL als Dienst arbeiten, bezahlen Sie nur für das, was Sie nutzen. Darüber hinaus haben Sie dank Optionen zum Hoch- und Aufskalieren ohne Unterbrechung mehr Kontrolle. [Azure Database for MySQL](overview.md), ein Dienst, der auf MySQL Community Edition basiert, ist in zwei Bereitstellungsmodi verfügbar:

   - [Flexible Server](flexible-server/overview.md): Azure Database for MySQL Flexible Server ist ein vollständig verwalteter produktionsbereiter Datenbankdienst, der eine differenziertere Steuerung und mehr Flexibilität bei den Verwaltungsfunktionen und Konfigurationseinstellungen der Datenbank ermöglichen soll. Mit der Flexible Server-Architektur können Benutzer Hochverfügbarkeit in einer einzelnen Verfügbarkeitszone sowie in mehreren Verfügbarkeitszonen auswählen. Flexible Server-Implementierungen bieten eine bessere Kostenoptimierung mit der Möglichkeit, den Server anzuhalten/zu starten, sowie einen burstfähigen Computetarif, der sich ideal für Workloads eignet, die nicht dauerhaft die volle Computekapazität benötigen. Flexible Server unterstützt außerdem reservierte Instanzen, sodass Sie bei den Kosten bis zu 63 Prozent sparen können. Dies ist ideal für Produktionsworkloads mit vorhersagbaren Computekapazitätsanforderungen. Der Dienst unterstützt die MySQL Community-Versionen 5.7 und 8.0. Der Dienst ist ab heute in vielen [Azure-Regionen](flexible-server/overview.md#azure-regions) allgemein verfügbar. Flexible Server eignen sich am besten für alle neuen Entwicklungen und die Migration von Produktionsworkloads zum Azure Database for MySQL-Dienst.

   - [Single Server](single-server-overview.md) ist ein vollständig verwalteter Datenbankdienst, der auf minimale Anpassungen ausgelegt ist. Die Single Server-Plattform kann die meisten Funktionen zur Datenbankverwaltung bereitstellen, z. B. Patching, Sicherungen, Hochverfügbarkeit sowie Sicherheit mit minimaler Benutzerkonfiguration und -steuerung. Die Architektur ist für integrierte Hochverfügbarkeit mit einer Verfügbarkeit von 99,99 % bei einer einzelnen Verfügbarkeitszone optimiert. Sie unterstützt die MySQL Community-Versionen 5.6 (eingestellt), 5.7 und 8.0. Der Dienst ist ab heute in vielen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/) allgemein verfügbar. Single Server-Instanzen eignen sich am besten **nur für vorhandene Anwendungen, die bereits Einzelserver nutzen**. Für alle neuen Entwicklungen oder Migrationen ist Flexible Server die empfohlene Bereitstellungsoption. Informationen zu den Unterschieden zwischen den Bereitstellungsoptionen für Flexible Server und Single Server finden Sie unter [Auswählen der richtigen MySQL Server-Option in Azure](select-right-deployment-type.md).
 
- **MySQL auf Azure-VMs**: Diese Option fällt in die Branchenkategorie IaaS. Mit diesem Dienst können Sie MySQL Server auf einem verwalteten virtuellen Computer auf der Azure-Cloudplattform ausführen. Alle aktuellen Versionen und Editionen von MySQL können auf diesem virtuellen Computer installiert werden.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Vergleich der MySQL-Bereitstellungsoptionen in Azure

Die Hauptunterschiede zwischen diesen Optionen werden in der folgenden Tabelle aufgeführt:

| attribute          | Azure Database for MySQL<br/>Einzelner Server |Azure Database for MySQL<br/>Flexible Server  |MySQL auf Azure-VMs |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:------------------|
| [**Allgemein**](flexible-server/overview.md)  | | | |
| Allgemeine Verfügbarkeit | Allgemein verfügbar | Allgemein verfügbar | Allgemein verfügbar |
| Vereinbarung zum Servicelevel (SLA) | SLA mit 99,99 %Verfügbarkeit |99,99 % bei Verwendung von Verfügbarkeitszonen| 99,99 % bei Verwendung von Verfügbarkeitszonen|
| Zugrunde liegendes Betriebssystem | Windows | Linux  | Vom Benutzer verwaltet |
| MySQL-Edition | Community Edition | Community Edition | Community oder Enterprise Edition |
| Unterstützte MySQL-Versionen | 5.6 (eingestellt), 5.7 und 8.0| 5.7 und 8.0 | Beliebige Version|
| Auswahl der Verfügbarkeitszone für die Anwendungscolocation | Nein | Ja | Ja |
| Benutzername in Verbindungszeichenfolge | `<user_name>@server_name`. Beispiel: `mysqlusr@mypgServer` | Nur Benutzername. Beispiel: `mysqlusr` | Nur Benutzername. Beispiel: `mysqlusr` | 
| [**Compute- und Speicherskalierung**](flexible-server/concepts-compute-storage.md) | | | |
| Computetarife | Basic, Universell, Arbeitsspeicheroptimiert | Burstfähig, Universell, Arbeitsspeicheroptimiert | Burstfähig, Universell, Arbeitsspeicheroptimiert |
| Compute-Skalierung | Unterstützt (die Skalierung vom und zum Tarif Basic wird **nicht unterstützt**)| Unterstützt | Unterstützt|
| Speichergröße | 5 GiB bis 16 TiB| 20 GiB bis 16 TiB | 32 GiB bis 32.767 GiB|
| Onlinespeicherskalierung | Unterstützt| Unterstützt| Nicht unterstützt|
| Automatische Speicherskalierung | Unterstützt| Unterstützt| Nicht unterstützt|
| IOPs-Skalierung | Nicht unterstützt| Unterstützt| Nicht unterstützt|
| [**Kostenoptimierung**](https://azure.microsoft.com/pricing/details/mysql/flexible-server/) | | | |
| Reservierte Azure-VM-Instanzen (RIs): Preise | Unterstützt | Unterstützt | Unterstützt |
| Beenden/Starten des Servers für die Entwicklung | Server kann bis zu 7 Tage angehalten werden | Server kann bis zu 30 Tage angehalten werden | Unterstützt |
| Kostengünstige burstfähige SKU | Nicht unterstützt | Unterstützt | Unterstützt |
| [**Netzwerk/Sicherheit**](concepts-security.md) | | | |
| Netzwerkverbindung | – Öffentliche Endpunkte mit Serverfirewall<br/> – Privater Zugriff mit Private Link-Unterstützung|– Öffentliche Endpunkte mit Serverfirewall<br/> – Privater Zugriff mit Virtual Network-Integration.| – Öffentliche Endpunkte mit Serverfirewall<br/> – Privater Zugriff mit Private Link-Unterstützung|
| SSL/TLS | Standardmäßig aktiviert mit Unterstützung für TLS 1.2, 1.1 und 1.0 | Standardmäßig aktiviert mit Unterstützung für TLS 1.2, 1.1 und 1.0| Unterstützung für TLS 1.2, 1.1 und 1.0 |
| Verschlüsselung ruhender Daten | Unterstützt mit kundenseitig verwalteten Schlüsseln (BYOK) | Unterstützt mit dienstseitig verwalteten Schlüsseln | Nicht unterstützt|
| Azure AD-Authentifizierung | Unterstützt | Nicht unterstützt | Nicht unterstützt|
| Support bei Microsoft Defender für Cloud | Ja | Nein | Nein |
| Serverüberwachung | Unterstützt | Unterstützt | Vom Benutzer verwaltet |
| [**Patching und Wartung**](flexible-server/concepts-maintenance.md) | | |
| Betriebssystempatching| Automatic  | Automatisch  | Vom Benutzer verwaltet |
| Upgrades von MySQL-Nebenversionen  | Automatisch  | Automatisch | Vom Benutzer verwaltet |
| Direkte Upgrades der MySQL-Hauptversion | Unterstützt von 5.6 bis 5.7 | Nicht unterstützt | Vom Benutzer verwaltet |
| Wartungssteuerung | Systemverwaltet | Vom Kunden verwaltet | Vom Benutzer verwaltet |
| Wartungsfenster | Jederzeit innerhalb eines 15-Stunden-Zeitfensters | Zeitfenster von 1 Stunde | Vom Benutzer verwaltet |
| Benachrichtigungen zu geplanten Wartungen | 3 Tage | 5 Tage | Vom Benutzer verwaltet |
| [**Hochverfügbarkeit**](flexible-server/concepts-high-availability.md) | | | |
| Hochverfügbarkeit | Integrierte Hochverfügbarkeit (ohne Hot Standby)| Integrierte Hochverfügbarkeit (ohne Hot Standby), Hochverfügbarkeit in derselben Zone und zonenredundante HA mit Hot Standby | Vom Benutzer verwaltet |
| Zonenredundanz | Nicht unterstützt | Unterstützt | Unterstützt|
| Platzierung der Standbyzone | Nicht unterstützt | Unterstützt | Unterstützt|
| Automatisches Failover | Ja (Wechsel zu anderem Server)| Ja | Vom Benutzer verwaltet|
| Vom Benutzer initiiertes erzwungenes Failover | Nein | Ja | Vom Benutzer verwaltet |
| Transparentes Anwendungsfailover | Ja | Ja | Vom Benutzer verwaltet|
| [**Replikation**](flexible-server/concepts-read-replicas.md) | | | |
| Unterstützung für Lesereplikate | Ja | Ja | Vom Benutzer verwaltet |
| Anzahl unterstützter Lesereplikate | 5 | 10 | Vom Benutzer verwaltet |
| Modus der Replikation | Asynchron | Asynchron | Vom Benutzer verwaltet |
| GTID-Unterstützung für Lesereplikate | Unterstützt | Unterstützt | Vom Benutzer verwaltet |
| Regionsübergreifende Unterstützung (Georeplikation) | Ja | Nicht unterstützt | Vom Benutzer verwaltet |
| Hybridszenario | Unterstützt mit [Datenreplikation](./concepts-data-in-replication.md)| Unterstützt mit [Datenreplikation](./flexible-server/concepts-data-in-replication.md) | Vom Benutzer verwaltet |
| GTID-Unterstützung für die Datenreplikation | Unterstützt | Unterstützt | Vom Benutzer verwaltet |
| Replikation ausgehender Daten | Nicht unterstützt | In der Vorschau | Unterstützt |
| [**Sicherung und Wiederherstellung**](flexible-server/concepts-backup-restore.md) | | | |
| Automatisierte Sicherungen | Ja | Ja | Nein |
| Sicherungsaufbewahrung | 7–35 Tage | 1–35 Tage | Vom Benutzer verwaltet |
| Langzeitaufbewahrung von Sicherungen | Vom Benutzer verwaltet | Vom Benutzer verwaltet | Vom Benutzer verwaltet |
| Exportieren von Sicherungskopien | Unterstützt für logische Sicherungen | Unterstützt für logische Sicherungen | Unterstützt |
| Funktion für die Point-in-Time-Wiederherstellung zu jedem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums | Ja | Ja | Vom Benutzer verwaltet |
| Schneller Wiederherstellungspunkt | Nein | Ja | Nein |
| Möglichkeit zum Wiederherstellen in einer anderen Zone | Nicht unterstützt | Ja | Ja |
| Möglichkeit zum Wiederherstellen in einem anderen VNet | Nein | Ja | Ja |
| Möglichkeit zum Wiederherstellen in einer anderen Region | Ja (georedundant) | Nein | Vom Benutzer verwaltet |
| Möglichkeit zum Wiederherstellen eines gelöschten Servers | Ja | Nein | Nein |
| [**Notfallwiederherstellung**](flexible-server/concepts-business-continuity.md) (Disaster Recovery, DR) | | | | 
| DR in allen Azure-Regionen | Mit regionsübergreifenden Lesereplikaten, georedundante Sicherungen | Nicht unterstützt | Vom Benutzer verwaltet |
| Automatisches Failover | Nein | Nicht unterstützt | Nein |
| Gleicher Lese-/Schreib-Endpunkt verwendbar | Nein | Nicht unterstützt | Nein |
| [**Überwachung**](flexible-server/concepts-monitoring.md) | | | |
| Azure Monitor-Integration und -Warnungen | Unterstützt | Unterstützt | Vom Benutzer verwaltet |
| Überwachen von Datenbankvorgängen | Unterstützt | Unterstützt | Vom Benutzer verwaltet |
| Statistik zur Abfrageleistung | Unterstützt | Unterstützt (mit Arbeitsmappen)| Vom Benutzer verwaltet |
| Serverprotokolle | Unterstützt | Unterstützt (mithilfe von Diagnoseprotokollen) | Vom Benutzer verwaltet |
| Überwachungsprotokolle | Unterstützt | Unterstützt | Unterstützt | 
| Fehlerprotokolle | Nicht unterstützt | Unterstützt | Unterstützt |
| Azure Advisor-Unterstützung | Unterstützt | Nicht unterstützt | Nicht unterstützt |
| **Plug-Ins** | | | |
| validate_password | Nicht unterstützt | In der Vorschau | Unterstützt |
| caching_sha2_password | Nicht unterstützt | In der Vorschau | Unterstützt |
| [**Entwicklerproduktivität**](flexible-server/quickstart-create-server-cli.md) | | | |
| Flottenverwaltung | Unterstützt mit der Azure CLI, PowerShell, REST und dem Azure Resource Manager | Unterstützt mit der Azure CLI, PowerShell, REST und Azure Resource Manager  | Unterstützt für VMs mit der Azure CLI, PowerShell, REST und Azure Resource Manager |
| Terraform-Unterstützung | Unterstützt | Unterstützt | Unterstützt |
| GitHub Actions | Unterstützt | Unterstützt | Vom Benutzer verwaltet |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Geschäftsmotive für die Entscheidung für PaaS oder IaaS

Es gibt verschiedene Faktoren, die Ihre Entscheidung zwischen PaaS und IaaS zum Hosten Ihrer MySQL-Datenbanken beeinflussen.

### <a name="cost"></a>Kosten

Die Einsparung von Kosten ist oft der wichtigste Aspekt, der darüber entscheidet, was die beste Lösung für das Hosten Ihrer Datenbanken ist. Dies gilt unabhängig davon, ob Sie ein Startup-Unternehmen mit wenig liquiden Mitteln oder ein Team in einem etablierten Unternehmen sind, das unter engen Budgetbeschränkungen arbeitet. In diesem Abschnitt werden die Grundlagen zur Abrechnung und Lizenzierung in Azure in Bezug auf Azure Database for MySQL und MySQL auf Azure-VMs beschrieben.

#### <a name="billing"></a>Abrechnung

Azure Database for MySQL ist derzeit als Dienst in verschiedenen Tarifen mit unterschiedlichen Preisen für Ressourcen verfügbar. Alle Ressourcen werden auf Stundenbasis mit einer festen Gebühr abgerechnet. Die neuesten Informationen zu aktuell unterstützten Dienstebenen, Computegrößen und Speichermengen finden Sie auf der [Preisübersichtsseite](https://azure.microsoft.com/pricing/details/mysql/). Sie können Dienstebenen und Computegrößen entsprechend den unterschiedlichen Durchsatzanforderungen Ihrer Anwendung dynamisch anpassen. Ihnen wird der ausgehende Internetdatenverkehr basierend auf den üblichen [Datenübertragungsraten](https://azure.microsoft.com/pricing/details/data-transfers/) berechnet.

Mit Azure Database for MySQL wird die Datenbanksoftware automatisch von Microsoft konfiguriert, gepatcht und aktualisiert. Diese automatisierten Aktionen senken Ihre Verwaltungskosten. Außerdem bietet Azure Database for MySQL [automatisierte Sicherungen](./concepts-backup.md). Mithilfe dieser Funktionen können Sie erhebliche Kosteneinsparungen erzielen, vor allem bei einer großen Anzahl von Datenbanken. Im Gegensatz dazu können Sie mit MySQL auf Azure-VMs eine beliebige MySQL-Version auswählen und ausführen. Unabhängig davon, welche MySQL-Version Sie verwenden, zahlen Sie für Folgendes: die bereitgestellte VM, die Speicherkosten für die Daten, den Speicher für die Sicherungen, die Überwachungsdaten und die Protokolle sowie die Kosten für den jeweils verwendeten MySQL-Lizenztyp (sofern vorhanden).

Azure Database for MySQL bietet integrierte Hochverfügbarkeit für jede Art von Unterbrechung auf Knotenebene und bietet gleichzeitig eine SLA-Garantie von 99,99 % für den Dienst. Für Hochverfügbarkeit von Datenbanken auf VMs sollten Sie jedoch Hochverfügbarkeitsoptionen wie die [MySQL-Replikation](https://dev.mysql.com/doc/refman/8.0/en/replication.html) verwenden, die für MySQL-Datenbanken zur Verfügung stehen. Durch Verwendung einer unterstützten Hochverfügbarkeitsoption erhalten Sie keine zusätzliche SLA. Allerdings können Sie mit zusätzlichen Kosten und höherem Verwaltungsaufwand eine Datenbankverfügbarkeit von mehr als 99,99 % erzielen.

Weitere Informationen zu Preisen finden Sie in den folgenden Artikeln:

- [Azure Database for MySQL: Preise](https://azure.microsoft.com/pricing/details/mysql/)
- [Preise für virtuelle Computer](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Verwaltung

Bei vielen Unternehmen geht es bei der Entscheidung für den Wechsel zu einem Clouddienst ebenso um eine Vereinfachung der Verwaltung wie um die Kosten.

Bei IaaS übernimmt Microsoft folgende Aufgaben:

- Verwaltet die zugrunde liegende Infrastruktur
- Automatisiertes Patchen der zugrunde liegenden Hardware und der Betriebssysteme

Bei PaaS übernimmt Microsoft folgende Aufgaben:

- Verwaltet die zugrunde liegende Infrastruktur
- Automatisiertes Patchen der zugrunde liegenden Hardware, der Betriebssysteme und des Datenbankmoduls
- Verwaltung der Hochverfügbarkeit der Datenbank
- Automatische Erstellung von Sicherungen und Replikation aller Daten, um eine Notfallwiederherstellung zu ermöglichen
- Verschlüsselung von ruhenden Daten und Daten in Bewegung als Standard
- Überwachung Ihres Servers und Bereitstellung von Features für Statistiken zur Abfrageleistung und Leistungsempfehlungen

In der folgenden Liste sind Verwaltungsaspekte der einzelnen Optionen beschrieben:

- Mit Azure Database for MySQL können Sie Ihre Datenbank weiterhin verwalten. Es ist aber keine Verwaltung von Datenbank-Engine, Betriebssystem oder Hardware mehr erforderlich. Hier sind einige Beispiele für Elemente angegeben, die Sie weiterhin verwalten können:

  - Datenbanken
  - Anmeldung
  - Indexoptimierung
  - Abfrageoptimierung
  - Überwachung
  - Sicherheit

  Darüber hinaus erfordert die Konfiguration der Hochverfügbarkeit für ein anderes Rechenzentrum nur noch einen minimalen bzw. gar keinen Konfigurations- oder Verwaltungsaufwand mehr.

- Mit MySQL auf virtuellen Azure-Computern haben Sie die uneingeschränkte Kontrolle über das Betriebssystem und die Konfiguration der MySQL-Serverinstanzen. Bei einer VM entscheiden Sie, wann ein Update oder Upgrade des Betriebssystems und der Datenbanksoftware durchgeführt werden soll und welche Patches angewendet werden sollen. Außerdem entscheiden Sie, wann zusätzliche Software, z. B. eine Antivirenanwendung, installiert werden soll. Durch einige bereitgestellte Automatisierungsfeatures lassen sich Patching, Sicherungen und Hochverfügbarkeit erheblich vereinfachen. Sie können die Größe der VM, die Anzahl von Datenträgern sowie deren Speicherkonfigurationen steuern. Weitere Informationen finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Zeit für einen Wechsel nach Azure

- Azure Database for MySQL ist die geeignete Lösung für cloudbasierte Anwendungen, wenn Entwicklerproduktivität und eine schnelle Markteinführung für neue Lösungen entscheidend sind. Aufgrund von programmgesteuerten Funktionen wie DBA ist der Dienst für Cloudarchitekten und Entwickler geeignet, da er den Verwaltungsaufwand für das zugrunde liegende Betriebssystem und die Datenbank verringert.

- Wenn Sie die Zeit und Kosten für den Erwerb neuer lokaler Hardware sparen möchten, ist MySQL auf Azure-VMs die richtige Lösung für Anwendungen, die eine differenzierte Steuerung und Anpassung der MySQL-Engine erfordern, die vom Dienst nicht unterstützt wird oder Zugriff auf das zugrunde liegende Betriebssystem erfordert. Diese Lösung eignet sich auch in Fällen, in denen Azure Database for MySQL nicht geeignet ist, für die Migration vorhandener lokaler Anwendungen und Datenbanken in unverändertem Zustand zu Azure.

Da keine Änderungen an der Darstellungs-, Anwendungs- und Datenschicht erforderlich sind, sparen Sie sich die Zeit und das Geld, die sonst für den Neuentwurf der vorhandenen Lösung nötig wären. Sie können sich stattdessen auf die Migration Ihrer gesamten Lösungen zu Azure konzentrieren und müssen lediglich einige Leistungsoptimierungen durchführen, die für die Azure-Plattform unter Umständen erforderlich sind.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Database for MySQL: Preise](https://azure.microsoft.com/pricing/details/MySQL/)
- [Erstellen Sie Ihren ersten Server.](./quickstart-create-mysql-server-database-using-azure-portal.md)
