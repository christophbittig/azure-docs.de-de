---
title: Verwalten von Sicherheitswarnungen in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: In dieser Dokumentation erfahren Sie mehr zur Verwendung von Microsoft Defender für Cloud-Funktionen zum Verwalten von und Reagieren auf Sicherheitswarnungen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcf92ce079d4e51d448ba199a505706f9e0d5b1d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095875"
---
# <a name="manage-and-respond-to-security-alerts-in-microsoft-defender-for-cloud"></a>Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel erfahren Sie, wie Sie Defender für Cloud-Warnungen anzeigen und verarbeiten und Ihre Ressourcen schützen.

Erweiterte Erkennungen, die Sicherheitswarnungen auslösen, sind nur verfügbar, wenn die erweiterten Sicherheitsfeatures von Microsoft Defender für Cloud aktiviert sind. Eine kostenlose Testversion ist verfügbar. Informationen zum Upgrade finden Sie unter [Aktivieren des erweiterten Schutzes](enable-enhanced-security.md).

## <a name="what-are-security-alerts"></a>Was sind Sicherheitswarnungen?
Defender für Cloud erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von verbundenen Partnerlösungen wie Lösungen zum Schutz von Firewalls und Endpunkten, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste mit priorisierten Sicherheitswarnungen wird in Defender für Cloud zusammen mit den Informationen angezeigt, die Sie zum schnellen Untersuchen des Problems benötigen, und Schritten zum Reagieren auf einen Angriff.

Informationen zu den verschiedenen Arten von Warnungen finden Sie unter [Sicherheitswarnungen (Referenzhandbuch)](alerts-reference.md).

Eine Übersicht darüber, wie Defender für Cloud Warnungen generiert, finden Sie unter [Erkennen von und Reagieren auf Bedrohungen mit Microsoft Defender für Cloud](alerts-overview.md).


## <a name="manage-your-security-alerts"></a>Verwalten von Sicherheitswarnungen

1. Wählen Sie in Defender für Cloud auf der Seite „Übersicht“ entweder oben auf der Seite die Kachel **Sicherheitswarnungen** oder auf der Randleiste den Link „Sicherheitswarnungen“ aus.

    :::image type="content" source="media/managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Abrufen der Seite „Sicherheitswarnungen“ auf der Übersichtsseite von Microsoft Defender für Cloud":::

    Die Seite „Sicherheitswarnungen“ wird geöffnet.

    :::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="Liste der Sicherheitswarnungen von Microsoft Defender für Cloud":::

1. Wählen Sie zum Filtern der Liste der Warnungen einen der relevanten Filter aus. Optional können Sie mit der Option **Filter hinzufügen** weitere Filter hinzufügen.

    :::image type="content" source="./media/managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Hinzufügen von Filtern zur Warnungsansicht" lightbox="./media/managing-and-responding-alerts/alerts-adding-filters-large.png":::

    Die Liste wird entsprechend den von Ihnen ausgewählten Filteroptionen aktualisiert. Diese Filterung kann sehr nützlich sein. Es könnte beispielsweise sein, dass Sie während der Untersuchung einer möglichen Sicherheitsverletzung im System die Sicherheitswarnungen überprüfen möchten, die in den letzten 24 Stunden aufgetreten sind.


## <a name="respond-to-security-alerts"></a>Reagieren auf Sicherheitswarnungen

1. Wählen Sie in der Liste **Sicherheitswarnungen** eine Warnung aus. Daraufhin wird ein Seitenbereich geöffnet, in dem eine Beschreibung der Warnung und alle betroffenen Ressourcen angezeigt werden. 

    :::image type="content" source="./media/managing-and-responding-alerts/alerts-details-pane.png" alt-text="Minidetailansicht einer Sicherheitswarnung":::

    > [!TIP]
    > Wenn dieser Seitenbereich geöffnet ist, können Sie die Liste der Warnungen schnell überprüfen, indem Sie auf der Tastatur die NACH-OBEN- oder NACH-UNTEN-TASTE drücken.

1. Wenn Sie weitere Informationen anzeigen möchten, wählen Sie **Vollständige Details anzeigen** aus.

    Im linken Bereich der Seite mit der Sicherheitswarnung werden allgemeine Informationen zur Sicherheitswarnung angezeigt: Titel, Schweregrad, Status, Zeitpunkt der Aktivität, Beschreibung der verdächtigen Aktivität und die betroffene Ressource. Neben der betroffenen Ressource finden Sie die Azure-Tags für die Ressource. Verwenden Sie diese Tags, um den Organisationskontext der Ressource abzuleiten, wenn Sie die Warnung untersuchen.

    Auf der rechten Seite befindet sich die Registerkarte **Warnungsdetails**, die detaillierte Informationen zur Warnung anzeigt, die Ihnen beim Untersuchen des Problems helfen: IP-Adressen, Dateien, Prozesse und vieles mehr.
     
    ![Vorschläge für Maßnahmen bei Sicherheitswarnungen](./media/managing-and-responding-alerts/security-center-alert-remediate.png)

    Im rechten Bereich finden Sie auch die Registerkarte **Aktion ausführen**. Über diese Registerkarte können Sie weitere Aktionen in Bezug auf die Sicherheitswarnung ausführen. Folgende Aktionen sind möglich:
    - *Auswirkungen der Bedrohung minimieren*: Stellt manuelle Schritte zur Behebung dieser Sicherheitswarnung bereit.
    - *Künftige Angriffe verhindern*: Gibt Sicherheitsempfehlungen, um die Angriffsfläche zu verringern, den Sicherheitsstatus zu erhöhen und so künftige Angriffe zu verhindern.
    - *Automatische Reaktion auslösen*: Bietet die Möglichkeit, eine Logik-App als Reaktion auf diese Sicherheitswarnung auszulösen.
    - *Ähnliche Warnungen unterdrücken*: Bietet die Möglichkeit, zukünftige Warnungen mit ähnlichen Merkmalen zu unterdrücken, wenn die Warnung für Ihre Organisation nicht relevant ist.

    ![Registerkarte „Aktion ausführen“](./media/managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Weitere Informationen

In diesem Dokument haben Sie erfahren, wie Sie Sicherheitswarnungen anzeigen. Verwandte Informationen finden Sie auf den folgenden Seiten:

- [Konfigurieren von Warnungsunterdrückungsregeln](alerts-suppression-rules.md)
- [Automatisieren der Reaktionen auf Defender für Cloud-Trigger](workflow-automation.md)
- [Sicherheitswarnungen (Referenzhandbuch)](alerts-reference.md)
