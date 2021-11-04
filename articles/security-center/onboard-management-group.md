---
title: Integrieren einer Verwaltungsgruppe in Microsoft Defender für Cloud
description: Es wird beschrieben, wie Sie eine bereitgestellte Azure Policy-Definition verwenden, um Microsoft Defender für Cloud für alle Abonnements einer Verwaltungsgruppe zu aktivieren.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 88e57e988efbaaf0472bf809aeecb5ddeb12445c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422404"
---
# <a name="enable-defender-for-cloud-on-all-subscriptions-in-a-management-group"></a>Aktivieren von Defender für Cloud für alle Abonnements in einer Verwaltungsgruppe

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Sie können Azure Policy verwenden, um Microsoft Defender für Cloud in allen Azure-Abonnements innerhalb derselben Verwaltungsgruppe zu aktivieren. Dies ist einfacher als der individuelle Zugriff darauf über das Portal und funktioniert sogar, wenn die Abonnements unterschiedliche Besitzer haben. 

Führen Sie das Onboarding einer Verwaltungsgruppe und aller zugehörigen Abonnements wie folgt durch:

1. Öffnen Sie Azure Policy als Benutzer mit Berechtigungen vom Typ **Sicherheitsadministrator**, und suchen Sie nach der Definition **Defender für Cloud für Ihr Abonnement aktivieren**.

    :::image type="content" source="./media/get-started/enable-security-center-policy.png" alt-text="Azure Policy-Definition „Defender für Cloud für Ihr Abonnement aktivieren“.":::

1. Wählen Sie die Option **Zuweisen** aus, und stellen Sie sicher, dass Sie den Bereich auf die Ebene der Verwaltungsgruppe festlegen.

    :::image type="content" source="./media/get-started/assign-policy.png" alt-text="Zuweisen der Definition „Defender für Cloud für Ihr Abonnement aktivieren“.":::

    > [!TIP]
    > Mit Ausnahme des Bereichs sind keine weiteren Parameter erforderlich.

1. Wählen Sie **Korrekturtask erstellen** aus, um sicherzustellen, dass alle vorhandenen Abonnements, für die Defender für Cloud nicht aktiviert ist, das Onboarding durchgeführt wird.

    :::image type="content" source="./media/get-started/remediation-task.png" alt-text="Erstellen eines Korrekturtasks für die Azure Policy-Definition „Defender für Cloud für Ihr Abonnement aktivieren“.":::

1. Wenn die Definition zugewiesen wird, bewirkt sie Folgendes:

    1. Erkennen aller Abonnements in der Verwaltungsgruppe, die noch nicht bei Defender für Cloud registriert sind.
    1. Kennzeichnen dieser Abonnements als „Nicht konform“
    1. Markieren Sie alle registrierten Abonnements als „konform“ (unabhängig davon, ob die erweiterten Sicherheitsfeatures von Defender für Cloud aktiviert oder deaktiviert sind).

    Mit dem Korrekturtask wird Defender für Cloud dann kostenlos für die nicht konformen Abonnements aktiviert.

> [!IMPORTANT]
> Mit der Richtliniendefinition wird Defender für Cloud nur in **vorhandenen** Abonnements aktiviert. Öffnen Sie zum Registrieren von neu erstellten Abonnements die Registerkarte „Konformität“, wählen Sie die relevanten nicht konformen Abonnements aus, und erstellen Sie einen Korrekturtask. Wiederholen Sie diesen Schritt, wenn Sie mindestens ein neues Abonnement mit Defender für Cloud überwachen möchten.

## <a name="optional-modifications"></a>Optionale Änderungen

Es gibt verschiedene Möglichkeiten, wie Sie die Azure Policy-Definition ändern können: 

- **Abweichendes Definieren von Konformität**: Mit der bereitgestellten Richtlinie werden alle Abonnements der Verwaltungsgruppe, die noch nicht bei Defender für Cloud registriert sind, als „nicht konform“ klassifiziert. Sie können es für alle Abonnements festlegen, ohne dass die erweiterten Sicherheitsfeatures von Defender für Cloud aktiviert sind.

    Mit der bereitgestellten Definition werden *beide* unten angegebenen Preiseinstellungen als konform festgelegt. Dies bedeutet, dass ein Abonnement, das auf „Standard“ oder „Free“ festgelegt ist, konform ist.

    > [!TIP]
    > Wenn ein Microsoft Defender-Plan aktiviert ist, wird er in einer Richtliniendefinition als auf „Standard“ festgelegt beschrieben. Wenn er deaktiviert ist, gilt „Free“. Informationen zu den Unterschieden zwischen diesen Plänen finden Sie unter [Erweiterte Sicherheitsfeatures von Microsoft Defender für Cloud](enhanced-security-features-overview.md). 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Wenn Sie eine Änderung in den folgenden Wert vornehmen, werden nur Abonnements, die auf „Standard“ festgelegt sind, als konform klassifiziert:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Definieren einiger Microsoft Defender-Pläne, die beim Aktivieren von Defender für Cloud angewendet werden sollen**: Die bereitgestellte Richtlinie aktiviert Defender für Cloud ohne optionale erweiterte Sicherheitsfeatures. Sie können einen oder mehrere Microsoft Defender-Pläne aktivieren.

    Im Abschnitt `deployment` der bereitgestellten Definition wird der Parameter `pricingTier` verwendet. Standardmäßig ist er auf `free` festgelegt, aber Sie können dies ändern. 


## <a name="next-steps"></a>Nächste Schritte:

Nach dem Onboarding einer kompletten Verwaltungsgruppe aktivieren Sie nun die erweiterten Sicherheitsfeatures. 

> [!div class="nextstepaction"]
> [Aktivieren des erweiterten Schutzes](enable-enhanced-security.md)