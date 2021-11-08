---
title: Tutorial zur Reaktion auf Warnungen – Microsoft Defender für Cloud
description: In diesem Tutorial erfahren Sie, wie Sie Sicherheitswarnungen selektieren und die Grundursache und den Bereich eines Vorfalls ermitteln.
services: security-center
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 7053bcc1e28eeb262dda8833ddf98806aae770b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472103"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Tutorial: Selektieren, Untersuchen und Beantworten von Sicherheitswarnungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Cloud analysiert Ihre Hybrid Cloud-Workloads ständig mithilfe von Advanced Analytics- und Threat Intelligence-Funktionen, um Sie vor potenziell schädlichen Aktivitäten auf Ihren Cloudressourcen warnen zu können. Sie können auch Warnungen aus anderen Sicherheitsprodukten und -diensten in Defender für Cloud integrieren. Nach der Auslösung einer Warnung muss schnell gehandelt werden, um das potenzielle Sicherheitsproblem zu untersuchen und zu beheben. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Selektieren von Sicherheitswarnungen
> * Untersuchen einer Sicherheitswarnung zum Ermitteln der Grundursache
> * Reagieren auf eine Sicherheitswarnung und Beseitigen der Grundursache

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
Um die in diesem Tutorial behandelten Features kennenzulernen, müssen Sie die erweiterten Sicherheitsfeatures von Defender für Cloud aktiviert haben. Sie können diese Features kostenlos testen. Weitere Informationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/). In der Schnellstartanleitung [Erste Schritte mit Defender für Cloud](get-started.md) werden die erforderlichen Schritte für den Upgradevorgang beschrieben.


## <a name="triage-security-alerts"></a>Selektieren von Sicherheitswarnungen
Mit Defender für Cloud können Sie sich einen Gesamtüberblick über alle Sicherheitswarnungen verschaffen. Sicherheitswarnungen werden nach Schweregrad der erkannten Aktivität angeordnet. 

Beurteilen Sie die Warnungen auf der Seite **Sicherheitswarnungen**:

:::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="Liste mit Sicherheitswarnungen von Microsoft Defender für Cloud":::

Überprüfen Sie auf dieser Seite die aktiven Sicherheitswarnungen in Ihrer Umgebung, um die Warnungen auszuwählen, die Sie zuerst untersuchen möchten.

Wenn Sie Sicherheitswarnungen selektieren, priorisieren Sie Warnungen nach ihrem Schweregrad, indem Sie sich den Warnungen mit höherem Schweregrad zuerst widmen. Weitere Informationen zum Schweregrad von Warnungen finden Sie unter [Wie werden Warnungen klassifiziert?](alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> Sie können Microsoft Defender für Cloud mit den beliebtesten SIEM-Lösungen verbinden, z. B. Microsoft Sentinel, und die Warnungen im Tool Ihrer Wahl empfangen. Weitere Informationen finden Sie unter [Streamen von Warnungen in eine SIEM-, SOAR- oder IT-Service-Management-Lösung](export-to-siem.md).


## <a name="investigate-a-security-alert"></a>Untersuchen einer Sicherheitswarnung

Gehen Sie wie folgt vor, sobald Sie entschieden haben, welche Warnung Sie zuerst untersuchen möchten:

1. Wählen Sie die gewünschte Warnung aus.
1. Wählen Sie auf der Seite mit der Übersicht über die Warnungen die Ressource aus, die zuerst untersucht werden soll.
1. Beginnen Sie mit der Untersuchung im linken Bereich, in dem allgemeine Informationen zur Sicherheitswarnung angezeigt werden.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Linker Bereich der Seite „Warnungsdetails“, in der die allgemeinen Informationen hervorgehoben sind":::.

    In diesem Bereich wird Folgendes angezeigt:
    - Schweregrad, Status und Aktivitätszeit der Warnung
    - Beschreibung, die die genaue erkannte Aktivität erläutert
    - Betroffene Ressourcen
    - Kill Chain-Absicht der Aktivität auf der MITRE ATT&CK-Matrix

1. Ausführlichere Informationen zur Untersuchung der verdächtigen Aktivität finden Sie auf der Registerkarte **Warnungsdetails**.

1. Die Informationen auf dieser Seite reichen möglicherweise für eine Reaktion aus. Wenn Sie weitere Informationen benötigen:

    - Wenden Sie sich an den Ressourcenbesitzer, um zu überprüfen, ob die erkannte Aktivität ein False Positive ist.
    - Untersuchen der von der angegriffenen Ressource generierten unformatierten Protokolle

## <a name="respond-to-a-security-alert"></a>Reagieren auf eine Sicherheitswarnung
Nachdem Sie eine Sicherheitswarnung untersucht und deren Umfang verstanden haben, können Sie in Microsoft Defender für Cloud auf die Warnung reagieren:

1.  Öffnen Sie die Registerkarte **Aktion ausführen**, um die empfohlenen Reaktionen anzuzeigen.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Registerkarte „Maßnahme ergreifen“ zu Sicherheitswarnungen." lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Im Abschnitt **Auswirkungen der Bedrohung minimieren**, finden Sie die manuellen Schritte zur Behebung des Problems.
1.  Um Ihre Ressourcen zu sichern und zukünftige Angriffe dieser Art zu verhindern, setzen Sie die Sicherheitsempfehlungen im Abschnitt **Künftige Angriffe verhindern** um.
1.  Um eine Logik-App mit automatisierten Reaktionsschriften zu starten, verwenden Sie den Abschnitt **Automatisierte Antwort auslösen**.
1.  Wenn die erkannte Aktivität *nicht* bösartig ist, können Sie künftige Warnungen dieser Art im Abschnitt **Ähnliche Warnungen unterdrücken** unterdrücken.

1.  Wenn Sie die Untersuchung der Warnung abgeschlossen und entsprechend reagiert haben, ändern Sie den Status in **Verworfen**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Festlegen des Status einer Warnung":::

    Dadurch wird die Warnung aus der Hauptwarnungsliste entfernt. Mit dem Filter auf der Seite mit der Liste der Warnungen können Sie alle Warnungen mit dem Status **Verworfen** anzeigen.

1.  Wir würden uns freuen, wenn Sie Microsoft Feedback zur Warnung zur Verfügung stellen würden:
    1. Markieren Sie die Warnung als **Hilfreich** oder **Nicht hilfreich**.
    1. Wählen Sie einen Grund aus und fügen Sie einen Kommentar hinzu.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Feedback zur Nützlichkeit einer Warnung an Microsoft.":::

    > [!TIP]
    > Wir berücksichtigen Ihr Feedback, um unsere Algorithmen zu verbessern und bessere Sicherheitswarnungen bereitzustellen.

## <a name="end-the-tutorial"></a>Abschließen des Tutorials

Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Falls Sie weitere Schnellstartanleitungen und Tutorials durcharbeiten möchten, sollten Sie die automatische Bereitstellung und die erweiterten Sicherheitsfeatures von Defender für Cloud aktiviert lassen. 

Wenn Sie nicht mit den anderen Tutorials fortfahren möchten oder wenn Sie eines dieser Features deaktivieren möchten:

1. Öffnen Sie im Menü von Defender für Cloud **Umgebungseinstellungen**.
1. Wählen Sie das relevante Abonnement aus.
1. Wählen Sie **Defender-Pläne** und dann die Option **Erhöhte Sicherheit deaktiviert** aus.

    :::image type="content" source="./media/enable-enhanced-security/disable-plans.png" alt-text="Aktivieren bzw. Deaktivieren der erweiterten Sicherheitsfeatures von Defender für Cloud":::

1. Wählen Sie **Speichern** aus.

    > [!NOTE]
    > Nachdem Sie die erweiterten Sicherheitsfeatures deaktiviert haben – unabhängig davon, ob Sie einen einzelnen Plan oder alle Pläne gleichzeitig deaktivieren –, wird die Datensammlung ggf. noch für kurze Zeit fortgesetzt. 

1. Öffnen Sie im Menü von Defender für Cloud **Umgebungseinstellungen**.
1. Wählen Sie das relevante Abonnement aus.
1. Wählen Sie **Automatische Bereitstellung** aus.
1. Deaktivieren Sie die relevanten Erweiterungen.

    >[!NOTE]
    > Wenn Sie die automatische Bereitstellung deaktivieren, wird der Log Analytics-Agent nicht von virtuellen Azure-Computern entfernt, auf denen der Agent bereitgestellt wurde. Wenn Sie die automatische Bereitstellung deaktivieren, schränkt dies die Sicherheitsüberwachung für Ihre Ressourcen ein.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Informationen zu Defender für Cloud-Features erhalten, die zum Reagieren auf eine Sicherheitswarnung verwendet werden. Verwandte Informationen finden Sie hier:

- [Reagieren auf Warnungen von Microsoft Defender für Key Vault](defender-for-key-vault-usage.md)
- [Sicherheitswarnungen (Referenzhandbuch)](alerts-reference.md)
- [Einführung in Defender für Cloud](defender-for-cloud-introduction.md)