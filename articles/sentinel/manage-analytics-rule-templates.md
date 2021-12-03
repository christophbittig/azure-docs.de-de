---
title: Verwalten von Vorlagenversionen für Ihre geplanten Analyseregeln in Microsoft Sentinel
description: Erfahren Sie, wie Sie die Beziehung zwischen Ihren Vorlagen für geplante Analyseregeln und den aus diesen Vorlagen erstellten Regeln verwalten. Außerdem erhalten Sie Informationen dazu, wie Sie Aktualisierungen der Vorlagen mit Ihren Regeln zusammenführen und Änderungen in Ihren Regeln auf die ursprüngliche Vorlage zurücksetzen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5845dfd88e3377eacff5b1d27ab927df422e4556
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522952"
---
# <a name="manage-template-versions-for-your-scheduled-analytics-rules-in-microsoft-sentinel"></a>Verwalten von Vorlagenversionen für Ihre geplanten Analyseregeln in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Dieses Feature befindet sich in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="introduction"></a>Einführung

Microsoft Sentinel enthält [Analyseregelvorlagen](detect-threats-built-in.md), die Sie in aktive Regeln umwandeln, indem Sie eine Kopie davon erstellen. Dies geschieht, wenn Sie eine Regel aus einer Vorlage erstellen. An diesem Punkt ist die aktive Regel jedoch nicht mehr mit der Vorlage verbunden. Wenn eine Regelvorlage von Microsoft-Technikern oder anderen Personen geändert wird, werden alle zuvor aus dieser Vorlage erstellten Regeln ***nicht*** dynamisch an die neue Vorlage angepasst.

Allerdings ***merken*** sich Regeln, die aus Vorlagen erstellt wurden, von welchen Vorlagen sie stammten. Dies bietet zwei Vorteile:

- Wenn Sie beim Erstellen einer Regel aus einer Vorlage (oder zu einem beliebigen späteren Zeitpunkt) Änderungen an der Regel vorgenommen haben, können Sie die Regel jederzeit auf ihre ursprüngliche Version (als Kopie der Vorlage) zurücksetzen.

- Sie können sich benachrichtigen lassen, wenn eine Vorlage aktualisiert wird, und haben dann die Wahl, Ihre Regeln auf die neue Version der jeweiligen Vorlage zu aktualisieren oder sie in der vorhandenen Form zu belassen.

In diesem Artikel erfahren Sie, wie Sie diese Aufgaben handhaben und was dabei zu beachten ist. Die nachfolgend beschriebenen Verfahren gelten für alle **[geplanten](detect-threats-built-in.md#scheduled)** Analyseregeln, die aus Vorlagen erstellt wurden.

## <a name="discover-your-rules-template-version-number"></a>Ermitteln der Vorlagenversionsnummer Ihrer Regel

Durch Implementierung der Vorlagenversionskontrolle können Sie die Versionen Ihrer Regelvorlagen und die daraus erstellten Regeln sehen und nachverfolgen. Für Regeln, deren Vorlagen aktualisiert wurden, wird neben dem Regelnamen der Badge „*Update verfügbar*“ angezeigt.

1. Wählen Sie auf dem Blatt **Analysen** die Registerkarte **Aktive Regeln** aus.

1. Wählen Sie eine beliebige Regel vom Typ **Geplant** aus.  

    - Wenn für die Regel der Badge „*Update verfügbar*“ anzeigt wird, weist der Detailbereich neben der Schaltfläche **Bearbeiten** eine Schaltfläche **Überprüfen und aktualisieren** auf (siehe Abbildung 1 im nächsten Schritt unten).

    - Wenn die Regel aus einer Vorlage erstellt wurde, aber nicht den Badge „*Update verfügbar*“ aufweist, wird im Detailbereich neben der Schaltfläche **Bearbeiten** eine Schaltfläche **Mit Vorlage vergleichen** angezeigt (siehe Abbildungen 2 und 3 im nächsten Schritt unten).

    - Wenn nur die Schaltfläche **Bearbeiten** angezeigt wird, wurde die Regel von Grund auf neu und nicht aus einer Vorlage erstellt.

        :::image type="content" source="media/manage-analytics-rule-templates/see-rules-with-updated-template.png" alt-text="Screenshot der Liste der aktiven Regeln mit einem Badge, der angibt, dass ein Vorlagenupdate verfügbar ist" lightbox="media/manage-analytics-rule-templates/see-rules-with-updated-template.png":::

1. Scrollen Sie nach unten zum Ende des Detailbereichs, wo zwei Versionsnummern angezeigt werden: die Version der Vorlage, aus der die Regel erstellt wurde, und die neueste verfügbare Vorlagenversion. 

    :::image type="content" source="media/manage-analytics-rule-templates/see-template-versions.png" alt-text="Screenshot des Detailbereichs. Scrollen Sie nach unten, um die Vorlagenversionsnummern zu sehen." border="false":::

    Die Nummer hat das Format „1.0.0“: Hauptversion, Nebenversion und Build.  
    (Derzeit wird die Buildnummer nicht verwendet und ist immer 0.)

    - Eine Abweichung bei der *Hauptversionsnummer* weist darauf hin, dass etwas Wesentliches in der Vorlage geändert wurde, was sich auf die Art und Weise, wie die Regel Bedrohungen erkennt, oder sogar auf ihre Funktionsfähigkeit insgesamt auswirken könnte. Dies ist eine Änderung, die Sie in Ihre Regeln aufnehmen sollten.

    - Eine Abweichung bei der *Nebenversionsnummer* weist auf eine geringfügige Verbesserung in der Vorlage hin (eine kosmetische Änderung oder etwas Ähnliches), die ganz vorteilhaft wäre, aber für die Aufrechterhaltung der Funktionalität, Effektivität oder Leistung der Regel nicht entscheidend ist. Dies ist eine Änderung, die Sie genauso gut übernehmen oder auch auslassen können.

    > [!NOTE]
    > Die Abbildungen 2 und 3 oben zeigen zwei Beispiele für Regeln, die aus Vorlagen erstellt wurden und bei denen die Vorlage nicht aktualisiert wurde.
    > - Abbildung 2 zeigt eine Regel mit einer Versionsnummer für die aktuelle Vorlage. Dies deutet darauf hin, dass die Regel nach der ersten Implementierung der Vorlagenversionskontrolle durch Microsoft Sentinel im Oktober 2021 erstellt wurde.
    > - Abbildung 3 zeigt eine Regel ohne aktuelle Vorlagenversion. Dies zeigt, dass die Regel vor Oktober 2021 erstellt wurde. Wenn eine neueste Vorlagenversion verfügbar ist, handelt es sich wahrscheinlich um eine neuere Version als die, die zum Erstellen der Regel verwendet wurde.

## <a name="compare-your-active-rule-with-its-template"></a>Vergleichen Ihrer aktiven Regel mit der jeweiligen Vorlage

Wählen Sie je nach gewünschter Aktion eine der folgenden Registerkarten aus, um die Anweisungen für diese Aktion anzuzeigen:

# <a name="update-template"></a>[Aktualisieren der Vorlage](#tab/update)

Nachdem Sie eine Regel ausgewählt und festgestellt haben, dass Sie sie aktualisieren möchten, wählen Sie im Detailbereich die Option **Überprüfen und aktualisieren** aus (siehe oben). Daraufhin enthält der **Analyseregel-Assistent** die Registerkarte **Mit der neuesten Version vergleichen**.

Auf dieser Registerkarte wird ein direkter Vergleich zwischen den YAML-Darstellungen der vorhandenen Regel und der neuesten Version der Vorlage angezeigt. 

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions.png" alt-text="Screenshot der Registerkarte „Mit der neuesten Version vergleichen“ im Analyseregel-Assistenten":::

> [!NOTE]
> Durch Aktualisieren dieser Regel wird Ihre vorhandene Regel mit der neuesten Version der Vorlage überschrieben.

Alle Automatisierungsschritte oder -logiken, die auf die vorhandene Regel verweisen, sollten überprüft werden, falls sich die Namen, auf die verwiesen wird, geändert haben. Außerdem können alle Anpassungen, die Sie beim Erstellen der ursprünglichen Regel vorgenommen haben ( Änderungen an der Abfrage, Planung, Gruppierung oder anderen Einstellungen), überschrieben werden.

### <a name="update-your-rule-with-the-new-template-version"></a>Aktualisieren Ihrer Regel mit der neuen Vorlagenversion

- Wenn die an der neuen Vorlagenversion vorgenommenen Änderungen für Sie akzeptabel sind und keine weiteren Aspekte Ihrer ursprünglichen Regel betroffen sind, wählen Sie **Überprüfen und aktualisieren** aus, um die Änderungen zu überprüfen und anzuwenden. 

- Wenn Sie die Regel weiter anpassen oder Änderungen erneut anwenden möchten, die andernfalls überschrieben werden könnten, wählen Sie **Weiter: Benutzerdefinierte Änderungen** aus. Wenn Sie diese Option auswählen, durchlaufen Sie die übrigen Registerkarten des [Analyseregel-Assistenten](detect-threats-custom.md), um diese Änderungen vorzunehmen. Anschließend überprüfen und übernehmen Sie die Änderungen auf der Registerkarte **Überprüfen und aktualisieren**.

- Wenn Sie keine Änderungen an Ihrer bestehenden Regel vornehmen wollen, sondern die vorhandene Vorlagenversion beibehalten möchten, beenden Sie einfach den Assistenten, indem Sie das X in der oberen rechten Ecke auswählen.

# <a name="revert-to-template"></a>[Zurücksetzen auf Vorlage](#tab/revert)

Nachdem Sie eine Regel ausgewählt und festgestellt haben, dass Sie sie auf die ursprüngliche Version zurücksetzen möchten, wählen Sie im Detailbereich die Option **Mit Vorlage vergleichen** aus (siehe oben). Daraufhin enthält der **Analyseregel-Assistent** die Registerkarte **Mit der neuesten Version vergleichen**.

Auf dieser Registerkarte wird ein direkter Vergleich zwischen den YAML-Darstellungen der vorhandenen Regel und der neuesten Version der Vorlage angezeigt. Diese beiden Versionsnummern sind möglicherweise identisch, doch wird auf der linken Seite die aktive Regel einschließlich aller Änderungen angezeigt, die während oder nach der Erstellung aus der Vorlage vorgenommen wurden, während auf der rechten Seite die unveränderte Vorlage angezeigt wird.

:::image type="content" source="media/manage-analytics-rule-templates/compare-template-versions-2.png" alt-text="Screenshot der Registerkarte „Mit der neuesten Version vergleichen“ im Analyseregel-Assistenten":::

> [!NOTE]
> Durch Aktualisieren dieser Regel wird Ihre vorhandene Regel mit der neuesten Version der Vorlage überschrieben.
Alle Automatisierungsschritte oder -logiken, die auf die vorhandene Regel verweisen, sollten überprüft werden, falls sich die Namen, auf die verwiesen wird, geändert haben. Außerdem können alle Anpassungen, die Sie beim Erstellen der ursprünglichen Regel vorgenommen haben ( Änderungen an der Abfrage, Planung, Gruppierung oder anderen Einstellungen), überschrieben werden.

### <a name="revert-your-rule-to-its-original-template-version"></a>Zurücksetzen Ihrer Regel auf die ursprüngliche Vorlagenversion

- Wenn Sie vollständig zur ursprünglichen Version dieser Regel (einer bereinigten Kopie der Vorlage) zurückkehren möchten, wählen Sie **Überprüfen und aktualisieren** aus, um die Änderungen zu überprüfen und anzuwenden. 

- Wenn Sie die Regel anders anpassen oder Änderungen erneut anwenden möchten, die andernfalls überschrieben werden könnten, wählen Sie **Weiter: Benutzerdefinierte Änderungen** aus. Wenn Sie diese Option auswählen, durchlaufen Sie die übrigen Registerkarten des [Analyseregel-Assistenten](detect-threats-custom.md), um diese Änderungen vorzunehmen. Anschließend überprüfen und übernehmen Sie die Änderungen auf der Registerkarte **Überprüfen und aktualisieren**.

- Wenn Sie keine Änderungen an Ihrer vorhandenen Regel vornehmen möchten, beenden Sie einfach den Assistenten, indem Sie oben rechts das X auswählen.

---

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie die Versionen Ihrer Microsoft Sentinel-Analyseregelvorlagen nachverfolgen und entweder aktive Regeln auf vorhandene Vorlagenversionen zurücksetzen oder auf neue Versionen aktualisieren können. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:

- Weitere Informationen zu [Analyseregeln](detect-threats-built-in.md).
- Weitere Informationen zum [Analyseregel-Assistenten](detect-threats-custom.md).
