---
title: Konfigurieren von E-Mail-Benachrichtigungen für Microsoft Defender für Cloud-Warnungen
description: Erfahren Sie, wie Sie die E-Mails von Microsoft Defender für Cloud-Sicherheitswarnungen optimieren.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 63123cf7dc7257578b4ddcf3afaf272f42d599d5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526843"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Konfigurieren von E-Mail-Benachrichtigungen für Sicherheitswarnungen 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Sicherheitswarnungen müssen die richtigen Personen in Ihrer Organisation erreichen. Standardmäßig sendet Microsoft Defender für Cloud E-Mails an Abonnementbesitzer, wenn für ein Abonnement eine Warnung mit hohem Schweregrad ausgelöst wird. Auf dieser Seite wird erläutert, wie diese Benachrichtigungen angepasst werden.

Verwenden Sie die Einstellungsseite für **E-Mail-Benachrichtigungen** von Defender für Cloud, um Einstellungen für Benachrichtigungs-E-Mails festzulegen, u. a.:

- ***Wer* soll benachrichtigt werden:** E-Mails können an ausgewählte Einzelpersonen oder an Personen mit einer bestimmten Azure-Rolle für ein Abonnement gesendet werden. 
- ***Worüber* soll eine Person benachrichtigt werden:** Ändern Sie die Schweregrade, für die Defender für Cloud Benachrichtigungen senden soll.

Um Warnungsmüdigkeit zu vermeiden, schränkt Defender für Cloud die Menge der ausgehenden E-Mails ein. Defender für Cloud sendet für jedes Abonnement:

- maximal eine E-Mail alle **6 Stunden** (4 E-Mails pro Tag) für Warnungen mit **hohem Schweregrad**
- maximal eine E-Mail alle **12 Stunden** (2 E-Mails pro Tag) für Warnungen mit **mittlerem Schweregrad**
- maximal eine E-Mail alle **24 Stunden** für Warnungen mit **niedrigem Schweregrad**

:::image type="content" source="./media/configure-email-notifications/email-notification-settings.png" alt-text="Konfigurieren der Details des Kontakts, der E-Mails zu Sicherheitswarnungen erhalten soll." :::
 
## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Kostenlos|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsadministrator**<br>**Besitzer des Abonnements** |
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>Anpassen der E-Mail-Benachrichtigungen für Sicherheitswarnungen über das Portal<a name="email"></a>
Sie können E-Mail-Benachrichtigungen an Einzelpersonen oder an alle Benutzer mit bestimmten Azure-Rollen senden.

1. Wählen Sie in Defender für Cloud im Bereich **Umgebungseinstellungen** das entsprechende Abonnement und dann die Option **E-Mail-Benachrichtigungen** aus.

1. Definieren Sie die Empfänger für Ihre Benachrichtigungen mit einer der folgenden oder beiden Optionen:

    - Wählen Sie in der Dropdownliste eine der verfügbaren Rollen aus.
    - Geben Sie bestimmte E-Mail-Adressen durch Kommas getrennt ein. Sie können beliebig viele E-Mail-Adressen eingeben.

1. Wählen Sie **Speichern** aus, um die Sicherheitskontaktinformationen für Ihr Abonnement zu übernehmen.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>Anpassen der E-Mail-Benachrichtigungen für Warnungen über die API
Sie können Ihre E-Mail-Benachrichtigungen auch über die bereitgestellte REST-API verwalten. Ausführliche Informationen finden Sie in der [Dokumentation zur SecurityContacts-API](/rest/api/securitycenter/securitycontacts).

Bei dem folgenden Beispiel handelt es sich um Anforderungstext für die PUT-Anforderung beim Erstellen einer Sicherheitskontaktkonfiguration:

URI: `https://management.azure.com/subscriptions/<SubscriptionId>/providers/Microsoft.Security/securityContacts/default?api-version=2020-01-01-preview`

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "Medium"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Sicherheitswarnungen finden Sie auf den folgenden Seiten:

- [Sicherheitswarnungen (Referenzhandbuch)](alerts-reference.md): Enthält Informationen zu den Sicherheitswarnungen, die im Threat Protection-Modul von Defender für Cloud ggf. angezeigt werden.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud](managing-and-responding-alerts.md): Hier wird beschrieben, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
- [Workflowautomatisierung](workflow-automation.md): Automatisieren von Reaktionen auf Warnungen mit benutzerdefinierter Benachrichtigungslogik
