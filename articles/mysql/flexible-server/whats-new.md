---
title: Neuerungen in Azure Database for MySQL Flexible Server
description: Hier erfahren Sie mehr zu Azure Database for MySQL Flexible Server, einem relationalen Datenbankdienst in der Microsoft-Cloud, der auf MySQL Community Edition basiert.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 08/17/2021
ms.openlocfilehash: 231622c5333875d1051bbdd2c17951c994b6f4d0
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351180"
---
# <a name="whats-new-in-azure-database-for-mysql---flexible-server-preview"></a>Neuerungen in Azure Database for MySQL Flexible Server (Vorschauversion)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

[Azure Database for MySQL Flexible Server](./overview.md#azure-database-for-mysql---flexible-server-preview) ist ein Bereitstellungsmodus, der mehr Kontrolle und Flexibilität bei der Datenbankverwaltung und den Konfigurationseinstellungen als der Single Server-Bereitstellungsmodus bietet. Der Dienst unterstützt derzeit die Community-Version von MySQL 5.7 und 8.0.

In diesem Artikel werden neue Releases und Features in Azure Database for MySQL Flexible Server ab Januar 2021 zusammengefasst. Auflistungen werden in umgekehrter chronologischer Reihenfolge angezeigt, die neuesten Updates zuerst.

## <a name="august-2021"></a>August 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Hochverfügbarkeit innerhalb einer Zone mithilfe von Hochverfügbarkeit in gleicher Zone**

  Mit diesem Dienst können Kund*innen jetzt flexibel ihre bevorzugte Verfügbarkeitszone für den Standbyserver auswählen, wenn sie Hochverfügbarkeit aktivieren. Zudem ermöglicht dieses Feature es Kund*innen, einen Standbyserver in derselben Zone wie der primäre Server bereitzustellen, wodurch die Replikationsverzögerung zwischen dem primären Server und dem Standbyserver reduziert wird. Dadurch entstehen auch niedrigere Latenzen zwischen dem Anwendungsserver und dem Datenbankserver, wenn diese in derselben Azure-Zone bereitgestellt werden. [Weitere Informationen](https://aka.ms/SameZone-HA)

- **Auswahl der Standbyzone mit zonenredundanter Hochverfügbarkeit**

  Der Dienst bietet Kund*innen jetzt die Möglichkeit, den Standort der Standbyserverzone zu wählen. Mit diesem Feature können Kund*innen ihren Standbyserver in der Zone ihrer Wahl bereitstellen. Durch das Bereitstellen der Standby-Datenbankserver und Standbyanwendungen in derselben Zone werden Latenzen reduziert, und Kund*innen können sich besser auf Notfallwiederherstellungen und Zonenausfälle vorbereiten. [Weitere Informationen](https://aka.ms/standby-selection)

- **Integration in private DNS-Zonen**

  [Privates DNS von Azure](../../dns/private-dns-privatednszone.md) ist ein zuverlässiger und sicherer DNS-Dienst für Ihre virtuellen Netzwerke, der Dienstnamen in IP-Adressen übersetzt. Privates Azure-DNS verwaltet Domänennamen im virtuellen Netzwerk und löst diese auf, ohne dass eine benutzerdefinierte DNS-Lösung konfiguriert werden muss. Dadurch können Sie Ihre in einem virtuellen Netzwerk ausgeführte Anwendung mit Ihrem flexiblen Server verbinden, der in einem lokal oder global mit Peering verbundenen virtuellen Netzwerk ausgeführt wird. Azure Database for MySQL Flexible Server ermöglicht jetzt die Integration in eine private Azure-DNS-Zone, um eine nahtlose Auflösung von privatem DNS innerhalb des aktuellen VNet oder eines beliebigen VNet mit Peering zu ermöglichen, mit dem die private DNS-Zone verknüpft ist. Wenn sich bei dieser Integration die IP-Adresse des flexiblen Back-End-Servers während eines Failovers oder eines anderen Ereignisses ändert, wird Ihre integrierte private DNS-Zone automatisch aktualisiert, damit die Anwendungskonnektivität automatisch wiederhergestellt wird, sobald der Server online ist. [Weitere Informationen](./concepts-networking-vnet.md)

- **Zeitpunktwiederherstellung für einen Server in einem angegebenen virtuellen Netzwerk**

  Da die Zeitpunktwiederherstellung im Dienst nun möglich ist, können Kund*innen Netzwerkeinstellungen konfigurieren, damit Benutzer*innen bei einem Wiederherstellungsvorgang zwischen privaten und öffentlichen Netzwerkoptionen wechseln können. Dank diesem Feature können Kund*innen flexibel einen Server, der wiederhergestellt wird, in ein angegebenes virtuelles Netzwerk injizieren, um ihre Verbindungsendpunkte zu schützen. [Weitere Informationen](./how-to-restore-server-portal.md)

- **Zeitpunktwiederherstellung für einen Server in einer Verfügbarkeitszone**

  Dank der Zeitpunktwiederherstellung im Dienst können Kund*innen jetzt Verfügbarkeitszonen konfigurieren und dabei die Datenbankserver und die Standbyanwendungen in derselben Zone bereitstellen, um die Latenz zu reduzieren. Dadurch können Kund*innen sich besser auf Notfallwiederherstellungen und Zonenausfälle vorbereiten. [Weitere Informationen](https://aka.ms/standby-selection)

- **Verfügbarkeit in vier zusätzlichen Azure-Regionen**

  Die Public Preview von Azure Database for MySQL Flexible Server ist jetzt in den folgenden Azure-Regionen verfügbar. [Weitere Informationen:](overview.md#azure-regions)

  - Australien, Südosten
  - Südafrika, Norden
  - Asien, Osten (Hongkong)
  - Indien, Mitte

- **Bekanntes Problem**

  - Direkt nach dem Failover des zonenredundanten Hochverfügbarkeitsservers kann der Client keine Verbindung mit dem Server herstellen, wenn SSL mit der auf VERIFY_IDENTITY festgelegten Eigenschaft ssl_mode verwendet wird. Dieses Problem kann umgangen werden, indem ssl_mode auf VERIFY_CA festgelegt wird.
  - In den folgenden Regionen kann kein Server mit Hochverfügbarkeit in gleicher Zone erstellt werden: „Indien, Mitte“, „Asien, Osten“, „Südkorea, Mitte“, „Südafrika, Norden“ und „Schweiz, Norden“.
  - Nach einem Hochverfügbarkeitsfailover kann es selten vorkommen, dass der primäre Server den Modus read_only annimmt. Sie können dieses Problem beheben, indem Sie den Wert read_only auf dem Blatt für Serverparameter in OFF ändern.
  - Nachdem Sie die Rechenleistung auf dem Blatt „Compute und Speicher“ erfolgreich skaliert haben, werden die IOPS auf den SKU-Standardwert zurückgesetzt. Kund*innen können das Problem umgehen, indem sie die IOPS auf dem Blatt „Compute und Speicher“ nach der Computebereitstellung und der konsequenten IOPS-Zurücksetzung auf den (zuvor festgelegten) gewünschten Wert neu skalieren.

## <a name="july-2021"></a>Juli 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Onlinemigration von Single Server zu Flexible Server**

  Kund*innen können jetzt mithilfe der Datenreplikation eine Instanz von Azure Database for MySQL Single Server mit minimaler Downtime ihrer Anwendungen zu Flexible Server migrieren. Eine ausführliche Anleitung finden Sie unter [Migrieren von Azure Database for MySQL Single Server zu Azure Database for MySQL Flexible Server mit minimaler Downtime](../howto-migrate-single-flexible-minimum-downtime.md).

- **Verfügbarkeit in „USA, Westen“ und „Deutschland, Westen-Mitte“**

  Die Public Preview von Azure Database for MySQL Flexible Server ist jetzt in den Azure-Regionen „USA, Westen“ und „Deutschland, Westen-Mitte“ verfügbar.

## <a name="june-2021"></a>Juni 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Verbesserte Leistung auf kleineren Speicherservern**

    Ab dem 21. Juni 2021 erhöht sich die zulässige Mindestspeichergröße für alle neu erstellten Server von 5 GB auf 20 GB. Darüber hinaus werden die kostenlosen IOPS von 100 auf 300 erhöht. Eine Zusammenfassung dieser Änderungen finden Sie in der folgenden Tabelle:

    | **Current** | **Ab 21. Juni 2021** |
    |:----------|:----------|
    | Mindestspeichergröße: 5 GB | Mindestspeichergröße: 20 GB |
    | Verfügbare IOPS: Max(100, 3 × [bereitgestellter Speicher in GB]) | Verfügbare IOPS: (300 + 3 × [bereitgestellter Speicher in GB]) |

- **Kostenloses Angebot für 12 Monate**

  Ab dem 15. Juni 2021 haben Kund*innen mit [kostenlosem Azure-Konto](https://azure.microsoft.com/free/) zwölf Monate lang kostenlosen Zugriff auf Azure Database for MySQL Flexible Server. Darin sind monatlich 750 Nutzungsstunden und 32 GB Speicher enthalten. Kund*innen können dieses Angebot zum Entwickeln und Bereitstellen von Anwendungen nutzen, die Azure Database for MySQL Flexible Server verwenden. [Weitere Informationen](https://go.microsoft.com/fwlink/?linkid=2165892)

- **Automatische Speichervergrößerung**

  Die automatische Speichervergrößerung verhindert, dass ein Server nicht mehr über genügend Speicherplatz verfügt und schreibgeschützt wird. Wenn die automatische Speichervergrößerung aktiviert ist, wird der Speicher automatisch ohne Beeinträchtigung der Workload vergrößert. Ab dem 21. Juni 2021 ist für alle neu erstellten Server die automatische Speichervergrößerung standardmäßig aktiviert. [Weitere Informationen](concepts-compute-storage.md#storage-auto-grow)

- **Datenreplikation**

    Flexible Server unterstützt jetzt die [Datenreplikation](concepts-data-in-replication.md). Mit diesem Feature können Sie Daten von einem MySQL-Server synchronisieren und zu Azure Database for MySQL Flexible Server migrieren. Der MySQL-Server kann lokal, auf VMs, in Azure Database for MySQL Single Server oder auf Datenbankdiensten außerhalb von Azure ausgeführt werden. Hier finden Sie weitere Informationen zum [Konfigurieren der Datenreplikation](how-to-data-in-replication.md).

- **GitHub Actions-Unterstützung in der Azure CLI**

  Mit der Flexible Server-CLI können Kund*innen jetzt Workflows automatisieren, um Updates mithilfe von GitHub Actions bereitzustellen. Mit diesem Feature können Sie Datenbankupdates mit GitHub Actions-Workflows für MySQL einrichten und bereitstellen. Mithilfe dieser CLI-Befehle können Sie ein Repository mit Continuous Deployment einrichten, um die Bereitstellung zu vereinfachen. [Weitere Informationen](/cli/azure/mysql/flexible-server/deploy?view=azure-cli-latest&preserve-view=true)

- **Fixes für erzwungenes Failover für zonenredundante Hochverfügbarkeit**

  Dieses Release enthält Fixes für bekannte Probleme mit erzwungenem Failover, damit Serverparameter und weitere IOPS-Änderungen bei Failovern beibehalten werden.

- **Bekanntes Problem**

  - Beim Versuch, eine Computeressource auf einem vorhandenen Server mit weniger als 20 GB bereitgestelltem Speicher hoch- oder herunterzuskalieren, tritt ein Fehler auf. Sie können das Problem beheben, indem Sie den bereitgestellten Speicher auf 20 GB hochskalieren und den Skalierungsvorgang für die Computeressource wiederholen.

## <a name="may-2021"></a>Mai 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Erweiterte regionale Verfügbarkeit („Frankreich, Mitte“, „Brasilien, Süden“ und „Schweiz, Norden“)**

    Die Public Preview von Azure Database for MySQL Flexible Server ist jetzt in den Regionen „Frankreich, Mitte“, „Brasilien, Süden“ und „Schweiz, Norden“ verfügbar. [Weitere Informationen](overview.md#azure-regions)

- **Erzwingung von SSL/TLS 1.2 jetzt deaktivierbar**

   Mit diesem Release können Kund*innen die Erzwingung von SSL und einer Mindestversion von TLS flexibler anpassen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Database for MySQL Flexible Server mit verschlüsselten Verbindungen](how-to-connect-tls-ssl.md).

- **Zonenredundante Hochverfügbarkeit in „Vereinigtes Königreich, Süden“ und „Japan, Osten“ verfügbar**

   Azure Database for MySQL Flexible Server bietet jetzt zonenredundante Hochverfügbarkeit in zwei weiteren Regionen: „Vereinigtes Königreich, Süden“ und „Japan, Osten“. [Weitere Informationen](overview.md#azure-regions)

- **Bekannte Probleme**

  - Auf Servern mit aktivierter zonenredundanter Hochverfügbarkeit sind die zusätzlichen IOPS-Änderungen nicht wirksam. Kund*innen können dieses Problem umgehen, indem sie die Hochverfügbarkeit deaktivieren, die IOPS skalieren und die zonenredundante Hochverfügbarkeit dann wieder aktivieren.
  - Nach einem erzwungenen Failover wird die Standbyverfügbarkeitszone im Portal ungenau wiedergegeben. (Keine Problemumgehung)
  - Änderungen an Serverparametern sind auf Servern mit aktivierter zonenredundanter Hochverfügbarkeit nach einem erzwungenen Failover nicht wirksam. (Keine Problemumgehung)

## <a name="april-2021"></a>April 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Erzwungenes Failover auf Standbyserver mit zonenredundanter Hochverfügbarkeit**

  Kund*innen können jetzt manuell ein Failover erzwingen, um Funktionalitäten in ihren Anwendungsszenarios zu testen. Dadurch können sie sich besser auf Ausfälle vorbereiten. [Weitere Informationen](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/forced-failover-for-azure-database-for-mysql-flexible-server/ba-p/2280671)

- **PowerShell-Modul für Flexible Server veröffentlicht**

  Entwickler*innen können jetzt PowerShell für die Bereitstellung, Verwaltung, den Betrieb und die Unterstützung von MySQL Flexible Server und abhängigen Ressourcen verwenden. [Weitere Informationen](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-the-mysql-flexible-server-powershell-module/ba-p/2203383)

- **Verbinden, Testen und Ausführen von Abfragen mit der Azure CLI**

  Azure Database for MySQL Flexible Server enthält jetzt verbesserte Entwicklerfeatures, sodass Kund*innen Verbindungen herstellen und Abfragen auf ihren Servern ausführen können, indem sie die Befehle az mysql flexible-server connect und az mysql flexible-server execute in der Azure CLI verwenden. [Weitere Informationen](connect-azure-cli.md#view-all-the-arguments)

- **Fixes für Bereitstellungsfehler bei der Servererstellung in virtuellen Netzwerken mit privatem Zugriff**

  Alle Bereitstellungsfehler, die beim Erstellen von Server in virtuellen Netzwerken aufgetreten sind, wurden behoben. Mit diesem Release können Benutzer*innen stets erfolgreich flexible Server mit privatem Zugriff erstellen.  

## <a name="march-2021"></a>März 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **MySQL 8.0.21 veröffentlicht**

  MySQL 8.0.21 ist jetzt in Flexible Server in allen wichtigen [Azure-Regionen](overview.md#azure-regions) verfügbar. Kund*innen können das Azure-Portal, die Azure CLI oder Azure Resource Manager-Vorlagen verwenden, um das MySQL 8.0.21-Release bereitstellen. [Weitere Informationen](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server)

- **Unterstützung für die Bereitstellung von Verfügbarkeitszonen bei der Servererstellung**

  Kund*innen können jetzt ihre bevorzugte Verfügbarkeitszone zum Zeitpunkt der Servererstellung angeben. Mit diesem Feature können Kund*innen ihre Anwendungen, die auf einer Azure-VM, in einer VM-Skalierungsgruppe, in AKS oder in einer Datenbank gehostet werden, in den gleichen Verfügbarkeitszonen bereitstellen, um die Datenbanklatenz zu minimieren und die Leistung zu verbessern. [Weitere Informationen](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server)

- **Leistungsfixes für Ausführungsprobleme mit flexiblen Servern in virtuellen Netzwerken mit privatem Zugriff**

  Vor diesem Release hat sich die Leistung von Flexible Server bei der Ausführung in der Konfiguration des virtuellen Netzwerks erheblich verschlechtert. Dieses Release enthält Fixes für das Problem, die die Leistung auf flexiblen Servern in virtuellen Netzwerken verbessern.

- **Bekannte Probleme**

  - SSL/TLS 1.2 wird erzwungen und kann nicht deaktiviert werden. (Keine Problemumgehungen)
  - Es treten zeitweilige Bereitstellungsfehler bei Servern auf, die in einem VNet bereitgestellt werden. Die Problemumgehung besteht im Wiederholen der Serverbereitstellung, bis sie erfolgreich ist.

## <a name="february-2021"></a>Februar 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Zusätzliches IOPS-Feature veröffentlicht**

  Azure Database for MySQL Flexible Server unterstützt die Bereitstellung zusätzlicher [IOPS](concepts-compute-storage.md#iops) unabhängig vom bereitgestellten Speicher. Kund*innen können dieses Feature verwenden, um die IOPS-Menge basierend auf ihren Workloadanforderungen jederzeit zu erhöhen oder zu verringern.

- **Bekannte Probleme**

  Die Leistung von Azure Database for MySQL Flexible Server nimmt bei isolierten virtuellen Netzwerken mit privatem Zugriff ab. (Keine Problemumgehung)

## <a name="january-2021"></a>Januar 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Bis zu zehn Lesereplikate für MySQL Flexible Server**

  Flexible Server unterstützt jetzt die asynchrone Replikation von Daten von einem Azure Database for MySQL-Server (Quelle) zu bis zu zehn weiteren Azure Database for MySQL-Servern (Replikate) in derselben Region. Durch dieses Feature können leseintensive Workloads je nach Benutzereinstellungen aufskaliert und gleichmäßig auf Replikatserver verteilt werden. [Weitere Informationen](concepts-read-replicas.md)

## <a name="contacts"></a>Kontakte

Wenn Sie Fragen oder Vorschläge zur Arbeit mit Azure Database for MySQL haben, können Sie sich an die folgenden Stellen wenden:

- Wenn Sie den Azure-Support kontaktieren möchten, [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den [Preisen für Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/server/).
- Suchen Sie in der [öffentlichen Dokumentation](index.yml) nach Azure Database for MySQL Flexible Server.
- Lesen Sie Details zur [Problembehandlung für häufige Migrationsfehler](../howto-troubleshoot-common-errors.md).
