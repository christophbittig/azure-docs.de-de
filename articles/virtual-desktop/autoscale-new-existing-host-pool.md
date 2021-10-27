---
title: Azure Virtual Desktop-Skalierungspläne für Hostpools (Vorschau)
description: Zuweisen von Skalierungsplänen zu neuen oder vorhandenen Hostpools in Ihrer Bereitstellung
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b61869e09003456f55a6d69cae01356a06bc633d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181647"
---
# <a name="enable-scaling-plans-for-existing-and-new-host-pools-preview"></a>Aktivieren von Skalierungsplänen für vorhandene und neue Hostpools (Vorschau)

> [!IMPORTANT]
> Die automatische Skalierung ist derzeit im Vorschaustadium verfügbar.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Sie können Skalierungspläne für alle vorhandenen Hostpools in Ihrer Bereitstellung aktivieren. Wenn Sie Ihren Skalierungsplan auf den Hostpool anwenden, gilt der Plan auch für alle Sitzungshosts innerhalb dieses Hostpools. Die Skalierung gilt auch automatisch für alle neuen Sitzungshosts, die Sie in Ihrem zugewiesenen Hostpool erstellen.

Wenn Sie einen Skalierungsplan deaktivieren, verbleiben alle zugewiesenen Ressourcen in dem Skalierungsstatus, den sie zum Zeitpunkt ihrer Deaktivierung hatten.

## <a name="assign-a-scaling-plan-to-an-existing-host-pool"></a>Zuweisen eines Skalierungsplans zu einem vorhandenen Hostpool

So weisen Sie einem vorhandenen Hostpool einen Skalierungsplan zu:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Wechseln Sie zu **Windows Virtual Desktop**.

3. Wählen Sie **Hostpools** aus, klicken Sie dann auf **Skalierungsplan**, und wählen Sie **Neu** aus.

4. Wählen Sie **Skalierungsplan** und dann **+ Zuweisen** aus, um einem nicht zugewiesenen Hostpool wie im folgenden Screenshot gezeigt einen Skalierungsplan zuzuweisen.

    > [!div class="mx-imgBorder"]
    > ![Screenshot des Skalierungsplan-Fensters Die Schaltfläche „+ Hinzufügen“ in der linken oberen Ecke ist mit einem roten Rahmen hervorgehoben.](media/assign-scaling-plan.png)

    Wenn Sie den Skalierungsplan während der Bereitstellung aktiviert haben, haben Sie auch die Möglichkeit, den Plan für den ausgewählten Hostpool im Menü **Skalierungsplan** zu deaktivieren, indem Sie das Kontrollkästchen **Skalierungsplan aktivieren** deaktivieren, wie im folgenden Screenshot gezeigt.

    > [!div class="mx-imgBorder"]
    > ![Screenshot des Fensters „Skalierungsplan“ Das Kontrollkästchen „Autoskalierung aktivieren“ ist ausgewählt und mit einem roten Rahmen hervorgehoben.](media/enable-autoscale.png)

## <a name="edit-an-existing-scaling-plan"></a>Bearbeiten eines vorhandenen Skalierungsplans

So bearbeiten Sie einen vorhandenen Skalierungsplan:

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.

2. Wechseln Sie zu **Azure Virtual Desktop**.

3. Wählen Sie **Skalierungsplan** und dann den Namen des Skalierungsplans aus, den Sie bearbeiten möchten. Das Einstellungsfenster wird geöffnet.

4. Um den Anzeigenamen, die Beschreibung, die Zeitzone oder Ausschlusstags des Plans zu bearbeiten, wechseln Sie zur Registerkarte **Eigenschaften**.

5. Um Hostpools zuzuweisen oder Zeitpläne zu bearbeiten, wechseln Sie in die Registerkarte **Verwalten**.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen eines Skalierungsplans finden Sie unter [Automatische Skalierung (Vorschau) für Azure Virtual Desktop-Sitzungshosts](autoscale-new-existing-host-pool.md).
- Informationen zur Problembehandlung bei Ihrem Skalierungsplan finden Sie unter [Aktivieren der Diagnose für Ihren Skalierungsplan](autoscale-diagnostics.md).