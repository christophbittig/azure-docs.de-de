---
title: Übersicht – Azure Database for MySQL Flexible Server
description: Erfahren Sie mehr zu Azure Database for MySQL Flexible Server, einem relationalen Datenbankdienst in der Microsoft-Cloud, der auf MySQL Community Edition basiert.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, references_regions
ms.topic: overview
ms.date: 08/10/2021
ms.openlocfilehash: 13d220dc0b168ade0bd6493025d858e01772a980
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893953"
---
# <a name="azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL: Flexibler Server 

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Azure Database for MySQL, ein Dienst, der auf MySQL Community Edition basiert, ist in zwei Bereitstellungsmodi verfügbar:

- Flexible Server
- Einzelner Server

In diesem Artikel erhalten Sie eine Übersicht und Einführung in die grundlegenden Konzepte des Modells zur Bereitstellung einer Flexible Server-Instanz. Informationen dazu, wie Sie entscheiden, welche Bereitstellungsoption für Ihre Workload geeignet ist, finden Sie unter [Auswählen der richtigen MySQL Server-Option in Azure](./../select-right-deployment-type.md).

## <a name="overview"></a>Übersicht

Azure Database for MySQL Flexible Server ist ein vollständig verwalteter produktionsbereiter Datenbankdienst, der eine differenziertere Steuerung und mehr Flexibilität bei den Verwaltungsfunktionen und Konfigurationseinstellungen der Datenbank ermöglichen soll. Mit der Flexible Server-Architektur können Benutzer Hochverfügbarkeit in einer einzelnen Verfügbarkeitszone sowie in mehreren Verfügbarkeitszonen auswählen. Flexible Server-Implementierungen bieten eine bessere Kostenoptimierung mit der Möglichkeit, den Server anzuhalten/zu starten, sowie einen burstfähigen Computetarif, der sich ideal für Workloads eignet, die nicht dauerhaft die volle Computekapazität benötigen. Flexible Server unterstützt außerdem reservierte Instanzen, sodass Sie bei den Kosten bis zu 63 Prozent sparen können. Dies ist ideal für Produktionsworkloads mit vorhersagbaren Computekapazitätsanforderungen. Der Dienst unterstützt die MySQL Community-Versionen 5.7 und 8.0. Der Dienst ist ab heute in vielen [Azure-Regionen](overview.md#azure-regions) allgemein verfügbar.

Die Flexible Server-Bereitstellungsoption bietet drei Computeebenen: „Burstfähig“, „Universell“ und „Arbeitsspeicheroptimiert“. Jede Ebene bietet unterschiedliche Compute- und Arbeitsspeicherkapazität, um Ihre Datenbankworkloads zu unterstützen. Sie können Ihre erste App mit der Ebene „Burstfähig“ für wenige US-Dollar im Monat erstellen und die Skalierung dann so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Durch die dynamische Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie bezahlen nur für die benötigten Ressourcen und auch nur dann, wenn Sie sie verwenden. Ausführliche Informationen finden Sie unter [Compute- und Speicheroptionen in Azure Database for MySQL: Flexible Server](concepts-compute-storage.md).

Flexible Server-Instanzen eignen sich am besten für Folgendes:
- Einfache Bereitstellungen, vereinfachte Skalierung und geringer Aufwand für die Datenbankverwaltung bei Funktionen wie Sicherungen, Hochverfügbarkeit, Sicherheit und Überwachung
- Anwendungsentwicklungen, die eine Communityversion von MySQL mit besseren Steuerungs- und Anpassungsmöglichkeiten erfordern
- Produktionsworkloads mit Hochverfügbarkeit in der gleichen Zone, mit zonenredundanter Hochverfügbarkeit und mit verwalteten Wartungsfenstern
- Vereinfachte Entwicklungsumgebung 
- Sicherheit, Compliance und Datenschutz auf Unternehmensniveau

Neueste Updates für Flexible Server finden Sie unter [Neuerungen in Azure Database for MySQL Flexible Server](whats-new.md).

![Flexible Server-Konzeptdiagramm](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="free-12-month-offer"></a>Kostenloses Angebot für 12 Monate

Mit einem [kostenlosen Azure-Konto](https://azure.microsoft.com/free/) können Sie Flexible Server 12 Monate lang kostenlos mit folgenden monatlichen Grenzwerten nutzen:
* **750 Stunden burstfähige B1MS-Instanz** – genügend Stunden zur kontinuierlichen Ausführung einer Datenbankinstanz jeden Monat.
* **32 GB** Speicher und **32 GB** Sicherungsspeicher. 

Sie können dieses Angebot zum Entwickeln und Bereitstellen von Anwendungen nutzen, die Azure Database for MySQL Flexible Server verwenden. Informationen zum kostenlosen Erstellen und Nutzen eines flexiblen Servers mit einem kostenlosen Azure-Konto finden Sie in [diesem Tutorial](how-to-deploy-on-azure-free-account.md). 

## <a name="high-availability-within-and-across-availability-zones"></a>Hochverfügbarkeit in Verfügbarkeitszonen und verfügbarkeitszonenübergreifend

Azure Database for MySQL Flexible Server ermöglicht das Konfigurieren von Hochverfügbarkeit mit automatischem Failover. Die Hochverfügbarkeitslösung wurde entwickelt, um sicherzustellen, dass committete Daten aufgrund von Ausfällen nie verloren gehen, und um die allgemeine Betriebszeit für Ihre Anwendung zu verbessern.Wenn Hochverfügbarkeit konfiguriert wird, stellt Flexible Server ein Standbyreplikat automatisch bereit und verwaltet es. Es gibt zwei Architekturmodelle für Hochverfügbarkeit: 

- **Zonenredundante Hochverfügbarkeit**: Diese Option wird für eine vollständige Isolierung und Redundanz der Infrastruktur über mehrere Verfügbarkeitszonen hinweg empfohlen. Sie bietet den höchsten Verfügbarkeitsgrad, erfordert aber eine zonenübergreifende Konfiguration der Anwendungsredundanz. Zonenredundante Hochverfügbarkeit wird bevorzugt, wenn bei jedem Infrastrukturausfall in der Verfügbarkeitszone die bestmögliche Verfügbarkeit erzielt werden soll und die Latenz in der Verfügbarkeitszone akzeptabel ist. Zonenredundante Hochverfügbarkeit gibt es nur in  [Azure-Regionen](overview.md#azure-regions) , die mehrere Verfügbarkeitszonen unterstützen und in denen zonenredundante Premium-Dateifreigaben zur Verfügung stehen. 

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Zonenredundante Hochverfügbarkeit":::

- **Hochverfügbarkeit in gleicher Zone:** Diese Option wird bei Infrastrukturredundanz mit geringerer Netzwerklatenz bevorzugt, da sich sowohl der primäre Server als auch der Standbyserver in derselben Verfügbarkeitszone befinden. Sie bietet Hochverfügbarkeit ohne die zonenübergreifende Konfiguration der Anwendungsredundanz. Hochverfügbarkeit in gleicher Zone wird bevorzugt, wenn Sie bestmögliche Verfügbarkeit innerhalb einer einzelnen Verfügbarkeitszone mit der geringstmöglichen Netzwerklatenz erreichen möchten. Hochverfügbarkeit in gleicher Zone steht in [allen Azure-Regionen](overview.md#azure-regions) zur Verfügung, in denen Sie Azure Database for MySQL Flexible Server erstellen können. 

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Hochverfügbarkeit in gleicher Zone":::

Weitere Informationen finden Sie unter [Hochverfügbarkeitskonzepte](concepts-high-availability.md).

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatisches Patchen mit verwaltetem Wartungsfenster

Der Dienst führt automatisches Patchen für die zugrunde liegende Hardware, das Betriebssystem und die Datenbank-Engine durch. Das Patchen umfasst Sicherheits- und Softwareupdates. Für die MySQL-Engine sind im Rahmen des geplanten Wartungsreleases auch kleinere Versionsupgrades enthalten. Benutzer können den Patchzeitplan so konfigurieren, dass er vom System verwaltet wird, oder einen eigenen Zeitplan definieren. Im Rahmen des Wartungszeitplans wird der Patch angewendet. Möglicherweise muss der Server als Teil des Patchprozesses neu gestartet werden, um das Update abzuschließen. Mithilfe des benutzerdefinierten Zeitplans können Benutzer ihren Patchzyklus überlegt gestalten und ein Wartungsfenster mit minimalen Auswirkungen auf das Unternehmen wählen. Im Allgemeinen gilt für den Dienst im Rahmen der kontinuierlichen Integration und Veröffentlichung ein monatlicher Releasezeitplan.

Weitere Informationen finden Sie unter [Geplante Wartung](concepts-maintenance.md). 

## <a name="automatic-backups"></a>Automatische Sicherungen

Für Azure Database for MySQL Flexible Server werden Sicherungen automatisch erstellt und in einem vom Benutzer konfigurierten lokal redundanten oder georedundanten Speicher gespeichert. Mithilfe von Sicherungen können Sie den Server zu einem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums der Sicherung wiederherstellen. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Für die Aufbewahrungsdauer kann optional ein Wert zwischen einem Tag und 35 Tagen konfiguriert werden. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

Weitere Informationen finden Sie unter [Sicherungskonzepten](concepts-backup-restore.md).

## <a name="network-isolation"></a>Netzwerkisolation

Um eine Verbindung mit Ihrer Azure Database for MySQL Flexible Server-Bereitstellung herzustellen, haben Sie zwei Netzwerkoptionen: den **privaten Zugriff** (VNET-Integration) und den **öffentlichen Zugriff (zugelassene IP-Adressen)** . 

- **Privater Zugriff (VNET-Integration):** Sie können Ihren flexiblen Server in Ihrer [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)-Instanz bereitstellen. Virtuelle Azure-Netzwerke ermöglichen eine private und sichere Netzwerkkommunikation. Ressourcen in einem virtuellen Netzwerk können über private IP-Adressen kommunizieren.

  Die VNET-Integration bietet sich an, wenn Sie die folgenden Funktionen benötigen:

  - Verbinden von Azure-Ressourcen im gleichen virtuellen Netzwerk mit Ihrem flexiblen Server über private IP-Adressen
  - Herstellen einer Verbindung von Azure-externen Ressourcen mit Ihrem flexiblen Server über ein VPN oder eine ExpressRoute-Verbindung
  - Kein öffentlicher Endpunkt

- **Öffentlicher Zugriff (zugelassene IP-Adressen)** : Sie können Ihren flexiblen Server mit einem öffentlichen Endpunkt bereitstellen. Der öffentliche Endpunkt ist eine öffentlich auflösbare DNS-Adresse. Der Ausdruck „zugelassene IP-Adressen“ bezieht sich auf einen Bereich von IP-Adressen, denen Sie die Berechtigung erteilen, auf den Server zuzugreifen. Diese Berechtigungen heißen **Firewallregeln**.

Weitere Informationen finden Sie unter [Netzwerkkonzepte](concepts-networking.md).

## <a name="adjust-performance-and-scale-within-seconds"></a>Anpassen von Leistung und Skalierung in nur wenigen Sekunden

Der Flexible Server-Dienst ist in drei SKU-Tarifen verfügbar: „Burstfähig“, „Allgemeiner Zweck“ und „Arbeitsspeicheroptimiert“. Der Tarif „Burstfähig“ eignet sich am besten für die kostengünstige Entwicklung und Workloads mit geringer Parallelität, die nicht ständig die volle Computekapazität benötigen. Die Tarife „Universell“ und „Arbeitsspeicheroptimiert“ eignen sich besser für Produktionsworkloads, die eine hohe Parallelität, Skalierung und vorhersagbare Leistung erfordern. Sie können Ihre erste App mit einer kleinen Datenbank für wenige USD im Monat erstellen und die Skalierung dann nahtlos so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Die Speicherskalierung geschieht online und unterstützt das automatische Wachstum des Speichers. Flexible Server ermöglicht ihnen die Bereitstellung zusätzlicher IOPS bis zu 20.000 IOPS über dem zusätzlichen IOPS-Grenzwert unabhängig vom Speicher. Mit dieser Funktion können Sie die Anzahl der IOPS basierend auf Ihren Workloadanforderungen jederzeit erhöhen oder verringern. Durch die dynamische Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie zahlen nur für die Ressourcen, die Sie verwenden. 

Weitere Informationen finden Sie unter [Compute- und Speicherkonzepte](concepts-compute-storage.md).

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Horizontale Skalierung Ihrer Leseworkload mit bis zu zehn Lesereplikaten

MySQL ist eine der beliebtesten Datenbank-Engines für die Ausführung von Web- und mobilen Anwendungen im Internet. Viele unserer Kunden verwenden es für ihre Onlinebildungsdienste, Videostreamingdienste, digitalen Zahlungslösungen, E-Commerce-Plattformen, Gamingdienste, Nachrichtenportale sowie für Websites für Behörden und das Gesundheitswesen. Diese Dienste müssen in dem Maße, wie der Datenverkehr im Web oder bei mobilen Anwendungen zunimmt, genutzt und skaliert werden.

Auf der Anwendungsseite wird die Anwendung typischerweise in Java oder PHP entwickelt und migriert, um in  [Azure-VM-Skalierungsgruppen](../../virtual-machine-scale-sets/overview.md) oder [Azure App Services](../../app-service/overview.md) ausgeführt zu werden, oder sie wird containerisiert, um unter [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md) ausgeführt zu werden. Mit VM-Skalierungsgruppen, App Service oder AKS als zugrunde liegende Infrastruktur wird die Anwendungsskalierung vereinfacht, indem neue virtuelle Computer sofort bereitgestellt und die zustandslosen Komponenten von Anwendungen repliziert werden, um die Anforderungen zu erfüllen, aber oft wird die Datenbank als zentrale zustandsbehaftete Komponente zum Engpass.

Mithilfe des Lesereplikatfeatures können Sie Daten von einer Azure Database for MySQL Flexible Server-Instanz auf einem schreibgeschützten Server replizieren. Sie können vom Quellserver auf **bis zu zehn Replikate** replizieren. Replikate werden asynchron mithilfe des auf der [Position der nativen, binären Protokolldatei (binlog) basierenden Replikationsverfahrens](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) der MySQL-Engine aktualisiert. Sie können eine Proxylösung für den Lastenausgleich wie [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) verwenden, um Ihre Anwendungsworkload nahtlos horizontal auf Lesereplikate zu skalieren, ohne dass Refactoringkosten anfallen. 

Weitere Informationen finden Sie unter [Lesereplikatkonzepte](concepts-read-replicas.md).

## <a name="setup-hybrid-or-multi-cloud-data-synchronization-with-data-in-replication"></a>Einrichten der Hybrid- oder Multi-Cloud-Datensynchronisierung mit Datenreplikation

Die Datenreplikation ermöglicht das Synchronisieren von Daten von einem externen MySQL-Server mit dem Dienst Azure Database for MySQL Flexible. Der externe Server kann lokal, in virtuellen Computern, in Azure Database for MySQL Single Server oder in einem Datenbankdienst vorhanden sein, der von anderen Cloudanbietern gehostet wird. Die Datenreplikation basiert auf der Position der binären Protokolldatei (binlog). Zu den wichtigsten Szenarien, bei denen die Verwendung der Datenreplikation infrage kommt, zählen Folgende:
* Synchronisierung von Hybriddaten
* Synchronisierung von Daten in verschiedenen Clouds
* [Migration zu Flexible Server mit minimaler Ausfallzeit](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)

Weitere Informationen finden Sie unter [Datenreplikationskonzepte](concepts-data-in-replication.md).


## <a name="stopstart-server-to-optimize-cost"></a>Anhalten/Starten des Servers zum Optimieren der Kosten

Der Flexible Server-Dienst ermöglicht Ihnen, Server bei Bedarf anzuhalten und zu starten, um Ihre Kosten zu optimieren. Die Abrechnung des Computetarifs wird sofort beendet, sobald der Server angehalten wird. Dies kann erhebliche Kosteneinsparungen bei Entwicklung, Tests und zeitgebundenen, vorhersehbaren Produktionsworkloads ermöglichen. Der Server verbleibt 30 Tage im angehaltenen Zustand, es sei denn, er wird früher wieder gestartet.

Weitere Informationen finden Sie unter [Serverkonzepte](concept-servers.md).

## <a name="enterprise-grade-security-compliance-and-privacy"></a>Sicherheit, Compliance und Datenschutz auf Unternehmensniveau

Der flexible Serverdienst nutzt das FIPS 140-2-zertifizierte Kryptografiemodul für die Speicherverschlüsselung ruhender Daten. Daten wie Sicherungen und temporäre Dateien, die während der Ausführung von Abfragen erstellt wurden, werden verschlüsselt. Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, wobei die Schlüssel (standardmäßig) vom System verwaltet werden.

Der Dienst verschlüsselt Daten während der Übertragung mit der Transport Layer Security, was standardmäßig erzwungen wird. Standardmäßig unterstützt Flexible Server verschlüsselte Verbindungen mit Transport Layer Security (TLS 1.2); alle eingehenden Verbindungen mit TLS 1.0 und TLS 1.1 werden verweigert. Die SSL-Erzwingung kann durch Festlegen des Serverparameters „require_secure_transport“ deaktiviert werden, und Sie können die mindestens erforderliche „tls_version“ für Ihren Server festlegen.

Weitere Informationen finden Sie unter [Verwenden verschlüsselter Verbindungen mit flexiblen Servern](how-to-connect-tls-ssl.md).

Flexible Server ermöglicht den vollständigen privaten Zugriff auf die Server mithilfe der Integration des [virtuellen Azure-Netzwerks](../../virtual-network/virtual-networks-overview.md) (VNET). Server im virtuellen Azure-Netzwerk können nur über private IP-Adressen erreicht und verbunden werden. Bei der VNET-Integration wird der öffentliche Zugriff verweigert, sodass Server nicht über öffentliche Endpunkte erreicht werden können.

Weitere Informationen finden Sie in den [Netzwerkkonzepten](concepts-networking.md).

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung

Der Flexible Server-Dienst verfügt über integrierte Funktionen für Leistungsüberwachung und Warnungen. Alle Azure-Metriken werden im Minutentakt erfasst, und für jede Metrik steht ein Verlauf von 30 Tagen zur Verfügung. Sie können Warnungen für die Metriken konfigurieren. Der Dienst macht Hostservermetriken verfügbar, um die Ressourcenauslastung zu überwachen, und ermöglicht die Konfiguration von Protokollen für langsame Abfragen. Mit diesen Tools können Sie Ihre Workloads schnell optimieren und Ihren Server für optimale Leistung konfigurieren. Azure Database for MySQL Flexible Server ermöglicht mithilfe von Azure Monitor-Arbeitsmappen das Visualisieren der Daten für langsame Abfragen und Überwachungsprotokolle. Mit Arbeitsmappen erhalten Sie einen flexiblen Canvas zum Analysieren von Daten und Erstellen umfangreicher visueller Berichte innerhalb des Azure-Portals. Azure Database for MySQL Flexible Server bietet drei verfügbare Arbeitsmappenvorlagen, die sofort verfügbar sind: Serverübersicht, [Überwachung](tutorial-configure-audit.md) und [Query Performance Insights](tutorial-query-performance-insights.md). Die Arbeitsmappe [Query Performance Insight](tutorial-query-performance-insights.md) soll den Zeitaufwand für die Problembehandlung der Datenbankleistung reduzieren. Dazu stellt sie u. a. folgende Informationen bereit:

* Erste N zeitintensive Abfragen und ihr Trend
* Abfragedetails: Zeigen Sie den Abfragetext sowie den Ausführungsverlauf mit minimaler, maximaler und durchschnittlicher Abfragedauer sowie die Standardabweichung der Abfragedauer an.
* Ressourcenverwendung (CPU, Arbeitsspeicher und Speicher)

Darüber hinaus können Sie Community-Überwachungstools wie [Percona Monitoring and Management](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/monitor-azure-database-for-mysql-using-percona-monitoring-and/ba-p/2568545) nutzen und in Ihren MySQL Flexible Server integrieren. 

Weitere Informationen finden Sie unter [Überwachungskonzepte](concepts-monitoring.md).

## <a name="migration"></a>Migration

Der Dienst führt die MySQL-Community-Version aus. Dies ermöglicht vollständige Anwendungskompatibilität und erfordert minimale Refactoringkosten für die Migration vorhandener Anwendungen, die in der MySQL-Engine entwickelt wurden, zu Flexible Server. Die Migration zu Flexible Server kann mithilfe der folgenden Option durchgeführt werden:

### <a name="offline-migrations"></a>Offlinemigrationen
*   Verwenden von Azure Data Migration Service, wenn die Netzwerkbandbreite zwischen Quelle und Azure gut ist (z. B. ExpressRoute mit hoher Geschwindigkeit). Weitere Informationen finden Sie in den Schrittanleitungen – [Durchführen einer Offlinemigration von MySQL zu Azure Database for MySQL mithilfe von DMS – Azure Database Migration Service](../../dms/tutorial-mysql-azure-mysql-offline-portal.md)
*   Verwenden Sie mydumper/myloader, um die Komprimierungseinstellungen zu nutzen, um Daten effizient über Netzwerke mit niedriger Geschwindigkeit (z. B. öffentliches Internet) zu verschieben. Weitere Informationen finden Sie in den Schritt-für-Schritt-Anweisungen [Migrieren großer Datenbanken zu Azure Database for MySQL mit mydumper/myloader](../../mysql/concepts-migrate-mydumper-myloader.md)

### <a name="online-or-minimal-downtime-migrations"></a>Onlinemigrationen oder Migrationen mit minimaler Ausfallzeit
Verwenden Sie die Datenreplikation mit einer konsistenten Sicherung/Wiederherstellung von mydumper/myloader für das anfängliche Seeding. Weitere Informationen finden Sie unter – [Tutorial: Migration von Azure Database for MySQL mit minimaler Ausfallzeit – Single Server zu Azure Database for MySQL – Flexible Server](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)

Informationen zum Migrieren aus Azure Database for MySQL Single Server zu Flexible Server in 5 einfachen Schritten finden Sie in [diesem Blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057).

Weitere Informationen finden Sie unter [Auswählen der geeigneten Tools für die Migration zu Azure Database for MySQL](../../mysql/how-to-decide-on-right-migration-tools.md).

## <a name="azure-regions"></a>Azure-Regionen

Ein Vorteil der Ausführung Ihrer Workload in Azure ist die globale Reichweite. Der flexible Server für Azure Database for MySQL ist derzeit in den folgenden Azure-Regionen verfügbar:

| Region | Verfügbarkeit | Hochverfügbarkeit in gleicher Zone | Zonenredundante Hochverfügbarkeit |
| --- | --- | --- | --- |
| Australien (Osten) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Australien, Südosten | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Brasilien Süd | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Kanada, Mitte | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Kanada, Osten | :heavy_check_mark: | :x: | :x: |
| Indien, Mitte | :heavy_check_mark: | :heavy_check_mark: | :x: |
| USA (Mitte) | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Asien, Osten (Hongkong) | :heavy_check_mark: | :heavy_check_mark: | :x: |
| East US | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| USA (Ost) 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Frankreich, Mitte | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Deutschland, Westen-Mitte | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Japan, Osten | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Japan, Westen | :heavy_check_mark: | :x: | :x: |
| Korea, Mitte | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Korea, Süden | :heavy_check_mark: | :heavy_check_mark: | :x: |
| USA Nord Mitte | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Nordeuropa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| USA Nord Mitte | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Norwegen, Osten | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Südafrika, Norden | :heavy_check_mark: | :heavy_check_mark: | :x: |
| USA Süd Mitte | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Asien, Südosten | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Schweiz, Norden | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Vereinigte Arabische Emirate, Norden | :heavy_check_mark: | :heavy_check_mark: | :x: |
| UK, Süden | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| UK, Westen | :heavy_check_mark: | :x: | :x: |
| USA, Westen-Mitte | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Europa, Westen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| USA (Westen) | :heavy_check_mark: | :heavy_check_mark: | :x: |
| USA, Westen 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| USA, Westen 3 | :heavy_check_mark: | :heavy_check_mark: | :x: |

## <a name="contacts"></a>Kontakte

Sollten Sie Fragen oder Vorschläge im Zusammenhang mit der Verwendung eines flexiblen Servers für Azure Database for MySQL haben, können Sie eine E-Mail an das zuständige Team senden ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Bei dieser E-Mail-Adresse handelt es sich nicht um einen Alias für technischen Support.

Weitere Kontaktmöglichkeiten:

- Wenn Sie den Azure-Support kontaktieren möchten, [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun die Grundlagen zum Bereitstellungsmodus Azure Database for MySQL Single Server kennen, können Sie mit den folgenden Themen fortfahren:

- Erstellen Sie Ihren ersten Server.
  - [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mit dem Azure-Portal](quickstart-create-server-portal.md)
  - [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mithilfe der Azure CLI](quickstart-create-server-cli.md)
  - [Erstellen einer Azure Database for MySQL Flexible Server-Instanz mithilfe der Azure CLI](how-to-manage-server-portal.md)

- Erstellen Sie Ihre erste App in Ihrer bevorzugten Sprache:
  - [Python](connect-python.md)
  - [PHP](connect-php.md)
