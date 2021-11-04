---
title: Einrichten von Microsoft Defender für relationale Open-Source-Datenbanken und Reagieren auf Warnungen
description: Hier erfahren Sie, wie Sie Microsoft Defender für relationale Open-Source-Datenbanken konfigurieren, um anomale Datenbankaktivitäten zu erkennen, die auf potenzielle Sicherheitsbedrohungen für die Datenbank hinweisen.
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7d34663e9c83e74ea25ce501a89a5a34b38677e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056001"
---
# <a name="enable-microsoft-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>Aktivieren von Microsoft Defender für relationale Open-Source-Datenbanken und Reagieren auf Warnungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Für die folgenden Dienste erkennt Microsoft Defender anomale Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese unbefugt zu nutzen:

- [Azure-Datenbank für PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Um Warnungen vom Microsoft Defender-Plan zu erhalten, müssen Sie ihn zunächst [wie unten beschrieben](#enable-enhanced-security) aktivieren.

Weitere Informationen zu diesem Microsoft Defender-Plan finden Sie unter [Einführung in Microsoft Defender für relationale Open-Source-Datenbanken](defender-for-databases-introduction.md).

## <a name="enable-enhanced-security"></a>Aktivieren der erweiterten Sicherheit

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Konfigurationsseite des Datenbankservers, den Sie schützen möchten.

1. Wählen Sie im Sicherheitsmenü auf der linken Seite **Microsoft Defender für Cloud** aus.

1. Wenn die erweiterte Sicherheit nicht aktiviert ist, wird eine Schaltfläche angezeigt, wie im folgenden Screenshot dargestellt. Wählen Sie **Microsoft Defender für [Datenbanktyp] aktivieren** (z. B. „Microsoft Defender für MySQL“) und dann **Speichern** aus.

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="Microsoft Defender für die MySQL aktivieren." lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > Diese Seite im Portal ist unabhängig vom Datenbanktyp (PostgreSQL, MySQL oder MariaDB) immer gleich.

## <a name="respond-to-security-alerts"></a>Reagieren auf Sicherheitswarnungen

Wenn Microsoft Defender für Ihre Datenbank aktiviert ist, werden anomale Aktivitäten erkannt und Warnungen generiert. Diese Warnungen sind an mehreren Stellen verfügbar, z. B.:

- Führen Sie im Azure-Portal die folgenden Schritte aus:
    - **Microsoft Defender für Cloud-Seite für Sicherheitswarnungen**: Zeigt Warnungen für alle durch Defender für Cloud geschützten Ressourcen in den Abonnements an, für die Sie über Anzeigeberechtigungen verfügen.
    - **Microsoft Defender für Cloud**-Seite der Ressource: Zeigt Warnungen und Empfehlungen für eine bestimmte Ressource an, wie unter [Aktivieren der erweiterten Sicherheit](#enable-enhanced-security) oben beschrieben.
- Im Posteingang der Person in Ihrer Organisation, die für den [Empfang von E-Mail-Warnungen festgelegt wurde](configure-email-notifications.md).  

> [!TIP]
> Auf einer Livekachel im [Übersichtsdashboard von Microsoft Defender für Cloud](overview-page.md) wird der Status aktiver Bedrohungen für all Ihre Ressourcen (einschließlich Datenbanken) nachverfolgt. Wählen Sie die Kachel aus, um die Warnungsseite von Defender für Cloud zu öffnen und einen Überblick über die aktiven Bedrohungen zu erhalten, die für Ihre Datenbanken erkannt wurden.
>
> Eine ausführliche Anleitung und die empfohlene Methode für die Reaktion auf Sicherheitswarnungen finden Sie unter [Reagieren auf eine Sicherheitswarnung](tutorial-security-incident.md#respond-to-a-security-alert).

### <a name="respond-to-email-notifications-of-security-alerts"></a>Reagieren auf E-Mail-Benachrichtigungen über Sicherheitswarnungen

Defender für Cloud sendet E-Mail-Benachrichtigungen, wenn anomale Datenbankaktivitäten erkannt werden. Die E-Mail enthält Informationen zum verdächtigen Sicherheitsereignis, z. B. Art der anomalen Aktivitäten, Datenbankname, Servername, Anwendungsname und Zeit des Ereignisses. Darüber hinaus enthält die E-Mail Informationen zu den möglichen Ursachen und empfohlene Maßnahmen zur Untersuchung und Abwehr potenzieller Bedrohungen für die Datenbank.

1. Wählen Sie in der E-Mail den Link **Vollständige Warnung anzeigen** aus, um das Azure-Portal zu öffnen und die Warnungsseite mit einer Übersicht der aktiven Bedrohungen anzuzeigen, die für die Datenbank erkannt wurden.
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="E-Mail-Benachrichtigung von Defender für Cloud zu einem vermuteten Brute-Force-Angriff.":::

    Zeigen Sie aktive Bedrohungen auf der Abonnementebene auf den Defender für Cloud-Seiten im Portal an:

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="In Microsoft Defender für Cloud angezeigte aktive Bedrohungen für ein oder mehrere Abonnements." lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. Wählen Sie eine Warnung aus, um weitere Details und empfohlene Aktionen zum Untersuchen der aktuellen Bedrohung und Abwehren zukünftiger Bedrohungen anzuzeigen.
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="Details zu einer bestimmten Warnung" lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> Ein ausführliches Tutorial zur Behandlung von Warnungen finden Sie unter [Tutorial: Selektieren, Untersuchen und Beantworten von Sicherheitswarnungen](tutorial-security-incident.md).


## <a name="next-steps"></a>Nächste Schritte

- [Automatisieren der Reaktionen auf Defender für Cloud-Trigger](workflow-automation.md)
- [Streamen von Warnungen in eine SIEM-, SOAR- oder IT-Service-Management-Lösung](export-to-siem.md)
- [Unterdrücken von Warnungen von Defender für Cloud](alerts-suppression-rules.md)
