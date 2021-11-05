---
title: Microsoft Defender für SQL - die Vorteile und Funktionen
description: Erfahren Sie mehr über die Vorteile und Funktionen von Microsoft Defender für SQL.
author: memildin
ms.author: memildin
ms.date: 05/27/2021
ms.topic: overview
ms.service: security-center
ms.custom: references_regions, ignite-fall-2021
manager: rkarlin
ms.openlocfilehash: 3f2694cbde7148b3a90895e83f2f209c4a553ccf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061065"
---
# <a name="introduction-to-microsoft-defender-for-sql"></a>Einführung in Microsoft Defender für SQL

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für SQL enthält zwei Microsoft Defender-Pläne, die das Datensicherheitspaket [ von Microsoft Defender für Cloud](../azure-sql/database/azure-defender-for-sql.md) erweitern, um Ihre Datenbanken und deren Daten zu schützen, egal wo sie sich befinden. Microsoft Defender für SQL enthält Funktionen zum Erkennen und Entschärfen potenzieller Datenbankschwachstellen und zum Erkennen anomaler Aktivitäten, die auf eine Bedrohung Ihrer Datenbanken hindeuten könnten.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|**Microsoft Defender für Azure SQL-Datenbankserver** - Generell verfügbar (GA)<br>**Microsoft Defender für SQL-Server auf Computern** - Generell verfügbar (GA) |
|Preise:|Die beiden Pläne, die **Microsoft Defender für SQL** bilden, werden wie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/) angegeben berechnet.|
|Geschützte SQL-Versionen:|[SQL auf virtuellen Azure-Computern](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[SQL Server-Instanzen mit Azure Arc-Unterstützung](/sql/sql-server/azure-arc/overview)<br>Lokale SQL Server-Instanzen auf Windows-Computern ohne Azure Arc<br>Azure SQL-[Singletons](../azure-sql/database/single-database-overview.md) und [Pool für elastische Datenbanken](../azure-sql/database/elastic-pool-overview.md)<br>[Verwaltete Azure SQL-Datenbank-Instanz](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics (früher SQL DW) und dedizierter SQL-Pool](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet (**Teilweise:** Teilmenge von Warnungen und Sicherheitsrisikobewertungen für SQL-Server. Verhaltensbedingte Bedrohungsschutzmaßnahmen sind nicht verfügbar.)|
|||

## <a name="what-does-microsoft-defender-for-sql-protect"></a>Was schützt Microsoft Defender für SQL?

**Microsoft Defender für SQL** umfasst zwei separate Microsoft Defender-Pläne:

- **Microsoft Defender für Azure SQL-Datenbankserver** schützt:
    - [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md)
    - [Verwaltete Azure SQL-Datenbank-Instanz](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Dedizierter SQL-Pool in Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Microsoft Defender für SQL-Server auf Rechnern** erweitert den Schutz für Ihre Azure-nativen SQL-Server, um hybride Umgebungen vollständig zu unterstützen und SQL-Server (alle unterstützten Versionen) zu schützen, die in Azure, anderen Cloud-Umgebungen und sogar auf Rechnern vor Ort gehostet werden:
    - [SQL Server auf virtuellen Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Lokale SQL Server-Instanzen:
        - [Azure Arc-fähige SQL-Server-Instanz (Vorschauversion)](/sql/sql-server/azure-arc/overview)
        - [Auf Windows-Computern ohne Azure Arc ausgeführte SQL Server-Instanzen](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-microsoft-defender-for-sql"></a>Was sind die Vorteile von Microsoft Defender für SQL?

Diese beiden Pläne verfügen über Funktionen zur Ermittlung und Verringerung potenzieller Datenbankschwachstellen und Erkennung ungewöhnlicher Aktivitäten, die Bedrohungen für Ihre Datenbanken darstellen können.

Ein Dienst zur Sicherheitsrisikobewertung ermittelt, verfolgt und unterstützt Sie beim Beheben potenzieller Sicherheitsrisiken für Ihre Datenbanken. Bewertungsscans bieten einen Überblick über den Sicherheitszustand Ihrer SQL-Computer und Einzelheiten zu allen Sicherheitsergebnissen.

- Erfahren Sie mehr über die [Sicherheitsrisikobewertung für Azure SQL-Datenbank](../azure-sql/database/sql-vulnerability-assessment.md).
- Erfahren Sie mehr über die [Sicherheitsrisikobewertung für Azure SQL-Server auf Computern](defender-for-sql-on-machines-vulnerability-assessment.md).

Ein Advanced Threat Protection-Dienst überwacht Ihre SQL Server-Instanzen kontinuierlich auf Bedrohungen wie die Einschleusung von SQL-Befehlen, Brute-Force-Angriffe und Berechtigungsmissbrauch. Dieser Dienst liefert handlungsorientierte Sicherheitswarnungen in Microsoft Defender für Cloud mit Details zu den verdächtigen Aktivitäten, Anleitungen zur Eindämmung der Bedrohungen und Optionen zur Fortsetzung Ihrer Untersuchungen mit Microsoft Sentinel. Erfahren Sie mehr über [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md).

 > [!TIP]
 > Zeigen Sie die Liste mit den Sicherheitswarnungen für SQL-Server [auf der Referenzseite für Warnungen](alerts-reference.md#alerts-sql-db-and-warehouse) an.


## <a name="is-there-a-performance-impact-from-deploying-microsoft-defender-for-sql-on-machines"></a>Wirkt sich die Bereitstellung von Microsoft Defender für SQL auf die Leistung von Computern aus?

Der Schwerpunkt von **Microsoft Defender für SQL auf Rechnern** liegt eindeutig auf der Sicherheit. Aber wir kümmern uns auch um Ihr Geschäft und haben daher die Leistung priorisiert, um zu gewährleisten, dass die Auswirkungen auf Ihre SQL-Server minimal sind. 

Der Dienst verfügt über eine zweigeteilte Architektur, um ein ausgewogenes Verhältnis zwischen Datenupload und Geschwindigkeit zu erreichen: 

- Einige unserer Erkennungsmodule werden auf dem Computer ausgeführt, um Geschwindigkeitsvorteile in Echtzeit zu erreichen.
- Andere werden in der Cloud ausgeführt, um den Computer vor hohen Rechenlasten zu schützen.

Labortests unserer Lösung, bei denen sie mit Benchmarklasten verglichen wurde, ergaben eine CPU-Auslastung von durchschnittlich 3 % für Spitzen-Slices. Eine Analyse der Telemetriedaten für unsere aktuellen Benutzer zeigt eine vernachlässigbare Auswirkung auf die CPU- und Arbeitsspeicherauslastung.

Natürlich kann die Leistung verschiedener Umgebungen, Computer und Lasten immer variieren. Die obigen Aussagen und Zahlen dienen als allgemeine Richtlinie, nicht als Garantie für einzelne Bereitstellungen.


## <a name="what-kind-of-alerts-does-microsoft-defender-for-sql-provide"></a>Welche Art von Warnmeldungen bietet Microsoft Defender für SQL?

Erweiterte Threat Intelligence-Sicherheitswarnungen werden ausgelöst, wenn Folgendes vorliegt:

- **Potenzielle Angriffe durch Einschleusung von SQL-Befehlen**: Hierzu gehören auch Sicherheitsrisiken, die erkannt werden, wenn von Anwendungen in der Datenbank eine fehlerhafte SQL-Anweisung generiert wird.
- **Anomale Muster für Datenbankzugriff und -abfrage**: Ein Beispiel hierfür sind eine ungewöhnlich hohe Anzahl von fehlgeschlagenen Anmeldeversuchen mit unterschiedlichen Anmeldeinformationen (Brute-Force-Angriff).
- **Verdächtige Datenbankaktivitäten**, z. B. berechtigter Benutzer, der über einen Computer mit Sicherheitsverletzung, der mit einem C&C-Server zum Kryptografiemining kommuniziert, auf eine SQL Server-Instanz zugreift.

Warnungen enthalten Details zum Incident, durch den sie ausgelöst wurden, sowie Empfehlungen zur Untersuchung und Eindämmung von Bedrohungen.



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über Microsoft Defender für SQL erfahren. Gehen Sie wie folgt vor, um die beschriebenen Dienste zu nutzen:

- Verwenden Sie Microsoft Defender für SQL-Server auf Rechnern, um [Ihre SQL-Server auf Schwachstellen zu scannen](defender-for-sql-usage.md)
- Eine Präsentation von Microsoft Defender für SQL finden Sie unter [wie Microsoft Defender für SQL SQL-Server überall schützen kann](https://www.youtube.com/watch?v=V7RdB6RSVpc)
