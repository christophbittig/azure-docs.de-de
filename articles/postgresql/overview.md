---
title: Was ist Azure Database for PostgreSQL?
description: Bietet eine Übersicht über den relationalen Datenbankdienst Azure Database for PostgreSQL im Kontext von Flexible Server.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 3d80aba60c4e3662e0a1b3d5d8ed8f2ab6c135a2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725255"
---
# <a name="what-is-azure-database-for-postgresql"></a>Was ist Azure Database for PostgreSQL?

Azure Database for PostgreSQL ist ein relationaler Datenbankdienst in der Microsoft-Cloud, der auf der Datenbank-Engine [PostgreSQL Community Edition](https://www.postgresql.org/) (verfügbar unter der GPLv2-Lizenz) basiert. Azure-Datenbank für PostgreSQL bietet Folgendes:

- Integrierte Hochverfügbarkeit
- Schutz von Daten durch automatische Sicherungen und Point-in-Time-Wiederherstellungen für bis zu 35 Tage.
- Automatisierte Wartung für die zugrunde liegende Hardware, das Betriebssystem und die Datenbank-Engine, um die Sicherheit des Diensts zu gewährleisten und sicherzustellen, dass er stets auf dem aktuellen Stand ist.
- Vorhersagbare Leistung inklusive nutzungsbasierter Bezahlung.
- Elastische Skalierung innerhalb weniger Sekunden.
- Sicherheit auf Unternehmensniveau und branchenführende Compliance, um sowohl ruhende vertrauliche Daten als auch Daten während der Übertragung zu schützen.
- Überwachung und Automatisierung, um die Verwaltung und Überwachung für umfangreiche Bereitstellungen zu vereinfachen.
- Branchenführender Support.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure-Datenbank für PostgreSQL":::

Diese Funktionen erfordern fast keine Administration und werden alle ohne zusätzliche Kosten bereitgestellt. Mit diesen Funktionen können Sie sich auf die schnelle Entwicklung von Anwendungen und die Verkürzung des Zeitraums bis zur Markteinführung konzentrieren, anstatt wertvolle Zeit und Ressourcen für die Verwaltung von virtuellen Computern und der Infrastruktur aufwenden zu müssen. Darüber hinaus können Sie Ihre Anwendung weiterhin mit den Open-Source-Tools und der Plattform Ihrer Wahl entwickeln und mit der Geschwindigkeit und Effizienz liefern, die Ihr Unternehmen verlangt, ohne dass Sie sich dafür neue Fähigkeiten aneignen müssen.

## <a name="deployment-models"></a>Bereitstellungsmodelle

Azure Database for PostgreSQL auf Grundlage der PostgreSQL Community Edition ist in drei Bereitstellungsmodi verfügbar:

- Einzelner Server
- Flexible Server (Vorschau)
- Hyperscale (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (Einzelserver)

Bei Azure Database for PostgreSQL Single Server handelt es sich um einen vollständig verwalteten Datenbankdienst mit minimalen Anforderungen für die Anpassung der Datenbank. Die Single Server-Plattform kann die meisten Funktionen zur Datenbankverwaltung bereitstellen, z. B. Patching, Sicherungen, Hochverfügbarkeit sowie Sicherheit mit minimaler Benutzerkonfiguration und -steuerung. Die Architektur ist für integrierte Hochverfügbarkeit mit einer Verfügbarkeit von 99,99 % bei einer einzelnen Verfügbarkeitszone optimiert. Sie unterstützt die Community-Versionen 9.5, 9,6, 10 und 11 von PostgreSQL. Der Dienst ist ab heute in vielen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/) allgemein verfügbar.

Für die Bereitstellungsoption „Einzelserver“ stehen drei Tarife zur Verfügung: „Basic“, „Allgemein“ und „Arbeitsspeicheroptimiert“. Jeder Tarif bietet unterschiedliche Ressourcenfunktionen für Ihre Datenbankworkloads. Sie können Ihre erste App mit einer kleinen Datenbank für wenige USD im Monat erstellen und die Skalierung dann so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Durch die dynamische Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie bezahlen nur für die benötigten Ressourcen und auch nur dann, wenn Sie sie verwenden. Einzelheiten hierzu finden Sie unter [Tarife](./concepts-pricing-tiers.md).

Single Server-Implementierungen eignen sich am besten für cloudnative Anwendungen, da sie für das automatisierte Patchen ohne genauen Patchzeitplan und ohne benutzerdefinierte PostgreSQL-Konfigurationseinstellungen konzipiert sind.

Eine detaillierte Übersicht über den Single Server-Bereitstellungsmodus finden Sie unter [Übersicht über Single Server](./overview-single-server.md).

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL – Flexible Server (Vorschau)

Azure Database for PostgreSQL Flexible Server ist ein vollständig verwalteter Datenbankdienst, der eine differenziertere Steuerung und mehr Flexibilität bei den Verwaltungsfunktionen und Konfigurationseinstellungen der Datenbank bietet. Durch die höhere Flexibilität und umfassenderen Anpassungsmöglichkeiten lassen sich mit diesem Dienst unterschiedliche Benutzeranforderungen erfüllen. Mit der Flexible Server-Architektur können Benutzer Hochverfügbarkeit in einer einzelnen Verfügbarkeitszone sowie in mehreren Verfügbarkeitszonen auswählen. Flexible Server-Implementierungen bieten eine bessere Kostenoptimierung mit der Möglichkeit, den Server anzuhalten/zu starten, sowie einen burstfähigen Computetarif, der sich ideal für Workloads eignet, die nicht dauerhaft die volle Computekapazität benötigen. Der Dienst unterstützt derzeit die Community-Versionen 11 und 12 von PostgreSQL. Die Unterstützung weiterer Versionen ist für die nahe Zukunft geplant. Aktuell befindet sich der Dienst in der Vorschauphase, und er ist in einer Vielzahl von Azure-Regionen verfügbar.

Flexible Server-Implementierungen eignen sich am besten für folgende Szenarien:

- Anwendungsentwicklungen, die bessere Steuerungs- und Anpassungsmöglichkeiten erfordern.
- Kostenoptimierungsoptionen mit der Möglichkeit, Server anzuhalten/starten.
- Zonenredundante Hochverfügbarkeit
- Verwaltete Wartungsfenster
  
Eine detaillierte Übersicht über den Flexible Server-Bereitstellungsmodus finden Sie unter [Übersicht über Flexible Server](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – Hyperscale (Citus)

Bei der Option „Hyperscale (Citus)“ werden Abfragen mithilfe von Sharding horizontal über mehrere Computer hinweg skaliert. Die Abfrage-Engine parallelisiert eingehende SQL-Abfragen auf diesen Servern, um die Antwortzeiten bei großen Datasets zu verkürzen. Sie eignet sich für Anwendungen, die eine größere Skalierbarkeit und höhere Leistung erfordern, und wird im Allgemeinen für Workloads mit bis zu 100 GB an Daten (oder auch mehr) eingesetzt.

Die Bereitstellungsoption „Hyperscale (Citus)“ bietet Folgendes:

- Horizontale Skalierung auf mehreren Computern mithilfe von Sharding
- Parallelisierung von Abfragen auf den Servern für kürzere Antwortzeiten bei großen Datasets
- Hervorragende Unterstützung für mehrinstanzenfähige Anwendungen, operative Echtzeitanalyse und Transaktionsworkloads mit hohem Durchsatz
  
Bei der Option „Hyperscale (Citus)“ können für PostgreSQL erstellte Anwendungen verteilte Abfragen mit [Standardverbindungsbibliotheken](./concepts-connection-libraries.md) und minimalen Änderungen ausführen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die drei Bereitstellungsmodi für Azure Database for PostgreSQL, und wählen Sie die idealen Optionen für Ihre Anforderungen aus.

- [Single Server](./overview-single-server.md)
- [Flexible Server](./flexible-server/overview.md)
- [Hyperscale (Citus)](./hyperscale-overview.md)
