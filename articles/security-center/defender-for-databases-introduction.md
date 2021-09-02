---
title: Azure Defender für relationale Open-Source-Datenbanken – Vorteile und Features
description: Hier erfahren Sie mehr über die Vorteile und Features von Azure Defender für relationale Open-Source-Datenbanken wie PostgreSQL, MySQL und MariaDB
author: memildin
ms.author: memildin
ms.date: 05/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 209fdfd71b4e2c0a5d1d5ede3f411b6476c19570
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732571"
---
# <a name="introduction-to-azure-defender-for-open-source-relational-databases"></a>Einführung in Azure Defender für relationale Open-Source-Datenbanken

Dieser Azure Defender-Plan bietet Schutz gegen Bedrohungen für die folgenden relationalen Open-Source-Datenbanken:

- [Azure-Datenbank für PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Azure Defender erkennt anomale Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese missbräuchlich zu nutzen. Mit diesem Plan kann problemlos auf potenzielle Bedrohungen für die Datenbank reagiert werden, ohne das Fachwissen eines Sicherheitsexperten besitzen oder komplexe Sicherheitsüberwachungssysteme verwalten zu müssen.

## <a name="availability"></a>Verfügbarkeit

| Aspekt                             | Details                                                                                                                                    |
|------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| Status des Release:                     | Allgemeine Verfügbarkeit (General Availability, GA)                                                     |
| Preise:                           | **Azure Defender für relationale Open-Source-Datenbanken** wird gemäß den unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/) aufgeführten Preisen abgerechnet.   |
| Geschützte Versionen von PostgreSQL:  | Einzelserver: „Universell“ und „Arbeitsspeicheroptimiert“. Weitere Informationen finden Sie in der Übersicht über die [PostgreSQL-Tarife](../postgresql/concepts-pricing-tiers.md).   |
| Geschützte Versionen von MySQL:       | Einzelserver: „Universell“ und „Arbeitsspeicheroptimiert“. Weitere Informationen finden Sie in der Übersicht über die [MySQL-Tarife](../mysql/concepts-pricing-tiers.md).                        |
| Geschützte Versionen von MariaDB:     | „Universell“ und „Arbeitsspeicheroptimiert“. Weitere Informationen finden Sie in der Übersicht über die [MariaDB-Tarife](../mariadb/concepts-pricing-tiers.md).                      |
| Clouds:                            | :::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National/Sovereign (Azure Government, Azure China 21Vianet) |
|                                    |                                                                                                                                            |

## <a name="what-are-the-benefits-of-azure-defender-for-open-source-relational-databases"></a>Welche Vorteile bietet Azure Defender für relationale Open-Source-Datenbanken?

Azure Defender bietet Sicherheitswarnungen zu anomalen Aktivitäten, damit Sie potenzielle Bedrohungen erkennen und darauf reagieren können, sobald sie auftreten.

Wenn Sie diesen Plan aktivieren, gibt Azure Defender Warnungen aus, sobald ein anomaler Datenbankzugriff und anomale Abfragemuster sowie verdächtige Datenbankaktivitäten erkannt werden.

Diese Warnungen werden auf der Seite mit Sicherheitswarnungen von Azure Defender angezeigt und umfassen Folgendes:

- Details zur verdächtigen Aktivität, die die Warnung ausgelöst hat
- die zugehörige MITRE ATT&CK-Taktik
- empfohlene Aktionen zur weiteren Untersuchung und Behandlung der Bedrohung
- Optionen zum Fortsetzen Ihrer Untersuchungen mit Azure Sentinel

:::image type="content" source="media/defender-for-databases-introduction/defender-alerts.png" alt-text="Beispiele für Sicherheitswarnungen, die für Datenbanken angezeigt werden, die mit Azure Defender für relationale Open-Source-Datenbanken geschützt werden." lightbox="./media/defender-for-databases-introduction/defender-alerts.png":::

## <a name="what-kind-of-alerts-does-azure-defender-for-open-source-relational-databases-provide"></a>Welche Art von Warnungen bietet Azure Defender für relationale Open-Source-Datenbanken?

Erweiterte Threat Intelligence-Sicherheitswarnungen werden ausgelöst, wenn Folgendes vorliegt:

- **Anomale Muster bei Datenbankzugriff und -abfragen**: Ein Beispiel hierfür sind eine ungewöhnlich hohe Anzahl von Fehlern bei Anmeldeversuchen mit unterschiedlichen Anmeldeinformationen (Brute-Force-Angriff)
- **Verdächtige Datenbankaktivitäten**: Beispielsweise ein berechtigter Benutzer, der über einen Computer mit Sicherheitsverletzung, der mit einem C&C-Server zum Kryptografiemining kommuniziert, auf eine SQL Server-Instanz zugreift
- **Brute-Force-Angriffe**: Dabei besteht die Möglichkeit, einfache Brute-Force-Angriffe von Brute-Force-Angriffen auf einen gültigen Benutzer oder erfolgreichen Brute-Force-Angriffen zu trennen

> [!TIP]
> Die vollständige Liste der Sicherheitswarnungen für Datenbankserver finden Sie auf der [Referenzseite für Warnungen](alerts-reference.md#alerts-osrdb).



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie sich über Azure Defender für relationale Open-Source-Datenbanken informiert.

> [!div class="nextstepaction"]
> [Aktivieren von Azure Defender](enable-azure-defender.md)
