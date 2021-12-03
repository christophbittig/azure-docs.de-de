---
title: Microsoft Defender für SQL
description: Hier erhalten Sie Informationen zu den Funktionen zur Verwaltung von Datenbankrisiken und die Erkennung ungewöhnlicher Aktivitäten, die eine Bedrohung für Ihre Datenbank in Azure SQL-Datenbank, Azure SQL Managed Instance oder Azure Synapse darstellen können.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 06/07/2021
ms.openlocfilehash: 77c9e9fc02ba5fdb5f67cf14495103b352196f0c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347900"
---
# <a name="microsoft-defender-for-sql"></a>Microsoft Defender für SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Microsoft Defender für SQL ist ein einheitliches Paket für erweiterte SQL-Sicherheitsfunktionen. Microsoft Defender für Cloud ist für die Azure SQL-Datenbank, Azure SQL Verwaltete Instanz (Managed Instance) und Azure Synapse Analytics verfügbar. Dazu zählen die Funktionen zur Ermittlung und Verringerung potenzieller Datenbankschwachstellen und Erkennung ungewöhnlicher Aktivitäten, die eine Bedrohung für Ihre Datenbank darstellen können. Es bietet einen einzelnen Anlaufpunkt zum Aktivieren und Verwalten dieser Funktionen.

## <a name="what-are-the-benefits-of-microsoft-defender-for-sql"></a>Was sind die Vorteile von Microsoft Defender für SQL?

Microsoft Defender stellt eine Reihe erweiterter SQL-Sicherheitsfunktionen bereit, darunter SQL-Risikoanalyse und Advanced Threat Protection (erweiterter Schutz vor Bedrohungen).
- Die [Sicherheitsrisikobewertung](sql-vulnerability-assessment.md) ist ein einfach zu konfigurierender Dienst, mit dem potenzielle Schwachstellen in der Datenbank ermittelt, nachverfolgt und behoben werden können. Er bietet Einblicke in Ihren Sicherheitsstatus, enthält umsetzbare Schritte zum Beheben von Sicherheitsproblemen und verbessert Ihre Datenbanksicherheit.
- [Advanced Threat Protection](threat-detection-overview.md) erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbank zuzugreifen oder diese zu nutzen. Sie überwacht Ihre Datenbank fortlaufend auf verdächtige Aktivitäten und bietet sofortige Sicherheitswarnungen zu potenziellen Sicherheitsrisiken, Angriffen durch Einschleusung von Azure SQL-Befehlen und ungewöhnlichen Datenbankzugriffsmustern. Die Warnungen von Advanced Threat Protection enthalten Details zu verdächtigen Aktivitäten sowie Empfehlungen, wie die Bedrohung untersucht und abgewendet werden kann.

Sie müssen Microsoft Defender für SQL nur einmal aktivieren, um alle diese enthaltenen Funktionen zu aktivieren. Mit nur einem Klick können Sie Microsoft Defender für alle Datenbanken auf Ihrem [Server](logical-servers.md) in Azure oder auf Ihrer SQL verwalteten Instanz (Managed Instance) aktivieren. Um Microsoft Defender-für-Cloud-Einstellungen aktivieren oder verwalten zu können, müssen Sie der Rolle [SQL-Sicherheits-Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) oder einer der Datenbank- oder Serveradministratorrollen angehören.

Weitere Informationen zu den Preisen für Microsoft Defender für SQL finden Sie auf der [Preisseite für Microsoft Defender für Cloud](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-microsoft-defender-for-cloud"></a>Aktivieren von Microsoft Defender für Cloud

Zum Aktivieren von Microsoft Defender-Plänen gibt es mehrere Möglichkeiten. Sie können Azure Defender wie folgt auf Abonnementebene aktivieren (**empfohlen**):

- [Microsoft Defender für Cloud](#enable-microsoft-defender-for-azure-sql-database-at-the-subscription-level-from-microsoft-defender-for-cloud)
- [Defender-Pläne programmgesteuert mit REST-API, Azure-CLI (Befehlszeilenschnittstelle), PowerShell oder Azure Policy aktivieren](#enable-microsoft-defender-plans-programatically)

Alternativ können Sie Microsoft Defender auf Ressourcenebene aktivieren, wie unter [Aktivieren von Microsoft Azure SQL-Datenbank Defender für die Azure SQL-Datenbank auf Ressourcenebene](#enable-microsoft-defender-for-azure-sql-database-at-the-resource-level) beschrieben.

### <a name="enable-microsoft-defender-for-azure-sql-database-at-the-subscription-level-from-microsoft-defender-for-cloud"></a>Aktivieren von Microsoft Defender für die Azure SQL-Datenbank auf Abonnementebene über Microsoft Defender für Cloud
Aktivieren von Microsoft Defender für die Azure SQL-Datenbank auf Abonnementebene über Microsoft Defender für Cloud

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Option **Defender für Cloud**.
1. Wählen Sie im Menü von Defender für Cloud **Preise und Einstellungen** aus.
1. Wählen Sie das relevante Abonnement aus.
1. Ändern Sie die Planeinstellung in **Ein**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Aktivieren von Microsoft Defender für die Azure SQL-Datenbank auf Abonnementebene":::

1. Wählen Sie **Speichern** aus.


### <a name="enable-microsoft-defender-plans-programatically"></a>Programmgesteuertes Aktivieren von Microsoft Defender-Plänen 

Die Flexibilität von Azure ermöglicht eine Reihe programmgesteuerter Methoden zum Aktivieren von Microsoft Defender-Plänen. 

Verwenden Sie eines der folgenden Tools, um Microsoft Defender für Ihr Abonnement zu aktivieren: 

| Methode       | Instructions                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| REST-API     | [Pricings-API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Bundle-Preise](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-microsoft-defender-for-azure-sql-database-at-the-resource-level"></a>Aktivieren von Microsoft Defender für die Azure SQL-Datenbank auf Ressourcenebene

Es wird empfohlen, Microsoft Defender-Pläne auf Abonnementebene zu aktivieren. Dies kann die Erstellung ungeschützter Ressourcen erleichtern. Wenn Sie jedoch Microsoft Defender für Cloud aus organisatorischen Gründen auf Serverebene aktivieren möchten, führen Sie die folgenden Schritte aus:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihren Server oder Ihre verwaltete Instanz.
1. Wählen Sie unter der Überschrift **Sicherheit** die Option **Defender für Cloud** aus.
1. Wählen Sie **Microsoft Defender für SQL aktivieren** aus.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Aktivieren von Microsoft Defender für SQL in Azure SQL-Datenbanken":::

> [!NOTE]
> Ein Speicherkonto wird automatisch erstellt und konfiguriert, um die Speicherkontoergebnisse der **Sicherheitsrisikobewertung** zu speichern. Wenn Sie Microsoft Defender bereits für einen anderen Server in derselben Ressourcengruppe und Region aktiviert haben, wird das vorhandene Speicherkonto verwendet.
>
> Die Kosten für Microsoft Defender für SQL orientieren sich an den Preisen des Standard-Tarifs von Microsoft Defender für Cloud pro Knoten, wobei ein Knoten der gesamte Server oder die verwaltete Instanz ist. Sie bezahlen daher nur einmal für den Schutz aller Datenbanken auf dem Server oder der verwalteten Instanz durch Microsoft Defender für Cloud. Sie können Microsoft Defender für Cloud über eine kostenlose Testversion ausprobieren.

## <a name="manage-microsoft-defender-for-cloud-settings"></a>Verwalten von Microsoft Defender für Cloud-Einstellungen

Anzeigen und Verwalten von Microsoft Defender-für-Cloud-Einstellungen

1. Wählen Sie im Bereich **Sicherheit** Ihres Servers oder der verwalteten Instanz die Option **Defender für Cloud** aus.

    Auf dieser Seite wird der Status von Microsoft Defender für SQL angezeigt:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Überprüfen des Status von Microsoft Defender für SQL in Azure SQL-Datenbanken":::

1. Wenn Microsoft Defender für SQL aktiviert ist, wird ein Link **Konfigurieren**, wie in der vorherigen Abbildung dargestellt, angezeigt. Wählen Sie **Konfigurieren** aus, um die Einstellungen für Microsoft Defender für SQL zu bearbeiten.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Einstellungen für Microsoft Defender für SQL":::.

1. Nehmen Sie die gewünschten Änderungen vor, und wählen Sie **Speichern** aus.


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Sicherheitsrisikobewertung](sql-vulnerability-assessment.md)
- Weitere Informationen zu [Advanced Threat Protection](threat-detection-configure.md)
- Erfahren Sie mehr zu [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md).
