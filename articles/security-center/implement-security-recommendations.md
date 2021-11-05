---
title: Implementieren von Sicherheitsempfehlungen in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie auf Empfehlungen in Microsoft Defender für Cloud reagieren können, um Ihre Ressourcen zu schützen und Sicherheitsrichtlinien zu erfüllen.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: d01a8f806154cdf0260f0dcf8c9f6c65386c99f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437909"
---
# <a name="implement-security-recommendations-in-microsoft-defender-for-cloud"></a>Implementieren von Sicherheitsempfehlungen in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Empfehlungen enthalten Vorschläge dazu, wie Sie Ihre Ressourcen besser schützen können. Sie implementieren eine Empfehlung, indem Sie die in der Empfehlung beschriebenen Schritte zur Bereinigung ausführen.

## <a name="remediation-steps"></a>Schritte zur Bereinigung<a name="remediation-steps"></a>

Nach der Überprüfung aller Empfehlungen müssen Sie entscheiden, welche zuerst umgesetzt werden soll. Sie sollten die Sicherheitskontrollen mit dem höchsten Potenzial für die Erhöhung Ihrer Sicherheitsbewertung priorisieren.

1. Wählen Sie in der Liste eine Empfehlung aus.

1. Befolgen Sie die Anweisungen im Abschnitt **Schritte zur Bereinigung**. Jede Empfehlung beinhaltet spezifische Anweisungen. Im folgenden Screenshot werden die Schritte angezeigt, die ausgeführt werden müssen, um Anwendungen so zu konfigurieren, dass der Datenverkehr nur über HTTPS zulässig ist.

    :::image type="content" source="./media/implement-security-recommendations/security-center-remediate-recommendation.png" alt-text="Manuelle Schritte zur Problembehebung für eine Empfehlung" lightbox="./media/implement-security-recommendations/security-center-remediate-recommendation.png":::

1. Nach Abschluss des Vorgangs wird eine Benachrichtigung angezeigt, in der Sie darüber informiert werden, ob das Problem behoben wurde.

## <a name="fix-button"></a>Korrektur-Schaltfläche

Um die Behebung zu vereinfachen und die Sicherheit Ihrer Umgebung zu verbessern (und Ihre Sicherheitsbewertung zu erhöhen), enthalten viele Empfehlungen eine **Korrektur**-Option.

Die **Korrektur** hilft Ihnen, eine Empfehlung für mehrere Ressourcen schnell zu korrigieren.

> [!TIP]
> Die **Korrektur**-Funktion ist nur für bestimmte Empfehlungen verfügbar. Um Empfehlungen zu finden, für die eine Korrektur verfügbar ist, verwenden Sie den Filter **Reaktionsmaßnahmen** für die Liste der Empfehlungen:
> 
> :::image type="content" source="media/implement-security-recommendations/quick-fix-filter.png" alt-text="Verwenden der Filter über der Empfehlungsliste, um Empfehlungen mit Korrekturoption zu finden":::

So führen Sie eine **Korrektur** aus:

1. Wählen Sie in der Liste der Empfehlungen, die das **Korrektur**-Symbol aufweisen:::image type="icon" source="media/implement-security-recommendations/fix-icon.png" border="false":::, eine Empfehlung aus.

    :::image type="content" source="./media/implement-security-recommendations/security-center-recommendations-fix-action.png" alt-text="Empfehlungsliste mit Hervorhebung der Empfehlungen mit der Korrektur-Maßnahme" lightbox="./media/implement-security-recommendations/security-center-recommendations-fix-action.png":::

1. Wählen Sie auf der Registerkarte **Fehlerhafte Ressourcen** die Ressourcen aus, für die Sie die Empfehlung implementieren möchten, und wählen Sie dann **Wartung ausführen** aus.

    > [!NOTE]
    > Einige der aufgelisteten Ressourcen sind möglicherweise deaktiviert, da Sie nicht über die entsprechenden Berechtigungen zum Ändern verfügen.

1. Lesen Sie sich im Bestätigungsdialogfeld die Details und Auswirkungen der Korrektur durch.

    ![Schnelle Problembehebung](./media/implement-security-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Die Auswirkungen sind in dem grauen Feld im Fenster **Ressourcen warten** aufgelistet, das nach dem Klicken auf **Wartung ausführen** geöffnet wird. Sie listen auf, welche Änderungen bei der Durchführung der **Korrektur** auftreten.

1. Fügen Sie ggf. die relevanten Parameter ein, und genehmigen Sie die Korrektur.

    > [!NOTE]
    > Es kann einige Minuten dauern, bis nach der Korrektur die Ressourcen auf der Registerkarte **Fehlerfreie Ressourcen** angezeigt werden. Zum Anzeigen der Korrekturaktionen sehen Sie sich das [Aktivitätsprotokoll](#activity-log) an.

1. Nach Abschluss des Vorgangs wird eine Benachrichtigung angezeigt, in der Sie darüber informiert werden, ob die Umstellung erfolgreich durchgeführt wurde.

## <a name="fix-actions-logged-to-the-activity-log"></a>Die Korrektur-Maßnahmen werden im Aktivitätsprotokoll protokolliert <a name="activity-log"></a>

Beim Korrekturvorgang wird eine Vorlagenbereitstellungs- oder REST-API-`PATCH`-Anforderung verwendet, um die Konfiguration auf die Ressource anzuwenden. Diese Vorgänge werden im [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md) protokolliert.


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde gezeigt, wie Sie Empfehlungen in Defender für Cloud korrigieren können. Informationen dazu, wie Empfehlungen für Ihre Umgebung definiert und ausgewählt werden, finden Sie auf der folgenden Seite:

- [Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?](security-policy-concept.md)