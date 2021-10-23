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
ms.openlocfilehash: 9804b8cd773eec6df8a8b7a5b06e61ee110987b3
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163572"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure CLI

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen

In diesem Artikel wird beschrieben, wie Sie das Azure-Portal verwenden sollten, um eine VM-Skalierungsgruppe im flexiblen Orchestrierungsmodus zu erstellen. Weitere Informationen über flexible Skalierungsgruppen finden Sie unter [Flexibler Orchestrierungsmodus für Skalierungsgruppen für VM](flexible-virtual-machine-scale-sets.md). 


> [!CAUTION]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="get-started-with-flexible-orchestration-mode"></a>Erste Schritte mit dem Orchestrierungsmodus „Flexibel“

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Erstellen Sie im Orchestrierungsmodus „Flexibel“ eine VM-Skalierungsgruppe über das Azure-Portal.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Suchen Sie in der Suchleiste nach **VM-Skalierungsgruppen**, und wählen Sie den Eintrag dann aus.
1. Wählen Sie auf der Seite **VM-Skalierungsgruppen** die Option **Erstellen** aus.
1. Zeigen Sie auf der Seite **VM-Skalierungsgruppe erstellen** den Abschnitt **Orchestration** an.
1. Wählen Sie unter **Orchestrierungsmodus** die Option **Flexibel** aus.
1. Legen Sie für **Anzahl von Fehlerdomänen** den gewünschten Wert fest.
1. Schließen Sie die Erstellung Ihrer Skalierungsgruppe ab. Weitere Informationen zum Erstellen einer Skalierungsgruppe finden Sie unter [Erstellen einer Skalierungsgruppe im Azure-Portal](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set).


### <a name="optional-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>(Optional) Fügen Sie der Skalierungsgruppe im Orchestrierungsmodus „Flexibel“ einen virtuellen Computer hinzu.

1. Suchen Sie in der Suchleiste nach **Virtuelle Computer**, und wählen Sie den Eintrag dann aus.
1. Wählen Sie auf der Seite **Virtuelle Computer** die Option **Hinzufügen** aus.
1. Zeigen Sie auf der Registerkarte **Grundlagen** den Abschnitt mit den **Details zur Instanz** an.
1. Fügen Sie Ihre VM der Skalierungsgruppe im Orchestrierungsmodus „Flexibel“ hinzu, indem Sie die Skalierungsgruppe unter **Verfügbarkeitsoptionen** auswählen. Sie können den virtuellen Computer einer Skalierungsgruppe in derselben Region, Zone und Ressourcengruppe hinzufügen.
1. Navigieren Sie zu der Registerkarte **Netzwerk** und legen Sie Ihre ausgehende Konnektivität fest.

    > [!IMPORTANT]
    > Eine explizit definierte ausgehende Konnektivität ist für VM-Skalierungsgruppen mit flexibler Orchestrierung erforderlich. Mehr Informationen finden Sie unter [Explizite ausgehende Netzwerkkonnektivität](flexible-virtual-machine-scale-sets-migration-resources.md#explicit-network-outbound-connectivity-required).

1. Schließen Sie die Erstellung Ihres virtuellen Computers ab.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie eine flexible Skalierung mit Azure CLI erstellen.](flexible-virtual-machine-scale-sets-cli.md)