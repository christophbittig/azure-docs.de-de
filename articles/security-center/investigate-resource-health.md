---
title: 'Tutorial: Untersuchen der Integrität Ihrer Ressourcen – Microsoft Defender für Cloud'
description: 'Tutorial: Erfahren Sie, wie Sie die Integrität Ihrer Ressourcen mit Microsoft Defender für Cloud untersuchen.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f156b8f1102e55ca56085aec7b63a1fdb2716450
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075802"
---
# <a name="tutorial-investigate-the-health-of-your-resources"></a>Tutorial: Untersuchen der Integrität Ihrer Ressourcen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!NOTE]
> Bei der in diesem Tutorial beschriebenen Ressourcenintegritätsseite handelt es sich um eine Vorschauversion.
> 
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] | |Preise:|**Microsoft Defender für DNS** wird gemäß den Angaben in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.

Die Ressourcenintegritätsseite zeigt eine Momentaufnahme der allgemeinen Integrität einer einzelnen Ressource. Sie können ausführliche Informationen zur Ressource und sich alle Empfehlungen im Zusammenhang mit der Ressource ansehen. Wenn Sie einen der [erweiterten Schutzpläne von Microsoft Defender](defender-for-cloud-introduction.md) verwenden, werden Ihnen außerdem sehr nützliche Sicherheitswarnungen für die jeweilige bestimmte Ressource gezeigt.

Diese einzelne, aktuell in der Vorschauphase befindliche Seite des Defender für Cloud-Portals enthält Folgendes:

1. **Ressourceninformationen:** Zugehörige Ressourcengruppe, zugehöriges Abonnement, geografischer Standort und Ähnliches.
1. **Angewendetes Sicherheitsfeature**: Gibt an, ob der Microsoft Defender-Plan für die Ressource aktiviert ist.
1. **Anzahl ausstehenden Empfehlungen und Warnungen**: Die Anzahl ausstehender Sicherheitsempfehlungen und Defender für Cloud-Warnungen.
1. **Umsetzbare Empfehlungen und handlungsrelevante Warnungen:** Auf zwei Registerkarten werden die Empfehlungen und Warnungen für die Ressource aufgeführt.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="Ressourcenintegritätsseite von Microsoft Defender für Cloud mit den Integritätsinformationen für einen virtuellen Computer":::

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Zugreifen auf die Ressourcenintegritätsseite für alle Ressourcentypen
> * Auswerten der ausstehenden Sicherheitsprobleme für eine Ressource
> * Verbessern des Sicherheitsstatus für die Ressource

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchlaufen der in diesem Tutorial behandelten Features müssen folgende Voraussetzungen erfüllt sein:

- Azure-Abonnement: Sollten Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Um Sicherheitsempfehlungen anwenden zu können, müssen Sie mit einem Konto angemeldet sein, das über die entsprechenden Berechtigungen verfügt (Ressourcengruppenmitwirkender, Ressourcengruppenbesitzer, Abonnementmitwirkender oder Abonnementbesitzer).
- Um Warnungen verwerfen zu können, müssen Sie mit einem Konto angemeldet sein, das über die entsprechenden Berechtigungen verfügt (Sicherheitsadministrator, Abonnementmitwirkender oder Abonnementbesitzer).

##  <a name="access-the-health-information-for-a-resource"></a>Zugreifen auf die Integritätsinformationen für eine Ressource

> [!TIP]
> In den folgenden Screenshots wird zwar ein virtueller Computer geöffnet, auf der Ressourcenintegritätsseite können jedoch Details für alle Ressourcentypen angezeigt werden. 

So öffnen Sie die Ressourcenintegritätsseite für eine Ressource:

1. Wählen Sie auf der [Seite „Ressourcenbestand“](asset-inventory.md) eine beliebige Ressource aus.

    :::image type="content" source="media/investigate-resource-health/inventory-select-resource.png" alt-text="Auswählen einer Ressource aus dem Ressourcenbestand, um die Ressourcenintegritätsseite anzuzeigen" lightbox="./media/investigate-resource-health/inventory-select-resource.png":::

1. Im linken Bereich der Ressourcenintegritätsseite finden Sie eine Übersicht über die Abonnement-, Status- und Überwachungsinformationen für die Ressource. Sie können auch sehen, ob erweiterte Sicherheitsfeatures für die Ressource aktiviert sind:

    :::image type="content" source="media/investigate-resource-health/resource-health-left-pane.png" alt-text="Im linken Bereich der Ressourcenintegritätsseite von Microsoft Defender für Cloud werden die Abonnement-, Status- und Überwachungsinformationen für die Ressource angezeigt. Außerdem finden Sie hier die Gesamtanzahl ausstehender Sicherheitsempfehlungen und -warnungen.":::

1. Überprüfen Sie auf den beiden Registerkarten im rechten Bereich die Listen der Sicherheitsempfehlungen und -warnungen für diese Ressource:

    :::image type="content" source="media/investigate-resource-health/resource-health-right-pane.png" alt-text="Im rechten Bereich der Ressourcenintegritätsseite von Microsoft Defender für Cloud befinden sich zwei Registerkarten: eine für Empfehlungen und eine für Warnungen." lightbox="./media/investigate-resource-health/resource-health-right-pane.png":::

    > [!NOTE]
    > In Microsoft Defender für Cloud wird der Sicherheitsstatus einer Ressource mit den Begriffen „fehlerfrei“ und „fehlerhaft“ beschrieben. Diese Begriffe beziehen sich darauf, ob die Ressource mit einer bestimmten [Sicherheitsempfehlung](security-policy-concept.md#what-is-a-security-recommendation) konform ist.
    >
    > Im obigen Screenshot sehen Sie, dass auch Empfehlungen aufgeführt werden, wenn die Ressource fehlerfrei ist. Ein Vorteil der Ressourcenintegritätsseite besteht darin, dass alle Empfehlungen aufgeführt werden, damit Sie sich einen vollständigen Überblick über die Integrität Ihrer Ressourcen verschaffen können. 


## <a name="evaluate-the-outstanding-security-issues-for-a-resource"></a>Auswerten der ausstehenden Sicherheitsprobleme für eine Ressource

Auf der Ressourcenintegritätsseite sind die Empfehlungen, für die Ihre Ressource den Status „Fehlerhaft“ aufweist, sowie die aktiven Warnungen aufgeführt. 

- Beheben Sie die in den Empfehlungen beschriebenen Probleme, um sicherzustellen, dass Ihre Ressource gemäß den für Ihre Abonnements geltenden Richtlinien gehärtet ist:
    1. Wählen Sie auf der rechten Seite eine Empfehlung aus.
    1. Fahren Sie gemäß den Anweisungen auf dem Bildschirm fort.

        > [!TIP]
        > Die Anweisungen zum Beheben von Problemen aus Sicherheitsempfehlungen sind bei jeder Defender für Cloud-Empfehlung anders.
        >
        > Sehen Sie sich den Schweregrad jeder Empfehlung sowie deren [mögliche Auswirkung auf Ihre Sicherheitsbewertung](secure-score-security-controls.md#security-controls-and-their-recommendations) an, um zu entscheiden, welche Empfehlungen zuerst umgesetzt werden sollten.

- So untersuchen Sie eine Sicherheitswarnung
    1. Wählen Sie im rechten Bereich eine Warnung aus.
    1. Gehen Sie wie unter [Reagieren auf Sicherheitswarnungen](managing-and-responding-alerts.md#respond-to-security-alerts) beschrieben vor.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie die Ressourcenintegritätsseite von Defender für Cloud verwenden.

Weitere Informationen finden Sie auf den folgenden verwandten Seiten:

- [Reagieren auf Sicherheitswarnungen](managing-and-responding-alerts.md#respond-to-security-alerts)
- [Überprüfen Ihrer Sicherheitsempfehlungen](review-security-recommendations.md)
