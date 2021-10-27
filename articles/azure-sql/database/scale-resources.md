---
title: Skalieren von Ressourcen
description: In diesem Artikel erfahren Sie, wie Sie Ihre Datenbank in Azure SQL-Datenbank und SQL Managed Instance durch Hinzufügen oder Entfernen von zugeordneten Ressourcen skalieren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma, urmilano, wiassaf
ms.date: 06/25/2019
ms.openlocfilehash: d6dd794a9eb0a7af1ed2e91a04c27d5321e14ca3
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179334"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamisches Skalieren von Datenbankressourcen bei minimaler Downtime
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Mit Azure SQL-Datenbank und SQL Managed Instance können Sie mit minimaler [Downtime](https://azure.microsoft.com/support/legal/sla/azure-sql-database) zusätzliche Ressourcen dynamisch zu Ihrer Datenbank hinzufügen. Allerdings wird die Verbindung mit der Datenbank während einer Umschaltzeit kurzzeitig unterbrochen, was jedoch durch Wiederholungslogik abgeschwächt werden kann.

## <a name="overview"></a>Übersicht

Wenn die Nachfrage nach Ihrer App von einigen wenigen Geräten und Kunden bis in den Millionenbereich zunimmt, können Azure SQL-Datenbank und SQL Managed Instance im laufenden Betrieb mit minimaler Downtime skaliert werden. Die Skalierbarkeit ist eines der wichtigsten Merkmale von PaaS (Platform-as-a-Service), mit dem Sie Ihrem Dienst bei Bedarf dynamisch weitere Ressourcen hinzufügen können. Mit Azure SQL-Datenbank können Sie Ressourcen (CPU-Leistung, Arbeitsspeicher, E/A-Durchsatz und Speicher), die Ihren Datenbanken zugeordnet sind, leicht ändern.

Sie können Leistungsprobleme beheben, die aufgrund der vermehrten Nutzung Ihrer Anwendung auftreten und sich per Indizierung oder mit Methoden zum Umschreiben von Abfragen nicht beseitigen lassen. Durch das Hinzufügen von weiteren Ressourcen können Sie schnell reagieren, wenn Ihre Datenbank die derzeitigen Ressourcenlimits erreicht und eine höhere Leistung benötigt, um die eingehende Workload verarbeiten zu können. Mit Azure SQL-Datenbank können Sie die Ressourcen auch zentral herunterskalieren, wenn sie nicht benötigt werden, um die Kosten zu senken.

Sie müssen sich nicht mit dem Kaufen von Hardware und Ändern der zugrunde liegenden Infrastruktur befassen. Sie können eine Datenbank einfach im Azure-Portal über einen Schieberegler skalieren.

![Skalieren der Datenbankleistung](./media/scale-resources/scale-performance.svg)

Azure SQL-Datenbank bietet das [DTU-basierte Kaufmodell](service-tiers-dtu.md) und das [vCore-basierte Kaufmodell](service-tiers-vcore.md), während Azure SQL Managed Instance nur das [vCore-basierte Kaufmodell](service-tiers-vcore.md) bietet. 

- Das [DTU-basierte Kaufmodell](service-tiers-dtu.md) bietet zur Unterstützung von einfachen bis hin zu komplexen Datenbankworkloads eine Mischung aus Compute-, Arbeitsspeicher- und E/A-Ressourcen auf drei Dienstebenen: Basic, Standard und Premium. Leistungsstufen auf den einzelnen Ebenen bieten unterschiedliche Ressourcenzusammenstellungen, durch zusätzliche Speicherressourcen ergänzt werden können.
- Beim [vCore-basierten Kaufmodell](service-tiers-vcore.md) können Sie die Anzahl virtueller Kerne, die Arbeitsspeichermenge sowie Menge und Geschwindigkeit des Speichers auswählen. Dieses Kaufmodell bietet drei Dienstebenen: „Universell“, „Unternehmenskritisch“ und „Hyperscale“.

Die Grenzwerte für Dienstebene, Computeebene und Ressourcen für eine Datenbank, einen Pool für elastische Datenbanken oder eine verwaltete Instanz können jederzeit geändert werden. Sie können beispielsweise Ihre erste App in einer Einzeldatenbank erstellen und dazu die serverlose Computeebene verwenden, und dann manuell oder programmgesteuert jederzeit die Dienstebene (Tarif) in die bereitgestellte Computeebene ändern, um die Anforderungen Ihrer Lösung zu erfüllen.

> [!NOTE]
> Es gibt jedoch auch wichtige Ausnahmen, bei denen Sie die Dienstebene einer Datenbank nicht ändern können:
> - Datenbanken auf der Dienstebene „Hyperscale“ können derzeit nicht in eine andere Dienstebene geändert werden.
> - Datenbanken, die Features verwenden, die [nur in den Dienstebenen „Unternehmenskritisch/Premium“ verfügbar](features-comparison.md#features-of-sql-database-and-sql-managed-instance) sind, können nicht geändert werden, um die Dienstebene „Universell/Standard“ zu verwenden.

Sie können die Ihrer Datenbank zugeordneten Ressourcen anpassen, indem Sie das Dienstziel ändern oder skalieren, um Workloadanforderungen zu erfüllen. Durch diese Möglichkeit brauchen Sie nur die Ressourcen zu bezahlen, die Sie benötigen, und wenn Sie sie benötigen. Beachten Sie den [Hinweis](#impact-of-scale-up-or-scale-down-operations) zu den potenziellen Auswirkungen, die ein Skalierungsvorgang auf eine Anwendung haben kann.

> [!NOTE]
> Dynamische Skalierbarkeit ist nicht dasselbe wie automatische Skalierung. Bei der automatischen Skalierung wird ein Dienst automatisch auf der Grundlage von Kriterien skaliert. Die dynamische Skalierbarkeit ermöglicht dagegen eine manuelle Skalierung mit minimaler Downtime. Einzeldatenbanken in Azure SQL-Datenbank können manuell skaliert oder bei der [Serverlosen Ebene](serverless-tier-overview.md) so festgelegt werden, dass die Computeressourcen automatisch skaliert werden. [Pools für elastische Datenbanken](elastic-pool-overview.md), mit denen Datenbanken Ressourcen in einem Pool gemeinsam nutzen können, können derzeit nur manuell skaliert werden.

Azure SQL-Datenbank ermöglicht ein dynamisches Skalieren Ihrer Datenbanken:

- In einer [Einzeldatenbank](single-database-scale.md) können Sie entweder [DTU](resource-limits-dtu-single-databases.md)- oder [V-Kern](resource-limits-vcore-single-databases.md)-Modelle nutzen, um die maximale Menge von Ressourcen zu definieren, die den einzelnen Datenbanken zugewiesen werden.
- Bei [Pools für elastische Datenbanken](elastic-pool-scale.md) können Sie das maximale Ressourcenlimit pro Datenbankgruppe im Pool definieren.

In Azure SQL Managed Instance ist ebenfalls ein Skalieren möglich: 

- [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) verwendet den Modus [Virtuelle Kerne](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) und ermöglicht Ihnen das Definieren der maximalen Anzahl von CPU-Kernen und des maximalen Speichers für Ihre Instanz. Alle Datenbanken innerhalb der verwalteten Instanz nutzen die der Instanz zugeordneten Ressourcen gemeinsam.

## <a name="impact-of-scale-up-or-scale-down-operations"></a>Auswirkungen von Skalierungsvorgängen (Hoch- bzw. Herunterskalieren)

Wenn Sie bei einer der vorgenannten Varianten eine Aktion zum Hoch- oder Herunterskalieren initiieren, wird der Datenbank-Engine-Prozess neu gestartet und bei Bedarf auf einen anderen virtuellen Computer verschoben. Das Verschieben des Datenbank-Engine-Prozesses auf einen neuen virtuellen Computer ist ein **Onlineprozess**, während dem Sie den vorhandenen Azure SQL-Datenbankdienst weiterhin verwenden können. Sobald die Zieldatenbank-Engine für die Verarbeitung von Abfragen bereit ist, werden geöffnete Verbindungen zur aktuellen Datenbank-Engine [beendet](single-database-scale.md#impact), und für Transaktionen ohne ausgeführten Commit erfolgt ein Rollback. Es werden neue Verbindungen zur Zieldatenbank-Engine hergestellt.

> [!NOTE]
> Es wird davon abgeraten, dass Sie Ihre verwaltete Instanz skalieren, wenn eine Transaktion mit langer Laufzeit – z. B. ein Datenimport, Datenverarbeitungsaufträge, eine Indexneuerstellung usw. – ausgeführt wird oder wenn es eine aktive Verbindung mit der Instanz gibt. Wenn Sie verhindern möchten, dass die Skalierung länger als üblich dauert, sollten Sie die Instanz nach Abschluss aller Vorgänge mit langer Laufzeit skalieren.

> [!NOTE]
> Sie müssen mit einer kurzen Unterbrechung der Verbindung rechnen, wenn das Hoch-/Herunterskalieren abgeschlossen ist. Wenn Sie [Wiederholungslogik bei vorübergehenden Standardfehlern](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) implementiert haben, bemerken Sie den Failover nicht.

## <a name="alternative-scale-methods"></a>Alternative Skalierungsmethoden

Die Skalierung von Ressourcen ist die einfachste und effektivste Möglichkeit, die Leistung Ihrer Datenbank zu verbessern, ohne die Datenbank oder den Anwendungscode zu ändern. Manchmal kann es vorkommen, dass auch die höchsten Dienstebenen, Computegrößen und Leistungsoptimierungen nicht zu einer erfolgreichen und kostengünstigen Verarbeitung Ihrer Workload führen. In diesen Fällen haben Sie weitere Möglichkeiten zum Skalieren Ihrer Datenbank:

- Die [horizontale Leseskalierung](read-scale-out.md) ist ein verfügbares Feature, bei dem Sie ein schreibgeschütztes Replikat Ihrer Daten erhalten, über das Sie anspruchsvolle schreibgeschützte Abfragen (z. B. Berichte) ausführen können. Mit einem schreibgeschützten Replikat wird Ihre schreibgeschützte Workload verarbeitet, ohne dass sich Auswirkungen auf die Ressourcenverwendung in Ihrer primären Datenbank ergeben.
- Das [Datenbank-Sharding](elastic-scale-introduction.md) umfasst eine Reihe von Verfahren, mit denen Sie Ihre Daten in mehrere Datenbanken aufteilen und unabhängig voneinander skalieren können.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verbesserung der Datenbankleistung durch das Ändern des Datenbankcodes finden Sie unter [Suchen und Anwenden von Empfehlungen zur Leistung](database-advisor-find-recommendations-portal.md).
- Informationen zur Optimierung Ihrer Datenbank mit integrierter Datenbank-Intelligence finden Sie unter [Automatische Optimierung](automatic-tuning-overview.md).
- Informationen zur horizontalen Leseskalierung in Azure SQL-Datenbank finden Sie unter [Verwenden von schreibgeschützten Replikaten für den Lastenausgleich schreibgeschützter Abfrageworkloads](read-scale-out.md).
- Informationen zum Datenbank-Sharding finden Sie unter [Horizontales Hochskalieren mit Azure SQL-Datenbank](elastic-scale-introduction.md).
- Ein Beispiel für die Verwendung von Skripts zum Überwachen und Skalieren einer Einzeldatenbank finden Sie unter [Verwenden von PowerShell zum Überwachen und Skalieren einer einzelnen SQL-Datenbank](scripts/monitor-and-scale-database-powershell.md).
