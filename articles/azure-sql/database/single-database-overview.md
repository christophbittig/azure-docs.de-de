---
title: Was ist eine Einzeldatenbank?
description: Erfahren Sie mehr über die Einzeldatenbank als Ressourcentyp in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fb607461e446ee44a92cee8e6dff60e8c2e6dd45
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546487"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Was ist eine Einzeldatenbank in Azure SQL-Datenbank?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Der Ressourcentyp für Einzeldatenbanken erstellt eine Datenbank in Azure SQL-Datenbank mit einem eigenen Satz von Ressourcen und wird über einen [Server](logical-servers.md) verwaltet. Bei einer einzelnen Datenbank ist jede Datenbank isoliert und portabel. Jede verfügt über eine eigene Dienstebene innerhalb des [DTU-basierten Kaufmodells](service-tiers-dtu.md) oder [vCore-basierten Kaufmodells](service-tiers-vcore.md) und eine garantierte Computegröße.

„Einzeldatenbank“ ist ein Bereitstellungsmodell für Azure SQL-Datenbank. Der andere Typ ist [Pools für elastische Datenbanken](elastic-pool-overview.md).

> [!div class="nextstepaction"]
> [Umfrage zur Verbesserung von Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)


## <a name="dynamic-scalability"></a>Dynamische Skalierbarkeit

Sie können Ihre erste App auf einem kleinen Singleton kostengünstig im serverlosen Computetarif oder einer kleinen Computegröße im bereitgestellten Computetarif erstellen. Die [Compute- oder Dienstebene](single-database-scale.md) können Sie jederzeit manuell oder programmgesteuert ändern, um die Anforderungen Ihrer Lösung zu erfüllen. Die Leistungsanpassung ist möglich, ohne dass es für die App oder für Ihre Kunden zu Ausfallzeiten kommt. Dank der dynamischen Skalierbarkeit kann Ihre Datenbank in transparenter Form auf sich schnell ändernde Ressourcenanforderungen reagieren, und Sie zahlen nur für die Ressourcen, die Sie jeweils benötigen.

## <a name="single-databases-and-elastic-pools"></a>Einzeldatenbanken und Pools für elastische Datenbanken

Eine Einzeldatenbank kann in einen oder aus einem [Pool für elastische Datenbanken](elastic-pool-overview.md) für die gemeinsame Nutzung von Ressourcen verschoben werden. Für viele Unternehmen und Anwendungen genügt es, wenn Einzeldatenbanken erstellt werden können und sich die Leistung nach oben oder unten anpassen lässt – insbesondere, wenn die Nutzungsmuster relativ gut vorhersagbar sind. Bei unvorhersagbaren Nutzungsmustern kann es jedoch schwer sein, die Kosten und Ihr Geschäftsmodell zu verwalten. Pools für elastische Datenbanken sind darauf ausgelegt, dieses Problem zu beheben. Das Konzept ist denkbar einfach. Sie ordnen Leistungsressourcen nicht einer Einzeldatenbank, sondern einem Pool zu, sodass Sie für die gesamten Leistungsressourcen des Pools und nicht für die Leistung einer Einzeldatenbank bezahlen.

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung

Verwenden Sie die [integrierten Features für die Leistungsüberwachung](performance-guidance.md) sowie [Warnungstools](alerts-insights-configure-portal.md) in Kombination mit den Leistungsbewertungen. Mit diesen Tools können Sie die Auswirkungen des zentralen Hoch- oder Herunterskalierens je nach Ihren derzeitigen bzw. projektbezogenen Leistungsanforderungen schnell bewerten. Darüber hinaus kann SQL-Datenbank zur einfacheren Überwachung [Metriken und Ressourcenprotokolle ausgeben](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="availability-capabilities"></a>Verfügbarkeitsfunktionen

Einzeldatenbanken und Pools für elastische Datenbanken bieten zahlreiche Verfügbarkeitsmerkmale. Weitere Informationen finden Sie unter [Verfügbarkeitsmerkmale](sql-database-paas-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Transact-SQL-Unterschiede

Die meisten Transact-SQL-Funktionen, die von Anwendungen verwendet werden, werden in Microsoft SQL Server und in der Azure SQL-Datenbank unterstützt. Die zentralen SQL-Komponenten wie z.B. Datentypen, Operatoren, Zeichenfolgen-, Arithmetik-, logische und Cursorfunktionen funktionieren in SQL Server und in der SQL-Datenbank gleich. Es gibt jedoch einige T-SQL-Unterschiede zwischen Elementen der Datendefinitionssprache (Data Definition Language, DDL) und der Datenbearbeitungssprache (Data Manipulation Language, DML), die dazu führen, dass T-SQL-Anweisungen und -Abfragen nur teilweise unterstützt werden (mehr dazu weiter unten in diesem Artikel).

Darüber hinaus werden einige Features und Syntax nicht unterstützt, da Azure SQL-Datenbank entwickelt wurde, um Features von Abhängigkeiten von Masterdatenbank und Betriebssystem zu isolieren. An sich sind die meisten Aktivitäten auf Serverebene für die SQL-Datenbank nicht geeignet. T-SQL-Anweisungen und -Optionen sind nicht verfügbar, wenn sie Optionen auf Serverebene, Betriebssystemkomponenten oder bestimmte Dateisystemkonfigurationen konfigurieren. Wenn solche Funktionen erforderlich sind, dann ist häufig eine andere geeignete Alternative von der SQL-Datenbank oder aus einem anderen Azure-Feature oder -Dienst verfügbar.

Weitere Informationen finden Sie unter [Auflösen von Transact-SQL-Unterschieden während der Migration zur SQL-Datenbank](transact-sql-tsql-differences-sql-server.md).

## <a name="security"></a>Sicherheit

SQL-Datenbank bietet eine Reihe von [integrierten Sicherheits- und Konformitätsfeatures](security-overview.md), mit der Sie Ihre Anwendung an verschiedene Sicherheits- und Konformitätsanforderungen anpassen können.

> [!IMPORTANT]
> Azure SQL-Datenbank wurde anhand einer Reihe von Konformitätsstandards zertifiziert. Weitere Informationen finden Sie im [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), wo die aktuelle Liste von Konformitätszertifizierungen für Azure SQL-Datenbank angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

- Um schnell mit einer Einzeldatenbank zu beginnen, verwenden Sie die [Schnellstartanleitung für Einzeldatenbanken](quickstart-content-reference-guide.md).
- Weitere Informationen zum Migrieren einer SQL Server-Datenbank zu Azure finden Sie unter [Migrieren zu Azure SQL-Datenbank](migrate-to-database-from-sql-server.md).
- Informationen zu unterstützten Funktionen finden Sie unter [Features (Funktionen)](features-comparison.md).
