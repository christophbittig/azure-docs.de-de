---
title: 'Schnellstart: Aktivieren der automatischen Azure-Verwaltung für virtuelle Computer über das Azure-Portal'
description: Hier erfahren Sie, wie Sie über das Azure-Portal schnell die automatische Verwaltung für virtuelle Computer auf einem neuen oder vorhandenen virtuellen Computer aktivieren.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom: mode-portal
ms.openlocfilehash: ab9a6f5ff85fe0b5b2d1df4d2a3f64bb4cb21207
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465679"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Schnellstart: Aktivieren der automatischen Azure-Verwaltung für virtuelle Computer im Azure-Portal

Beginnen Sie mit der automatischen Azure-Verwaltung für virtuelle Computer, indem Sie über das Azure-Portal die automatische Verwaltung für einen neuen oder vorhandenen virtuellen Computer aktivieren.


## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), bevor Sie beginnen.

> [!NOTE]
> Mit kostenlosen Testkonten ist kein Zugriff auf die virtuellen Computer möglich, die in diesem Tutorial verwendet werden. Führen Sie ein Upgrade auf ein Abonnement mit nutzungsbasierter Zahlung durch.

> [!IMPORTANT]
> Sie müssen über die Rolle **Mitwirkender** für die Ressourcengruppe verfügen, die Ihre virtuellen Computer enthält, um Automanage zu aktivieren. Wenn Sie Automanage zum ersten Mal für ein Abonnement aktivieren, benötigen Sie die folgenden Berechtigungen: Rolle **Besitzer** oder **Mitwirkender** sowie Rollen vom Typ **Benutzerzugriffsadministrator** für Ihr Abonnement.


## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

## <a name="enable-automanage-on-existing-machines"></a>Aktivieren von Automanage auf vorhandenen Computern

1. Suchen Sie über die Suchleiste nach **Automanage – Best Practices für Azure-VMs**, und wählen Sie die Option aus.

2. Wählen Sie **Für vorhandene VM aktivieren** aus.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Für vorhandene VM aktivieren":::

4. Wählen Sie unter **Konfigurationsprofil** Ihren Profiltyp aus: **Azure Best Practices – Produktion**, **Azure Best Practices – Dev/Test** oder [**Benutzerdefiniertes Profil**](virtual-machines-custom-profile.md).

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Auswählen der Umgebung":::

   Klicken Sie auf **Azure Best Practices-Profile anzeigen**, um die Unterschiede zwischen den Umgebungen anzuzeigen.
    1. Wählen Sie in der Dropdownliste eine Umgebung aus: *Dev/Test* für Tests oder *Produktion* für die Produktion.
    1. Klicken Sie auf die Schaltfläche **OK** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Durchsuchen der Produktionsumgebung":::

5. Gehen Sie auf dem Blatt **Computer auswählen** wie folgt vor:
    1. Filtern Sie die Liste nach Ihrem **Abonnement** und der **Ressourcengruppe**.
    1. Aktivieren Sie das Kontrollkästchen für jeden virtuellen Computer, den Sie integrieren möchten.
    1. Klicken Sie auf die Schaltfläche **Auswählen**.
    > [!NOTE]
    > Sie können sowohl Azure-VMs als auch Server mit Azure Arc-Unterstützung auswählen.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Auswählen eines vorhandenen virtuellen Computers aus der Liste verfügbarer virtueller Computer":::


6. Klicken Sie auf die Schaltfläche **Aktivieren**.


## <a name="disable-automanage-for-vms"></a>Deaktivieren der automatischen Verwaltung für virtuelle Computer

Sie können die Verwendung der automatischen Azure-Verwaltung für virtuelle Computer schnell beenden, indem Sie die automatische Verwaltung deaktivieren.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Deaktivieren der automatischen Verwaltung auf einem virtuellen Computer":::

1. Navigieren Sie zur Seite **Automatische Verwaltung – Best Practices für Azure-VMs**, auf der alle automatisch verwalteten virtuellen Computer aufgeführt sind.
1. Aktivieren Sie das Kontrollkästchen neben dem virtuellen Computer, den Sie deaktivieren möchten.
1. Klicken Sie auf die Schaltfläche **Automatische Verwaltung deaktivieren**.
1. Lesen Sie die Meldung im angezeigten Popupelement sorgfältig durch, bevor Sie dem **Deaktivieren** zustimmen.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine neue Ressourcengruppe erstellt haben, um die automatische Azure-Verwaltung für virtuelle Computer zu testen, und die Ressourcengruppe nicht mehr benötigen, können Sie sie löschen. Wenn Sie die Gruppe löschen, werden auch der virtuelle Computer und alle Ressourcen in der Ressourcengruppe gelöscht.

Die automatische Azure-Verwaltung erstellt Standardressourcengruppen zum Speichern von Ressourcen. Überprüfen Sie die Ressourcengruppen mit der Namenskonvention „DefaultResourceGroupRegionName“ und „AzureBackupRGRegionName“, um alle Ressourcen zu bereinigen.

1. Wählen Sie die **Ressourcengruppe** aus.
1. Wählen Sie auf der Seite für die Ressourcengruppe die Option **Löschen** aus.
1. Geben Sie bei entsprechender Aufforderung den Namen der Ressourcengruppe ein, und wählen Sie dann **Löschen** aus.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie die automatische Azure-Verwaltung für virtuelle Computer aktiviert.

Erfahren Sie, wie Sie angepasste Profile erstellen und anwenden können, wenn Sie Automanage auf virtuellen Computern aktivieren.

> [!div class="nextstepaction"]
> [Erstellen eines benutzerdefinierten Profils in Azure Automanage für virtuelle Computer](virtual-machines-custom-profile.md)
