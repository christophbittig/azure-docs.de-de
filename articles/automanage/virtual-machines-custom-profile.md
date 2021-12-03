---
title: Erstellen eines benutzerdefinierten Profils in der automatischen Azure-Verwaltung für VMs
description: Erfahren Sie, wie Sie ein benutzerdefiniertes Profil in Azure Automanage erstellen und Ihre Dienste und Einstellungen auswählen.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 821ad720645c433b9a487383e5ed9edb5bc21e61
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478896"
---
# <a name="create-a-custom-profile-in-azure-automanage-for-vms"></a>Erstellen eines benutzerdefinierten Profils in der automatischen Azure-Verwaltung für VMs

Azure Automanage für bewährte Methoden für Computer verfügt über Standardprofile für bewährte Methoden, die nicht bearbeitet werden können. Wenn Sie jedoch mehr Flexibilität benötigen, können Sie die Gruppe von Diensten und Einstellungen auswählen, indem Sie ein benutzerdefiniertes Profil erstellen.

Wir unterstützen das Umschalten von Diensten auf ON und OFF. Wir unterstützen derzeit die Anpassung von Einstellungen bei [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) und [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), bevor Sie beginnen.

> [!NOTE]
> Mit kostenlosen Testkonten ist kein Zugriff auf die virtuellen Computer möglich, die in diesem Tutorial verwendet werden. Führen Sie ein Upgrade auf ein Abonnement mit nutzungsbasierter Zahlung durch.

> [!IMPORTANT]
> Zum erstmaligen Aktivieren von Automanage in einem Abonnement ist die folgende Azure RBAC-Berechtigung erforderlich: Rolle **Besitzer** oder **Mitwirkender** sowie Rollen vom Typ **Benutzerzugriffsadministrator**.


## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Aktivieren der automatischen Verwaltung für virtuelle Computer auf einem vorhandenen virtuellen Computer

1. Suchen Sie über die Suchleiste nach **Automanage – Best Practices für Azure-VMs**, und wählen Sie die Option aus.

2. Wählen Sie **Für vorhandene VM aktivieren** aus.

3. Wählen Sie unter **Konfigurationsprofil** **Benutzerdefiniertes Profil** aus.

4. Wählen Sie ein vorhandenes benutzerdefiniertes Profil aus der Dropdownliste aus, sofern vorhanden, oder erstellen Sie ein neues benutzerdefiniertes Profil, indem Sie auf **Neu erstellen** klicken.

5. Geben Sie auf dem Blatt **Neues Profil erstellen** die Details ein:
    1. Profilname
    1. Subscription
    1. Ressourcengruppe
    1. Region

    :::image type="content" source="media\virtual-machine-custom-profile\create-custom-profile.png" alt-text="Füllen Sie die Details des benutzerdefinierten Profils aus.":::

6. Passen Sie das Profil mit den gewünschten Diensten und Einstellungen an, und klicken Sie auf **Erstellen**.

7. Gehen Sie auf dem Blatt **Computer auswählen** wie folgt vor:
    1. Filtern Sie die Liste der virtuellen Computer nach Ihrem **Abonnement** und nach der **Ressourcengruppe**.
    1. Aktivieren Sie das Kontrollkästchen für jeden virtuellen Computer, den Sie integrieren möchten.
    1. Klicken Sie auf die Schaltfläche **Auswählen**.

    :::image type="content" source="media\virtual-machine-custom-profile\existing-vm-select-machine.png" alt-text="Auswählen eines vorhandenen virtuellen Computers aus der Liste verfügbarer virtueller Computer":::

    > [!NOTE]
    > Klicken Sie auf die Option zum **Anzeigen unzulässiger Computer**, um die Liste der nicht unterstützten Computer und die Gründe dafür anzuzeigen. 

## <a name="disable-automanage-for-vms"></a>Deaktivieren der automatischen Verwaltung für virtuelle Computer

Sie können die Verwendung der automatischen Azure-Verwaltung für virtuelle Computer schnell beenden, indem Sie die automatische Verwaltung deaktivieren.

:::image type="content" source="media\virtual-machine-custom-profile\disable-step-1.png" alt-text="Deaktivieren der automatischen Verwaltung auf einem virtuellen Computer":::

1. Navigieren Sie zur Seite **Automanage – Best Practices für Azure-Computer**, auf der alle automatisch verwalteten virtuellen Computer aufgeführt sind.
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

Die am häufigsten gestellten Fragen werden in unserem FAQ-Abschnitt (Häufig gestellte Fragen) beantwortet. 

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen](faq.yml)