---
title: Konfigurieren von E-Mail-Benachrichtigungen für Azure Security Center-Warnungen
description: Hier erfahren Sie, wie Sie die von Azure Security Center gesendeten E-Mail-Typen für Sicherheitswarnungen optimieren.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/07/2021
ms.author: memildin
ms.openlocfilehash: 4bdfdcc83a920b5b2e7317c4706c470e302cc49f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731101"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Konfigurieren von E-Mail-Benachrichtigungen für Sicherheitswarnungen 

Sicherheitswarnungen müssen die richtigen Personen in Ihrer Organisation erreichen. Standardmäßig sendet Security Center E-Mails an Abonnementbesitzer, wenn für ein Abonnement eine Warnung mit hohem Schweregrad ausgelöst wird. Auf dieser Seite wird erläutert, wie diese Benachrichtigungen angepasst werden.

Wenn Sie eigene Einstellungen für Benachrichtigungs-E-Mails definieren möchten, können Sie auf der Seite mit Einstellungen für **E-Mail-Benachrichtigungen** von Azure Security Center Folgendes auswählen:

- ***Wer* soll benachrichtigt werden:** E-Mails können an ausgewählte Einzelpersonen oder an Personen mit einer bestimmten Azure-Rolle für ein Abonnement gesendet werden. 
- ***Worüber* soll eine Person benachrichtigt werden:** Ändern Sie die Schweregrade, für die Security Center Benachrichtigungen senden soll.

Zur Vermeidung von Warnungsmüdigkeit wird die Menge der ausgehenden E-Mails von Security Center begrenzt. Für jedes Abonnement wird von Security Center Folgendes gesendet:

- maximal eine E-Mail alle **6 Stunden** (4 E-Mails pro Tag) für Warnungen mit **hohem Schweregrad**
- maximal eine E-Mail alle **12 Stunden** (2 E-Mails pro Tag) für Warnungen mit **mittlerem Schweregrad**
- maximal eine E-Mail alle **24 Stunden** für Warnungen mit **niedrigem Schweregrad**

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Konfigurieren der Details des Kontakts, der E-Mails zu Sicherheitswarnungen erhalten soll." :::
 
## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Kostenlos|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsadministrator**<br>**Besitzer des Abonnements** |
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National/Sovereign (Azure Government, Azure China 21Vianet)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>Anpassen der E-Mail-Benachrichtigungen für Sicherheitswarnungen über das Portal<a name="email"></a>
Sie können E-Mail-Benachrichtigungen an Einzelpersonen oder an alle Benutzer mit bestimmten Azure-Rollen senden.

1. Wählen Sie in Security Center im Bereich **Preise und Einstellungen** das entsprechende Abonnement und dann die Option **E-Mail-Benachrichtigungen** aus.

1. Definieren Sie die Empfänger für Ihre Benachrichtigungen mit einer der folgenden oder beiden Optionen:

    - Wählen Sie in der Dropdownliste eine der verfügbaren Rollen aus.
    - Geben Sie bestimmte E-Mail-Adressen durch Kommas getrennt ein. Sie können beliebig viele E-Mail-Adressen eingeben.

1. Wählen Sie **Speichern** aus, um die Sicherheitskontaktinformationen für Ihr Abonnement zu übernehmen.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>Anpassen der E-Mail-Benachrichtigungen für Warnungen über die API
Sie können Ihre E-Mail-Benachrichtigungen auch über die bereitgestellte REST-API verwalten. Ausführliche Informationen finden Sie in der [Dokumentation zur SecurityContacts-API](/rest/api/securitycenter/securitycontacts).

Bei dem folgenden Beispiel handelt es sich um Anforderungstext für die PUT-Anforderung beim Erstellen einer Sicherheitskontaktkonfiguration:

URI: https://management.azure.com/subscriptions/<SubscriptionId> /providers/Microsoft.Security/securityContacts/default?api-version=2020-01-01-preview

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

- [Sicherheitswarnungen (Referenzhandbuch)](alerts-reference.md): Enthält Informationen zu den Sicherheitswarnungen, die im Threat Protection-Modul von Azure Security Center ggf. angezeigt werden.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Hier wird beschrieben, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
- [Workflowautomatisierung](workflow-automation.md): Automatisieren von Reaktionen auf Warnungen mit benutzerdefinierter Benachrichtigungslogik