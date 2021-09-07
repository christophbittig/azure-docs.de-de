---
title: Umsetzen von Empfehlungen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie auf Empfehlungen in Azure Security Center reagieren, um Ihre Ressourcen zu schützen und Sicherheitsrichtlinien einzuhalten.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f0caaea2296a8981df4717497cb641676b43dea1
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237073"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Umsetzen von Empfehlungen in Azure Security Center

Empfehlungen enthalten Vorschläge dazu, wie Sie Ihre Ressourcen besser schützen können. Sie implementieren eine Empfehlung, indem Sie die in der Empfehlung beschriebenen Schritte zur Bereinigung ausführen.

## <a name="remediation-steps"></a>Schritte zur Bereinigung<a name="remediation-steps"></a>

Nach der Überprüfung aller Empfehlungen müssen Sie entscheiden, welche zuerst umgesetzt werden soll. Sie sollten die Sicherheitskontrollen mit dem höchsten Potenzial für die Erhöhung Ihrer Sicherheitsbewertung priorisieren.

1. Wählen Sie in der Liste eine Empfehlung aus.

1. Befolgen Sie die Anweisungen im Abschnitt **Schritte zur Bereinigung**. Jede Empfehlung beinhaltet spezifische Anweisungen. Im folgenden Screenshot werden die Schritte angezeigt, die ausgeführt werden müssen, um Anwendungen so zu konfigurieren, dass der Datenverkehr nur über HTTPS zulässig ist.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Manuelle Schritte zur Problembehebung für eine Empfehlung" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Nach Abschluss des Vorgangs wird eine Benachrichtigung angezeigt, in der Sie darüber informiert werden, ob das Problem behoben wurde.

## <a name="fix-button"></a>Korrektur-Schaltfläche

Um die Behebung zu vereinfachen und die Sicherheit Ihrer Umgebung zu verbessern (und Ihre Sicherheitsbewertung zu erhöhen), enthalten viele Empfehlungen eine **Korrektur**-Option.

Die **Korrektur** hilft Ihnen, eine Empfehlung für mehrere Ressourcen schnell zu korrigieren.

> [!TIP]
> Die **Korrektur**-Funktion ist nur für bestimmte Empfehlungen verfügbar. Um Empfehlungen zu finden, für die eine Korrektur verfügbar ist, verwenden Sie den Filter **Reaktionsmaßnahmen** für die Liste der Empfehlungen:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Verwenden der Filter über der Empfehlungsliste, um Empfehlungen mit Korrekturoption zu finden":::

So führen Sie eine **Korrektur** aus:

1. Wählen Sie in der Liste der Empfehlungen, die das **Korrektur**-Symbol aufweisen:::image type="icon" source="media/security-center-remediate-recommendations/fix-icon.png" border="false":::, eine Empfehlung aus.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-recommendations-fix-action.png" alt-text="Empfehlungsliste mit Hervorhebung der Empfehlungen mit der Korrektur-Maßnahme" lightbox="./media/security-center-remediate-recommendations/security-center-recommendations-fix-action.png#lightbox":::

1. Wählen Sie auf der Registerkarte **Fehlerhafte Ressourcen** die Ressourcen aus, für die Sie die Empfehlung implementieren möchten, und wählen Sie dann **Wartung ausführen** aus.

    > [!NOTE]
    > Einige der aufgelisteten Ressourcen sind möglicherweise deaktiviert, da Sie nicht über die entsprechenden Berechtigungen zum Ändern verfügen.

1. Lesen Sie sich im Bestätigungsdialogfeld die Details und Auswirkungen der Korrektur durch.

    ![Schnelle Problembehebung](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Die Auswirkungen sind in dem grauen Feld im Fenster **Ressourcen warten** aufgelistet, das nach dem Klicken auf **Wartung ausführen** geöffnet wird. Sie listen auf, welche Änderungen bei der Durchführung der **Korrektur** auftreten.

1. Fügen Sie ggf. die relevanten Parameter ein, und genehmigen Sie die Korrektur.

    > [!NOTE]
    > Es kann einige Minuten dauern, bis nach der Korrektur die Ressourcen auf der Registerkarte **Fehlerfreie Ressourcen** angezeigt werden. Zum Anzeigen der Korrekturaktionen sehen Sie sich das [Aktivitätsprotokoll](#activity-log) an.

1. Nach Abschluss des Vorgangs wird eine Benachrichtigung angezeigt, in der Sie darüber informiert werden, ob die Umstellung erfolgreich durchgeführt wurde.

## <a name="fix-actions-logged-to-the-activity-log"></a>Die Korrektur-Maßnahmen werden im Aktivitätsprotokoll protokolliert <a name="activity-log"></a>

Beim Korrekturvorgang wird ein Vorlagenbereitstellungs- oder REST PATCH-API-Aufruf verwendet, um die Konfiguration auf die Ressource anzuwenden. Diese Vorgänge werden im [Azure-Aktivitätsprotokoll](../azure-resource-manager/management/view-activity-logs.md) protokolliert.


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie Empfehlungen in Security Center umsetzen. Weitere Informationen zu Security Center finden Sie auf den folgenden Seiten:

* [Festlegen von Sicherheitsrichtlinien im Azure Security Center](tutorial-security-policy.md) – Hier erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?](security-policy-concept.md)