---
title: Konfigurieren der Einstellungen für den automatischen Start bei einem virtuellen Computer
description: Erfahren Sie, wie Sie Einstellungen für den automatischen Start bei virtuellen Computern in einem Lab konfigurieren. Mit dieser Einstellung können virtuelle Computer im Lab nach einem Zeitplan automatisch gestartet werden.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: ea4220af8a3d7ade997b9e64b7164757edfc165a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286714"
---
# <a name="start-up-lab-virtual-machines-automatically"></a>Automatisches Starten virtueller Lab-Computer

Mit dem automatischen Start können Sie virtuelle Computer (virtual machines, VMs) in einem Lab täglich automatisch zu einem geplanten Zeitpunkt starten. Zunächst müssen Sie eine Richtlinie für den automatischen Start erstellen. Anschließend müssen Sie auswählen, für welche VMs die Richtlinie gelten soll. Der zusätzliche Schritt der aktiven Auswahl von virtuellen Computern für den automatischen Start soll das nicht gewollte Starten von einzelnen virtuellen Computern verhindern, was zu höheren Kosten führt.

In diesem Artikel erfahren Sie, wie Sie die Richtlinie für den automatischen Start für ein Lab konfigurieren. Weitere Informationen zum Konfigurieren der Einstellungen für das automatische Herunterfahren finden Sie unter [Verwalten von Richtlinien für das automatische Herunterfahren für ein Lab in Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

## <a name="configure-auto-start-settings-for-a-lab"></a>Konfigurieren der Einstellungen für den automatischen Start für ein Lab 

Mit der Richtlinie wird der automatische Start nicht automatisch auf virtuelle Computer im Lab angewendet. Führen Sie nach dem Konfigurieren der Richtlinie die Schritte unter [Aktivieren des automatischen Starts für einen virtuellen Computer im Lab](#enable-auto start-for-a-vm-in-the-lab) aus.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie in **DevTest Labs** zu Ihrem Lab.

1. Wählen Sie **Konfiguration und Richtlinien** unter **Einstellungen** aus. 

   :::image type="content" source="./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png" alt-text="Screenshot des Menüs „Konfiguration und Richtlinien“ in DevTest Labs":::

1. Wählen Sie auf der Seite **Konfiguration und Richtlinien** unter **Zeitpläne** die Option **Automatischer Start** aus.

1. Wählen Sie **Ja** aus, um **den automatischen Start zuzulassen**. Anschließend werden Planungsinformationen angezeigt.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/portal-lab-auto-start.png" alt-text="Screenshot der Option „Automatischer Start“ unter „Zeitpläne“":::
 
1. Geben Sie die folgenden Planungsinformationen an:

    |Eigenschaft | Beschreibung |
    |---|---|
    |Geplanter Start| Geben Sie eine Startzeit ein.|
    |Zeitzone| Wählen Sie in der Dropdownliste eine Zeitzone aus.|
    |Wochentage| Wählen Sie die Felder neben den Tagen aus, an denen der Zeitplan angewendet werden soll.|

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/auto-start-configuration.png" alt-text="Screenshot der Zeitplaneinstellungen für den automatischen Start":::

1. Wählen Sie **Speichern** aus. 

## <a name="enable-auto-start-for-a-vm-in-the-lab"></a>Aktivieren des automatischen Starts für einen virtuellen Computer im Lab

Diese Schritte sind eine Fortsetzung des vorherigen Abschnitts. Nachdem Sie eine Richtlinie für den automatischen Start erstellt haben, wählen Sie die virtuellen Computer aus, auf die die Richtlinie angewendet werden soll.

1. Schließen Sie die Seite **Konfiguration und Richtlinien**, um zur Seite **DevTest Labs** zurückzukehren.

1. Wählen Sie unter **Meine virtuellen Computer** eine VM aus.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-vm.png" alt-text="Screenshot der Option „VM aus Liste auswählen“ unter „Meine virtuellen Computer“":::

1. Wählen Sie auf der Seite des **virtuellen Computers** unter **Vorgänge** die Option **Automatischer Start** aus. 

1. Wählen Sie auf der Seite **Automatischer Start** die Option **Ja** und dann **Speichern** aus.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-auto-start.png" alt-text="Screenshot des Menüs „Autostart auswählen“":::

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Richtlinien zum automatischen Herunterfahren für ein Lab in Azure DevTest Labs](devtest-lab-auto-shutdown.md)
