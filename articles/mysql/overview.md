---
title: 'Übersicht: Azure Database for MySQL'
description: Hier finden Sie Informationen zum Azure Database for MySQL-Dienst, einem relationalen Datenbankdienst in der Microsoft-Cloud, der auf der MySQL Community-Edition basiert.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: e93432347de7df3b2743143c68078b3dfc853848
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894353"
---
# <a name="what-is-azure-database-for-mysql"></a>Was ist Azure-Datenbank für MySQL?

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL ist ein relationaler Datenbankdienst in der Microsoft-Cloud, der auf den Versionen 5.6 (eingestellt), 5.7 und 8.0 der Datenbank-Engine [MySQL Community Edition](https://www.mysql.com/products/community/) (verfügbar unter der GPLv2-Lizenz) basiert. Azure-Datenbank für MySQL bietet Folgendes:

- Zonenredundante Hochverfügbarkeit und Hochverfügbarkeit in gleicher Zone
- Volle Kontrolle mit der Möglichkeit, Ihr geplantes Wartungsfenster auszuwählen
- Schutz von Daten durch automatische Sicherungen und Point-in-Time-Wiederherstellungen für bis zu 35 Tage.
- Automatisiertes Patchen und Warten für die zugrunde liegende Hardware, das Betriebssystem und die Datenbank-Engine, um die Sicherheit des Diensts zu gewährleisten und sicherzustellen, dass er stets auf dem aktuellen Stand ist
- Vorhersagbare Leistung inklusive nutzungsbasierter Bezahlung.
- Elastische Skalierung innerhalb weniger Sekunden.
- Kostenoptimierungsoptionen mit kostengünstiger burstfähiger SKU und der Möglichkeit, Server anzuhalten/zu starten
- Sicherheit auf Unternehmensniveau, branchenführende Compliance und Datenschutz, um sowohl ruhende vertrauliche Daten als auch Daten während der Übertragung zu schützen
- Überwachung und Automatisierung, um die Verwaltung und Überwachung für umfangreiche Bereitstellungen zu vereinfachen.
- Branchenführender Support.

Diese Funktionen erfordern fast keine Administration und werden alle ohne zusätzliche Kosten bereitgestellt. Mit diesen Funktionen können Sie sich auf die schnelle Entwicklung von Apps und die Verkürzung des Zeitraums bis zur Markteinführung konzentrieren, anstatt wertvolle Zeit und Ressourcen für die Verwaltung von virtuellen Computern und der Infrastruktur aufwenden zu müssen. Darüber hinaus können Sie Ihre Anwendung weiterhin mit den Open-Source-Tools und der Plattform Ihrer Wahl entwickeln und mit der Geschwindigkeit und Effizienz liefern, die Ihr Unternehmen verlangt, ohne dass Sie sich dafür neue Fähigkeiten aneignen müssen.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Konzeptdiagramm zu Azure-Datenbank für MySQL":::

## <a name="deployment-models"></a>Bereitstellungsmodelle

Azure Database for MySQL, ein Dienst, der auf MySQL Community Edition basiert, ist in zwei Bereitstellungsmodi verfügbar:
- Flexible Server
- Einzelner Server 

### <a name="azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL: Flexibler Server

Azure Database for MySQL Flexible Server ist ein vollständig verwalteter produktionsbereiter Datenbankdienst, der eine differenziertere Steuerung und mehr Flexibilität bei den Verwaltungsfunktionen und Konfigurationseinstellungen der Datenbank ermöglichen soll. Mit der Flexible Server-Architektur können Benutzer Hochverfügbarkeit in einer einzelnen Verfügbarkeitszone sowie in mehreren Verfügbarkeitszonen auswählen. Flexible Server-Implementierungen bieten eine bessere Kostenoptimierung mit der Möglichkeit, den Server anzuhalten/zu starten, sowie einen burstfähigen Computetarif, der sich ideal für Workloads eignet, die nicht dauerhaft die volle Computekapazität benötigen. Flexible Server unterstützt außerdem reservierte Instanzen, sodass Sie bei den Kosten bis zu 63 Prozent sparen können. Dies ist ideal für Produktionsworkloads mit vorhersagbaren Computekapazitätsanforderungen. Der Dienst unterstützt die MySQL Community-Versionen 5.7 und 8.0. Der Dienst ist ab heute in vielen [Azure-Regionen](flexible-server/overview.md#azure-regions) allgemein verfügbar.

Die Flexible Server-Bereitstellungsoption bietet drei Computeebenen: „Burstfähig“, „Universell“ und „Arbeitsspeicheroptimiert“. Jede Ebene bietet unterschiedliche Compute- und Arbeitsspeicherkapazität, um Ihre Datenbankworkloads zu unterstützen. Sie können Ihre erste App mit der Ebene „Burstfähig“ für wenige US-Dollar im Monat erstellen und die Skalierung dann so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Durch die dynamische Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie bezahlen nur für die benötigten Ressourcen und auch nur dann, wenn Sie sie verwenden. Ausführliche Informationen finden Sie unter [Compute- und Speicheroptionen in Azure Database for MySQL: Flexible Server](flexible-server/concepts-compute-storage.md).

Flexible Server-Instanzen eignen sich am besten für Folgendes:
- Einfache Bereitstellungen, vereinfachte Skalierung und geringer Aufwand für die Datenbankverwaltung bei Funktionen wie Sicherungen, Hochverfügbarkeit, Sicherheit und Überwachung
- Anwendungsentwicklungen, die eine Communityversion von MySQL mit besseren Steuerungs- und Anpassungsmöglichkeiten erfordern
- Produktionsworkloads mit Hochverfügbarkeit in der gleichen Zone, mit zonenredundanter Hochverfügbarkeit und mit verwalteten Wartungsfenstern
- Vereinfachte Entwicklungsumgebung 
- Sicherheit auf Unternehmensniveau

Eine detaillierte Übersicht über den Flexible Server-Bereitstellungsmodus finden Sie unter [Übersicht über Flexible Server](flexible-server/overview.md). Neueste Updates für Flexible Server finden Sie unter [Neuerungen in Azure Database for MySQL Flexible Server](flexible-server/whats-new.md).

### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL Single Server 

Azure Database for MySQL Single Server ist ein vollständig verwalteter Datenbankdienst, der auf minimale Anpassungen ausgelegt ist. Die Single Server-Plattform kann die meisten Funktionen zur Datenbankverwaltung bereitstellen, z. B. Patching, Sicherungen, Hochverfügbarkeit sowie Sicherheit mit minimaler Benutzerkonfiguration und -steuerung. Die Architektur ist für integrierte Hochverfügbarkeit mit einer Verfügbarkeit von 99,99 % bei einer einzelnen Verfügbarkeitszone optimiert. Sie unterstützt die MySQL Community-Versionen 5.6 (eingestellt), 5.7 und 8.0. Der Dienst ist ab heute in vielen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/) allgemein verfügbar.

Single Server-Instanzen eignen sich am besten **nur für vorhandene Anwendungen, die bereits Einzelserver nutzen**. Für alle neuen Entwicklungen oder Migrationen ist Flexible Server die empfohlene Bereitstellungsoption. Informationen zu den Unterschieden zwischen den Bereitstellungsoptionen für Flexible Server und Single Server finden Sie unter [Auswählen der richtigen MySQL Server-Option in Azure](select-right-deployment-type.md).

Eine detaillierte Übersicht über den Single Server-Bereitstellungsmodus finden Sie unter [Übersicht über Single Server](single-server-overview.md). Neueste Updates für Flexible Server finden Sie unter [Neuerungen in Azure Database for MySQL Single Server](single-server-whats-new.md).

## <a name="contacts"></a>Kontakte
Sollten Sie Fragen oder Vorschläge im Zusammenhang mit der Verwendung von Azure Database for MySQL haben, senden Sie eine E-Mail an das zuständige Team ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Bei dieser E-Mail-Adresse handelt es sich nicht um einen Alias für den technischen Support.

Weitere Kontaktmöglichkeiten:

- Wenn Sie den Azure-Support kontaktieren möchten, [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die beiden Bereitstellungsmodi für Azure Database for MySQL, und wählen Sie die idealen Optionen für Ihre Anforderungen aus.

- [Single Server](single-server/index.yml)
- [Flexible Server](flexible-server/index.yml)
- [Auswählen der richtigen MySQL-Bereitstellungsoption für ihre Workload](select-right-deployment-type.md)
