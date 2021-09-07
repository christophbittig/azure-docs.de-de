---
title: Einrichten von Azure Defender für relationale Open-Source-Datenbanken und Reagieren auf Warnungen
description: Hier erfahren Sie, wie Sie Azure Defender für relationale Open-Source-Datenbanken konfigurieren, um anomale Datenbankaktivitäten zu erkennen, die auf potenzielle Sicherheitsbedrohungen für die Datenbank hinweisen.
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1300a8ad11d7091085c95205b435a2d3fb077cd3
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369967"
---
# <a name="enable-azure-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>Aktivieren von Azure Defender für relationale Open-Source-Datenbanken und Reagieren auf Warnungen

Für die folgenden Dienste erkennt Azure Defender anomale Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese unbefugt zu nutzen:

- [Azure-Datenbank für PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Um Warnungen vom Azure Defender-Plan zu erhalten, müssen Sie ihn zunächst [wie unten beschrieben](#enable-azure-defender) aktivieren.

Weitere Informationen zu diesem Azure Defender-Plan finden Sie unter [Einführung in Azure Defender für relationale Open-Source-Datenbanken](defender-for-databases-introduction.md).

## <a name="enable-azure-defender"></a>Aktivieren von Azure Defender

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Konfigurationsseite des Datenbankservers, den Sie schützen möchten.

1. Wählen Sie auf der linken Seite im Menü „Sicherheit“ die Option **Security Center** aus.

1. Wenn Azure Defender nicht aktiviert ist, wird wie im folgenden Screenshot dargestellt eine Schaltfläche zum Aktivieren angezeigt. Wählen Sie **Azure Defender für [Datenbanktyp] aktivieren** (z. B. „Azure Defender für MySQL“) und dann **Speichern** aus.

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="Aktivieren von Azure Defender für MySQL" lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > Diese Seite im Portal ist unabhängig vom Datenbanktyp (PostgreSQL, MySQL oder MariaDB) immer gleich.

## <a name="respond-to-security-alerts"></a>Reagieren auf Sicherheitswarnungen

Wenn Azure Defender für Ihre Datenbank aktiviert ist, werden anomale Aktivitäten erkannt und Warnungen generiert. Diese Warnungen sind an mehreren Stellen verfügbar, z. B.:

- Führen Sie im Azure-Portal die folgenden Schritte aus:
    - **Azure Defender-Seite für Sicherheitswarnungen**: Zeigt Warnungen für alle durch Azure Defender geschützten Ressourcen in den Abonnements an, für die Sie über Anzeigeberechtigungen verfügen.
    - **Security Center**-Seite der Ressource: Zeigt Warnungen und Empfehlungen für eine bestimmte Ressource an (siehe Screenshot im obigen Abschnitt [Aktivieren von Azure Defender](#enable-azure-defender)).
- Im Posteingang der Person in Ihrer Organisation, die für den [Empfang von E-Mail-Warnungen festgelegt wurde](security-center-provide-security-contact-details.md).  

> [!TIP]
> Auf einer Livekachel im [Übersichtsdashboard von Azure Security Center](overview-page.md) wird der Status aktiver Bedrohungen für all Ihre Ressourcen (einschließlich Datenbanken) nachverfolgt. Wählen Sie die Kachel aus, um die Warnungsseite von Azure Defender zu öffnen und einen Überblick über die aktiven Bedrohungen zu erhalten, die für Ihre Datenbanken erkannt wurden.
>
> Eine ausführliche Anleitung und die empfohlene Methode für die Reaktion auf Azure Defender-Warnungen finden Sie unter [Reagieren auf eine Sicherheitswarnung](tutorial-security-incident.md#respond-to-a-security-alert).


### <a name="respond-to-email-notifications-of-security-alerts"></a>Reagieren auf E-Mail-Benachrichtigungen über Sicherheitswarnungen

Azure Defender sendet E-Mail-Benachrichtigungen, wenn anomale Datenbankaktivitäten erkannt werden. Die E-Mail enthält Informationen zum verdächtigen Sicherheitsereignis, z. B. Art der anomalen Aktivitäten, Datenbankname, Servername, Anwendungsname und Zeit des Ereignisses. Darüber hinaus enthält die E-Mail Informationen zu den möglichen Ursachen und empfohlene Maßnahmen zur Untersuchung und Abwehr potenzieller Bedrohungen für die Datenbank.

1. Klicken Sie in der E-Mail auf den Link **View the full alert** (Vollständige Warnung anzeigen), um das Azure-Portal zu öffnen und die Warnungsseite mit einer Übersicht der aktiven Bedrohungen anzuzeigen, die für die Datenbank erkannt wurden.
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="E-Mail-Benachrichtigung von Azure Defender zu einem vermuteten Brute-Force-Angriff":::

    Zeigen Sie aktive Bedrohungen auf der Abonnementebene auf den Security Center-Seiten im Portal an:

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="In Azure Security Center angezeigte aktive Bedrohungen für ein oder mehrere Abonnements" lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. Wählen Sie eine Warnung aus, um weitere Details und empfohlene Aktionen zum Untersuchen der aktuellen Bedrohung und Abwehren zukünftiger Bedrohungen anzuzeigen.
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="Details zu einer bestimmten Warnung" lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> Ein ausführliches Tutorial zur Behandlung von Warnungen finden Sie unter [Tutorial: Selektieren, Untersuchen und Beantworten von Sicherheitswarnungen](tutorial-security-incident.md).


## <a name="next-steps"></a>Nächste Schritte

- [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md)
- [Streamen von Warnungen in eine SIEM-, SOAR- oder IT-Service-Management-Lösung](export-to-siem.md)
- [Unterdrücken von Warnungen von Azure Defender](alerts-suppression-rules.md)