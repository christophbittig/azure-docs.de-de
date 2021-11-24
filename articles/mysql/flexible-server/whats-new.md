---
title: Neuerungen in Azure Database for MySQL Flexible Server
description: Hier erfahren Sie mehr zu Azure Database for MySQL Flexible Server, einem relationalen Datenbankdienst in der Microsoft-Cloud, der auf MySQL Community Edition basiert.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc, references_regions
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 8333f0086a7d2778fcda1efec64ebca6de94a002
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398001"
---
# <a name="whats-new-in-azure-database-for-mysql---flexible-server-"></a>Neuerungen in Azure Database for MySQL Flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

[Azure Database for MySQL Flexible Server](./overview.md) ist ein Bereitstellungsmodus, der mehr Kontrolle und Flexibilität bei der Datenbankverwaltung und den Konfigurationseinstellungen als der Single Server-Bereitstellungsmodus bietet. Der Dienst unterstützt derzeit die Community-Version von MySQL 5.7 und 8.0.

In diesem Artikel werden neue Releases und Features in Azure Database for MySQL Flexible Server ab Januar 2021 zusammengefasst. Auflistungen werden in umgekehrter chronologischer Reihenfolge angezeigt, die neuesten Updates zuerst.

## <a name="november-2021"></a>November 2021
- **Allgemeine Verfügbarkeit von Azure Database for MySQL – Flexible Server**
    
  Azure Database for MySQL – Flexible Server ist jetzt in mehr als [30 Azure-Regionen](overview.md) weltweit **allgemein verfügbar**.

- **Anzeigen verfügbarer vollständiger Sicherungen im Azure-Portal** Ein dediziertes Blatt „Sicherung und Wiederherstellung“ ist jetzt im Azure-Portal verfügbar. Auf diesem Blatt werden die Sicherungen aufgelistet, die innerhalb des Aufbewahrungszeitraums des Servers verfügbar sind. Dadurch erhalten Sie effektiv eine Ansicht mit einem einzelnen Bereich zum Verwalten der Sicherungen und aufeinanderfolgenden Wiederherstellungen eines Servers. Dieses Blatt ermöglicht Folgendes: 
   1) Anzeigen der Abschlusszeitstempel für alle verfügbaren vollständigen Sicherungen innerhalb des Aufbewahrungszeitraums des Servers 
   2) Ausführen von Wiederherstellungsvorgängen mit diesen vollständigen Sicherungen
  
- **Schnellste Wiederherstellungspunkte**
  
  Mit der Option „Schnellster Wiederherstellungspunkt“ können Sie eine Flexible Server-Instanz an einem bestimmten Tag innerhalb des Aufbewahrungszeitraums des Servers so schnell wie möglich wiederherstellen. Bei diesem Wiederherstellungsvorgang wird einfach die vollständige Momentaufnahmesicherung wiederhergestellt, ohne dass eine Wiederherstellung von Protokollen erforderlich ist. Mit dem schnellsten Wiederherstellungspunkt werden den Kunden bei der Durchführung von Point-in-Time-Wiederherstellungen über das Azure-Portal 3 Optionen angezeigt, nämlich letzter Wiederherstellungspunkt, benutzerdefinierter Wiederherstellungspunkt und schnellster Wiederherstellungspunkt. [Weitere Informationen](concepts-backup-restore.md#point-in-time-restore)
  
- **Blatt mit häufig gestellten Fragen im Azure-Portal**

  Das Blatt „Sicherung und Wiederherstellung“ enthält auch den Abschnitt zum Auflisten der am häufigsten gestellten Fragen mit entsprechenden Antworten. Dadurch erhalten Sie Antworten auf die meisten Fragen zur Sicherung direkt im Azure-Portal. Darüber hinaus können Sie durch Auswählen des Fragezeichensymbols für die häufig gestellten Fragen im oberen Menü auf weitere verwandte Details zugreifen.

- **Wiederherstellen einer gelöschten Instanz von Flexibler Server**
    
  Der Dienst ermöglicht Ihnen nun, eine gelöschte flexible MySQL-Serverressource innerhalb von 5 Tagen ab dem Zeitpunkt der Löschung des Servers wiederherzustellen. Eine detaillierte Anleitung zur Wiederherstellung eines gelöschten Servers [finden Sie in den dokumentierten Schritten](../flexible-server/how-to-restore-dropped-server.md). Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, werden Administratoren [Verwaltungssperren](../../azure-resource-manager/management/lock-resources.md) empfohlen.

- **Bekannte Probleme**

Auf Servern mit aktivierter Hochverfügbarkeit und georedundanter Sicherungsoption wurde ein seltenes Problem aufgrund einer Racebedingung festgestellt, die den Neustart des Standbyservers so blockiert, dass er nicht abgeschlossen werden kann. Als Folge dieses Problems kann es passieren, dass beim Failover der für Hochverfügbarkeit aktivierten Azure Database for MySQL-Instanz die flexible MySQL-Serverinstanz lange Zeit im Zustand „Wird neu gestartet“ hängen bleibt. Die Korrektur wird im nächsten Bereitstellungszyklus in der Produktion bereitgestellt.

## <a name="october-2021"></a>Oktober 2021

- **Threadpools sind jetzt für Azure Database for MySQL – Flexible Server verfügbar**
 
    Threadpools verbessern die Skalierbarkeit von Azure Database for MySQL – Flexible Server. Mithilfe eines Threadpools können Benutzer jetzt die Leistung optimieren, einen besseren Durchsatz erzielen und die Latenz für hohe gleichzeitige Workloads verringern. [Weitere Informationen](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/achieve-up-to-a-50-performance-boost-in-azure-database-for-mysql/ba-p/2909691)

- **Georedundante Backup-Wiederherstellung in einer geopolitischen Region für Notfallwiederherstellungsszenarien**

    Der Dienst bietet jetzt die zusätzliche Flexibilität, georedundanten Sicherungsspeicher auszuwählen, um eine höhere Datenresilienz zu gewährleisten. Die Aktivierung von Georedundanz ermöglicht Kunden die Wiederherstellung nach einem geografischen Notfall oder regionalen Ausfall, wenn sie nicht auf den Server in der primären Region zugreifen können. Wenn dieses Feature aktiviert ist, können Kunden eine Geowiederherstellung durchführen und einen neuen Server in der geografisch gekoppelten geografischen Region bereitstellen, indem sie die neueste verfügbare georedundante Sicherung des ursprünglichen Servers nutzen. [Weitere Informationen](../flexible-server/concepts-backup-restore.md). 

-  **Auswahl der Verfügbarkeitszonen bei der Erstellung von Lesereplikaten**

    Bei der Erstellung von Lesereplikaten haben Sie die Möglichkeit, den Ort der Verfügbarkeitszonen Ihrer Wahl auszuwählen. Eine Verfügbarkeitszone ist ein Hochverfügbarkeitsangebot, das Ihre Anwendungen und Daten vor Fehlschlägen im Rechenzentrum schützt. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. [Weitere Informationen](../flexible-server/concepts-read-replicas.md)

- **Lesereplikate in Azure Database for MySQL – Flexible Server werden auf burstfähigen SKUs nicht mehr verfügbar sein**
    
    Sie werden nicht in der Lage sein, neue Lesereplikate auf dem Server der burstfähigen Ebene zu erstellen oder bestehende zu pflegen. Im Interesse einer guten Abfrage- und Entwicklungserfahrung für burstfähige SKU-Ebenen wird die Unterstützung für die Erstellung und Pflege von Lesereplikaten für Server in der Burstable-Ebene eingestellt. 

    Wenn Sie einen bestehenden Azure Database for MySQL – Flexible Server mit aktiviertem Lesereplikat besitzen, müssen Sie Ihren Server innerhalb von 60 Tagen entweder auf die Preisebenen „universell“ oder „arbeitsspeicheroptimiert“ hochskalieren oder das Lesereplikat löschen. Nach Ablauf der 60 Tage können Sie zwar weiterhin den Primärserver für Ihre Schreib-Lese-Vorgänge verwenden, die Replikation auf Lesereplikat-Server wird jedoch gestoppt. Bei neu erstellten Servern ist die Option Lesereplikat nur für die Preisstufen „universell“ und „arbeitsspeicheroptimiert“ verfügbar.  

 - **Überwachen von Azure Database for MySQL: Flexible Server mit Azure Monitor-Arbeitsmappen**
 
     Azure Database for MySQL: Flexible Server ist jetzt in Azure Monitor Arbeitsmappen integriert. Arbeitsmappen bieten einen flexiblen Bereich für die Datenanalyse und die Erstellung umfassender visueller Berichte innerhalb des Azure-Portals. Mit dieser Integration verfügt der Server über Links zu Arbeitsmappen und einigen Beispielvorlagen, mit denen der Dienst im großen Stil überwacht werden kann. Diese Vorlagen können bearbeitet, an Kundenanforderungen angepasst und an das Dashboard angeheftet werden, um eine fokussierte und organisierten Ansicht der Azure-Ressourcen zu erstellen. [Einblicke in die Abfrageleistung](./tutorial-query-performance-insights.md), [Überwachung](./tutorial-configure-audit.md) und Instanzübersichtsvorlagen sind derzeit verfügbar. [Weitere Informationen](./concepts-workbooks.md)

- **Vorauszahlung für Azure Database for MySQL-Computeressourcen mit reservierten Instanzen**

    Mit Azure Database for MySQL - Flexible Server können Sie jetzt Geld sparen, indem Sie Rechenressourcen im Voraus bezahlen, im Vergleich zu Pay-as-you-go-Preisen. Mit reservierten Azure Database for MySQL-Instanzen leisten Sie eine Vorauszahlung für MySQL-Server für einen Zeitraum von einem Jahr oder drei Jahren und erhalten dafür einen immensen Rabatt auf die Computekosten. Sie können auch eine Reservierung von Azure Database for MySQL: Single Server gegen Flexible Server tauschen. [Weitere Informationen](../concept-reserved-pricing.md)

- **Stoppen des Servers für bis zu 30 Tage, wenn der Server nicht benutzt wird**
    
    Azure Database for MySQL Flexible Server bietet Ihnen jetzt die Möglichkeit, den Server für bis zu 30 Tage anzuhalten, wenn er nicht genutzt wird, und den Server innerhalb dieses Zeitraums zu starten, wenn Sie bereit sind, Ihre Entwicklung fortzusetzen. So können Sie in Ihrem eigenen Tempo entwickeln und Entwicklungskosten für die Datenbankserver einsparen, indem Sie nur für die Ressourcen zahlen, die auch genutzt werden. Dies ist wichtig für Entwicklungs- und Testarbeitslasten und wenn Sie den Server nur einen Teil des Tages nutzen. Wenn Sie den Server anhalten, werden alle aktiven Verbindungen beendet. Wenn sich der Server im Status Angehalten befindet, wird das Compute des Servers nicht in Rechnung gestellt. Der Speicher wird jedoch weiterhin in Rechnung gestellt, da der Speicher des Servers verbleibt, um sicherzustellen, dass die Datendateien verfügbar sind, wenn der Server wieder gestartet wird. [Weitere Informationen](concept-servers.md#stopstart-an-azure-database-for-mysql-flexible-server)

- **Terraform-Unterstützung für MySQL Flexible Server**
    
    Terraform-Unterstützung für MySQL Flexible Server ist jetzt mit der [neuesten Version v2.81.0 von azurerm](https://github.com/hashicorp/terraform-provider-azurerm/blob/v2.81.0/CHANGELOG.md) verfügbar. Das detaillierte Referenzdokument für die Bereitstellung und Verwaltung eines MySQL Flexible Servers mit Terraform finden Sie [hier](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/mysql_flexible_server). Alle Bugs oder bekannten Probleme können [hier](https://github.com/hashicorp/terraform-provider-azurerm/issues) gefunden oder gemeldet werden.

- **Statischer Parameter „innodb_log_file_size“ ist jetzt konfigurierbar**

    - [innodb_log_file_size](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size) kann jetzt mit einem der folgenden Werte konfiguriert werden: 256 MB, 512 MB, 1 GB oder 2 GB. Da es sich um einen statischen Parameter handelt, ist ein Serverneustart erforderlich. Wenn Sie den Parameter „innodb_log_file_size“ vom Standardwert geändert haben, überprüfen Sie, ob der Wert von „show global status like 'innodb_buffer_pool_pages_dirty'“ (globalen Status wie „innodb_buffer_pool_pages_dirty“ anzeigen) 30 Sekunden lang auf 0 bleibt, um eine Verzögerung des Neustarts zu vermeiden. Weitere Informationen finden Sie unter [Serverparameter in Azure Database for MySQL](./concepts-server-parameters.md).

- **Verfügbarkeit in zwei weiteren Azure-Regionen**

   Azure Database for MySQL Flexible Server ist jetzt in den folgenden Azure-Regionen verfügbar:

   - USA, Westen 3
   - USA, Norden-Mitte [Weitere Informationen](overview.md#azure-regions).

- **Bekannte Probleme**
    - Wenn die primäre Region ausfällt, können keine georedundanten Server in der jeweiligen geografisch gekoppelten Region erstellt werden, da der Speicher nicht in der primären Region bereitgestellt werden kann. Sie müssen warten, bis die primäre Region verfügbar ist, um georedundante Server in der geografisch gekoppelten Region bereitstellen zu können.
    

## <a name="september-2021"></a>September 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Verfügbarkeit in drei weiteren Azure-Regionen**

   Die öffentliche Vorschau von Azure Database for MySQL – Flexible Server ist jetzt in den folgenden Azure-Regionen verfügbar:

   - UK, Westen
   - Kanada, Osten
   - Japan, Westen

- **Fehlerbehebungen**

   Die Erstellung von Selbe-Zone Hochverfügbarkeit wurde in den folgenden Regionen behoben:

   - Indien, Mitte
   - Asien, Osten
   - Korea, Mitte
   - Südafrika, Norden
   - Schweiz, Norden

## <a name="august-2021"></a>August 2021

Dieses Release von Azure Database for MySQL Flexible Server enthält die folgenden Updates.

- **Hochverfügbarkeit innerhalb einer Zone mithilfe von Hochverfügbarkeit in gleicher Zone**

  Mit diesem Dienst können Kund*innen jetzt flexibel ihre bevorzugte Verfügbarkeitszone für den Standbyserver auswählen, wenn sie Hochverfügbarkeit aktivieren. Zudem ermöglicht dieses Feature es Kund*innen, einen Standbyserver in derselben Zone wie der primäre Server bereitzustellen, wodurch die Replikationsverzögerung zwischen dem primären Server und dem Standbyserver reduziert wird. Dadurch entstehen auch niedrigere Latenzen zwischen dem Anwendungsserver und dem Datenbankserver, wenn diese in derselben Azure-Zone bereitgestellt werden. [Weitere Informationen](./concepts-high-availability.md)

- **Auswahl der Standbyzone mit zonenredundanter Hochverfügbarkeit**

  Der Dienst bietet Kund*innen jetzt die Möglichkeit, den Standort der Standbyserverzone zu wählen. Mit diesem Feature können Kund*innen ihren Standbyserver in der Zone ihrer Wahl bereitstellen. Die Unterbringung der Standby-Datenbankserver und der Standby-Anwendungen in derselben Zone verringert die Latenzzeiten und ermöglicht es den Kunden, sich besser auf Notfallwiederherstellung-Situationen und Zonenausfall-Szenarien vorzubereiten. [Weitere Informationen](./concepts-high-availability.md)

- **Integration in private DNS-Zonen**

  [Privates DNS von Azure](../../dns/private-dns-privatednszone.md) ist ein zuverlässiger und sicherer DNS-Dienst für Ihre virtuellen Netzwerke, der Dienstnamen in IP-Adressen übersetzt. Privates Azure-DNS verwaltet Domänennamen im virtuellen Netzwerk und löst diese auf, ohne dass eine benutzerdefinierte DNS-Lösung konfiguriert werden muss. Dadurch können Sie Ihre in einem virtuellen Netzwerk ausgeführte Anwendung mit Ihrem flexiblen Server verbinden, der in einem lokal oder global mit Peering verbundenen virtuellen Netzwerk ausgeführt wird. Azure Database for MySQL Flexible Server ermöglicht jetzt die Integration in eine private Azure-DNS-Zone, um eine nahtlose Auflösung von privatem DNS innerhalb des aktuellen VNet oder eines beliebigen VNet mit Peering zu ermöglichen, mit dem die private DNS-Zone verknüpft ist. Wenn sich bei dieser Integration die IP-Adresse des flexiblen Back-End-Servers während eines Failovers oder eines anderen Ereignisses ändert, wird Ihre integrierte private DNS-Zone automatisch aktualisiert, damit die Anwendungskonnektivität automatisch wiederhergestellt wird, sobald der Server online ist. [Weitere Informationen](./concepts-networking-vnet.md)

- **Zeitpunktwiederherstellung für einen Server in einem angegebenen virtuellen Netzwerk**

  Da die Zeitpunktwiederherstellung im Dienst nun möglich ist, können Kund*innen Netzwerkeinstellungen konfigurieren, damit Benutzer*innen bei einem Wiederherstellungsvorgang zwischen privaten und öffentlichen Netzwerkoptionen wechseln können. Dank diesem Feature können Kund*innen flexibel einen Server, der wiederhergestellt wird, in ein angegebenes virtuelles Netzwerk injizieren, um ihre Verbindungsendpunkte zu schützen. [Weitere Informationen](./how-to-restore-server-portal.md)

- **Zeitpunktwiederherstellung für einen Server in einer Verfügbarkeitszone**

  Die Zeitpunktwiederherstellung für den Dienst aktiviert jetzt die Konfiguration von Verfügbarkeitszonen. Die Unterbringung von Datenbankservern und Standby-Anwendungen in derselben Zone verringert die Latenzzeiten und ermöglicht es den Kunden, sich besser auf Notfallwiederherstellung-Situationen und Zonenausfall-Szenarien vorzubereiten. [Weitere Informationen](./concepts-high-availability.md)

- **Plug-Ins „validate_password“ und „caching_sha2_password“ in der privaten Vorschau verfügbar**

  Flexible Server unterstützt jetzt die Aktivierung der Plug-Ins „validate_password“ und „caching_sha2_password“ in der privaten Vorschau. Senden Sie uns eine E-Mail: AskAzureDBforMySQL@service.microsoft.com.

- **Verfügbarkeit in vier zusätzlichen Azure-Regionen**

   Die öffentliche Vorschau von Azure Database for MySQL – Flexible Server ist jetzt in den folgenden Azure-Regionen verfügbar:

   - Australien, Südosten
   - Südafrika, Norden
   - Asien, Osten (Hongkong)
   - Indien, Mitte

   [Weitere Informationen](overview.md#azure-regions)

- **Bekannte Probleme**

   - Direkt nach dem Failover des zonenredundanten Hochverfügbarkeitsservers kann der Client keine Verbindung mit dem Server herstellen, wenn SSL mit der auf VERIFY_IDENTITY festgelegten Eigenschaft ssl_mode verwendet wird. Dieses Problem kann umgangen werden, indem ssl_mode auf VERIFY_CA festgelegt wird.
   - In den folgenden Regionen kann kein Server mit Hochverfügbarkeit in gleicher Zone erstellt werden: „Indien, Mitte“, „Asien, Osten“, „Südkorea, Mitte“, „Südafrika, Norden“ und „Schweiz, Norden“.
   - Nach einem Hochverfügbarkeitsfailover kann es selten vorkommen, dass der primäre Server den Modus read_only annimmt. Sie können dieses Problem beheben, indem Sie den Wert read_only auf dem Blatt für Serverparameter in OFF ändern.
   - Nachdem Sie die Rechenleistung auf dem Blatt „Compute und Speicher“ erfolgreich skaliert haben, werden die IOPS auf den SKU-Standardwert zurückgesetzt. Kunden können das Problem umgehen, indem sie die IOPs auf dem Blatt Compute+Storage nach der Bereitstellung und dem anschließenden Zurücksetzen der IOPS auf den gewünschten Wert skalieren (der zuvor festgelegt wurde).

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

  Ab dem 15. Juni 2021 haben Kund*innen mit [kostenlosem Azure-Konto](https://azure.microsoft.com/free/) zwölf Monate lang kostenlosen Zugriff auf Azure Database for MySQL Flexible Server. Darin sind monatlich 750 Nutzungsstunden und 32 GB Speicher enthalten. Kund*innen können dieses Angebot zum Entwickeln und Bereitstellen von Anwendungen nutzen, die Azure Database for MySQL Flexible Server verwenden. [Weitere Informationen](./how-to-deploy-on-azure-free-account.md)

- **Automatische Speichervergrößerung**

  Die automatische Speichervergrößerung verhindert, dass ein Server nicht mehr über genügend Speicherplatz verfügt und schreibgeschützt wird. Wenn die automatische Speichervergrößerung aktiviert ist, wird der Speicher automatisch ohne Beeinträchtigung der Workload vergrößert. Ab dem 21. Juni 2021 wird bei allen neu erstellten Servern die automatische Speichervergrößerung standardmäßig aktiviert sein. [Weitere Informationen](concepts-compute-storage.md#storage-auto-grow)

- **Datenreplikation**

    Flexible Server unterstützt jetzt die [Datenreplikation](concepts-data-in-replication.md). Mit diesem Feature können Sie Daten von einem MySQL-Server synchronisieren und zu Azure Database for MySQL Flexible Server migrieren. Der MySQL-Server kann lokal, auf VMs, in Azure Database for MySQL Single Server oder auf Datenbankdiensten außerhalb von Azure ausgeführt werden. Hier finden Sie weitere Informationen zum [Konfigurieren der Datenreplikation](how-to-data-in-replication.md).

- **GitHub Actions-Unterstützung in der Azure CLI**

  Mit der Flexible Server-CLI können Kund*innen jetzt Workflows automatisieren, um Updates mithilfe von GitHub Actions bereitzustellen. Mit diesem Feature können Sie Datenbankupdates mit GitHub Actions-Workflows für MySQL einrichten und bereitstellen. Mithilfe dieser CLI-Befehle können Sie ein Repository mit Continuous Deployment einrichten, um die Bereitstellung zu vereinfachen. [Weitere Informationen](/cli/azure/mysql/flexible-server/deploy?view=azure-cli-latest&preserve-view=true)

- **Fixes für erzwungenes Failover für zonenredundante Hochverfügbarkeit**

  Dieses Release enthält Fixes für bekannte Probleme mit erzwungenem Failover, damit Serverparameter und weitere IOPS-Änderungen bei Failovern beibehalten werden.

- **Bekannte Probleme**

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

  Flexible Server unterstützt jetzt die asynchrone Replikation von Daten von einem Azure Database for MySQL-Server (die „Quelle“) zu bis zu zehn weiteren Azure Database for MySQL-Servern (die „Replikate“) in derselben Region. Durch dieses Feature können leseintensive Workloads je nach Benutzereinstellungen aufskaliert und gleichmäßig auf Replikatserver verteilt werden. [Weitere Informationen](concepts-read-replicas.md)

## <a name="contacts"></a>Kontakte

Wenn Sie Fragen oder Vorschläge zur Arbeit mit Azure Database for MySQL haben, können Sie sich an die folgenden Stellen wenden:

- Wenn Sie den Azure-Support kontaktieren möchten, [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie uns Feedback geben oder Wünsche zu neuen Funktionen äußern möchten, senden Sie uns eine E-Mail an AskAzureDBforMySQL@service.microsoft.com.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den [Preisen für Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/server/).
- Suchen Sie in der [öffentlichen Dokumentation](index.yml) nach Azure Database for MySQL Flexible Server.
- Lesen Sie Details zur [Problembehandlung für häufige Migrationsfehler](../howto-troubleshoot-common-errors.md).
