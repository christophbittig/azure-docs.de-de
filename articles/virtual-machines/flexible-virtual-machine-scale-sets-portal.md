---
title: Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure CLI
description: Erfahren Sie, wie Sie im Orchestrierungsmodus „Flexibel“ eine VM-Skalierungsgruppe im Azure-Portal erstellen.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 5d25e46ae62962e3f94d4a3f9222f91ebfa1d430
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823450"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Preview: Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure CLI

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen

In diesem Artikel wird beschrieben, wie Sie das Azure-Portal verwenden sollten, um eine VM-Skalierungsgruppe im flexiblen Orchestrierungsmodus zu erstellen. Weitere Informationen über flexible Skalierungsgruppen finden Sie unter [Flexibler Orchestrierungsmodus für Skalierungsgruppen für VM](flexible-virtual-machine-scale-sets.md). 


> [!IMPORTANT]
> Das Feature für VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ befindet sich derzeit in der öffentlichen Vorschauphase. Es ist ein Opt-in-Verfahren erforderlich, um die unten beschriebenen Funktionen der öffentlichen Vorschauversion zu nutzen.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="register-for-flexible-orchestration-mode"></a>Registrieren für den Orchestrierungsmodus „Flexibel“

Bevor Sie VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ bereitstellen können, müssen Sie Ihr Abonnement zunächst für die Previewfunktion registrieren. Die Featureregistrierung kann bis zu 15 Minuten dauern.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Navigieren Sie zu Ihren **Abonnements**.
1. Navigieren Sie zur Detailseite für das Abonnement, für das Sie eine Skalierungsgruppe im Modus „Flexible Orchestrierung“ erstellen möchten, indem Sie den Namen des Abonnements auswählen.
1. Wählen Sie im Menü unter **Einstellungen** die Option **Previewfunktionen** aus.
1. Wählen Sie die folgenden vier zu aktivierenden Orchestrator-Funktionen aus: *VMOrchestratorSingleFD*, *VMOrchestratorMultiFD*, *VMScaleSetFlexPreview*, and *SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*.
1. Wählen Sie **Registrieren**.

Nachdem Sie die Features für Ihr Abonnement registriert haben, schließen Sie den Opt-in-Prozess ab, indem Sie die Änderung an den Computeressourcenanbieter weitergeben. 

1. Wählen Sie im Menü unter **Einstellungen** die Option **Ressourcenanbieter** aus.
1. Wählen Sie `Microsoft.compute` aus.
1. Wählen Sie **Erneut registrieren** aus.


## <a name="get-started-with-flexible-orchestration-mode"></a>Erste Schritte mit dem Orchestrierungsmodus „Flexibel“

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Erstellen Sie im Orchestrierungsmodus „Flexibel“ eine VM-Skalierungsgruppe über das Azure-Portal.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Suchen Sie in der Suchleiste nach **VM-Skalierungsgruppen**, und wählen Sie den Eintrag dann aus.
1. Wählen Sie auf der Seite **VM-Skalierungsgruppen** die Option **Erstellen** aus.
1. Zeigen Sie auf der Seite **VM-Skalierungsgruppe erstellen** den Abschnitt **Orchestration** an.
1. Wählen Sie unter **Orchestrierungsmodus** die Option **Flexibel** aus.
1. Legen Sie für **Anzahl von Fehlerdomänen** den gewünschten Wert fest.
1. Schließen Sie die Erstellung Ihrer Skalierungsgruppe ab. Weitere Informationen zum Erstellen einer Skalierungsgruppe finden Sie unter [Erstellen einer Skalierungsgruppe im Azure-Portal](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set).


### <a name="next-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>Fügen Sie als Nächstes der Skalierungsgruppe im Orchestrierungsmodus „Flexibel“ einen virtuellen Computer hinzu.

1. Suchen Sie in der Suchleiste nach **Virtuelle Computer**, und wählen Sie den Eintrag dann aus.
1. Wählen Sie auf der Seite **Virtuelle Computer** die Option **Hinzufügen** aus.
1. Zeigen Sie auf der Registerkarte **Grundlagen** den Abschnitt mit den **Details zur Instanz** an.
1. Fügen Sie Ihre VM der Skalierungsgruppe im Orchestrierungsmodus „Flexibel“ hinzu, indem Sie die Skalierungsgruppe unter **Verfügbarkeitsoptionen** auswählen. Sie können den virtuellen Computer einer Skalierungsgruppe in derselben Region, Zone und Ressourcengruppe hinzufügen.
1. Navigieren Sie zu der Registerkarte **Netzwerk** und legen Sie Ihre ausgehende Konnektivität fest.

    > [!IMPORTANT]
    > Eine explizit definierte ausgehende Konnektivität ist für VM-Skalierungsgruppen mit flexibler Orchestrierung erforderlich. Mehr Informationen finden Sie unter [Explizite ausgehende Netzwerkkonnektivität](flexible-virtual-machine-scale-sets.md#explicit-network-outbound-connectivity-required).

1. Schließen Sie die Erstellung Ihres virtuellen Computers ab.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie eine flexible Skalierung mit Azure CLI erstellen.](flexible-virtual-machine-scale-sets-cli.md)