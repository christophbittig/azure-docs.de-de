---
title: 'Microsoft Defender für relationale Open-Source-Datenbanken: Vorteile und Features'
description: Erfahren Sie mehr über die Vorteile und Features von Microsoft Defender für relationale Open-Source-Datenbanken wie PostgreSQL, MySQL und MariaDB.
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: 200aa7e3cbc4b65b4671c3ce41a8bbbc8aa31394
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526723"
---
# <a name="introduction-to-microsoft-defender-for-open-source-relational-databases"></a>Einführung in Microsoft Defender für relationale Open-Source-Datenbanken

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieser Plan bietet folgenden relationalen Open-Source-Datenbanken Schutz gegen Bedrohungen:

- [Azure-Datenbank für PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Defender für Cloud erkennt anomale Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese missbräuchlich zu nutzen. Mit diesem Plan kann problemlos auf potenzielle Bedrohungen für die Datenbank reagiert werden, ohne das Fachwissen eines Sicherheitsexperten besitzen oder komplexe Sicherheitsüberwachungssysteme verwalten zu müssen.

## <a name="availability"></a>Verfügbarkeit

| Aspekt                             | Details                                                                                                                                    |
|------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| Status des Release:                     | Allgemeine Verfügbarkeit (General Availability, GA)                                                     |
| Preise:                           | **Microsoft Defender für relationale Open-Source-Datenbanken** wird gemäß den auf der Seite [Preise](https://azure.microsoft.com/pricing/details/security-center/) aufgeführten Preisen abgerechnet.   |
| Geschützte Versionen von PostgreSQL:  | Einzelserver: „Universell“ und „Arbeitsspeicheroptimiert“. Weitere Informationen finden Sie in der Übersicht über die [PostgreSQL-Tarife](../postgresql/concepts-pricing-tiers.md).   |
| Geschützte Versionen von MySQL:       | Einzelserver: „Universell“ und „Arbeitsspeicheroptimiert“. Weitere Informationen finden Sie in der Übersicht über die [MySQL-Tarife](../mysql/concepts-pricing-tiers.md).                        |
| Geschützte Versionen von MariaDB:     | „Universell“ und „Arbeitsspeicheroptimiert“. Weitere Informationen finden Sie in der Übersicht über die [MariaDB-Tarife](../mariadb/concepts-pricing-tiers.md).                      |
| Clouds:                            | :::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet) |
|                                    |                                                                                                                                            |

## <a name="what-are-the-benefits-of-microsoft-defender-for-open-source-relational-databases"></a>Welche Vorteile bietet Microsoft Defender für relationale Open-Source-Datenbanken?

Defender für Cloud bietet Sicherheitswarnungen zu anomalen Aktivitäten, damit Sie potenzielle Bedrohungen erkennen und darauf reagieren können, sobald sie auftreten.

Wenn Sie diesen Plan aktivieren, gibt Defender für Cloud Warnungen aus, sobald ein anomaler Datenbankzugriff und anomale Abfragemuster sowie verdächtige Datenbankaktivitäten erkannt werden.

Diese Warnungen werden auf der Seite mit Sicherheitswarnungen von Defender für Cloud angezeigt und umfassen Folgendes:

- Details zur verdächtigen Aktivität, die die Warnung ausgelöst hat
- die zugehörige MITRE ATT&CK-Taktik
- empfohlene Aktionen zur weiteren Untersuchung und Behandlung der Bedrohung
- Optionen zum Fortsetzen Ihrer Untersuchungen mit Microsoft Sentinel

:::image type="content" source="media/defender-for-databases-introduction/defender-alerts.png" alt-text="Beispiele für Sicherheitswarnungen, die für Datenbanken angezeigt werden, die mit Microsoft Defender für relationale Open-Source-Datenbanken geschützt werden." lightbox="./media/defender-for-databases-introduction/defender-alerts.png":::

## <a name="what-kind-of-alerts-does-microsoft-defender-for-open-source-relational-databases-provide"></a>Welche Art von Warnungen bietet Microsoft Defender für relationale Open-Source-Datenbanken?

Erweiterte Threat Intelligence-Sicherheitswarnungen werden ausgelöst, wenn Folgendes vorliegt:

- **Anomale Muster bei Datenbankzugriff und -abfragen**: Ein Beispiel hierfür sind eine ungewöhnlich hohe Anzahl von Fehlern bei Anmeldeversuchen mit unterschiedlichen Anmeldeinformationen (Brute-Force-Angriff)
- **Verdächtige Datenbankaktivitäten**: Beispielsweise ein berechtigter Benutzer, der über einen Computer mit Sicherheitsverletzung, der mit einem C&C-Server zum Kryptografiemining kommuniziert, auf eine SQL Server-Instanz zugreift
- **Brute-Force-Angriffe**: Dabei besteht die Möglichkeit, einfache Brute-Force-Angriffe von Brute-Force-Angriffen auf einen gültigen Benutzer oder erfolgreichen Brute-Force-Angriffen zu trennen

> [!TIP]
> Die vollständige Liste der Sicherheitswarnungen für Datenbankserver finden Sie auf der [Referenzseite für Warnungen](alerts-reference.md#alerts-osrdb).



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über Microsoft Defender für relationale Open-Source-Datenbanken erfahren.

> [!div class="nextstepaction"]
> [Aktivieren des erhöhten Schutzes](enable-enhanced-security.md)
