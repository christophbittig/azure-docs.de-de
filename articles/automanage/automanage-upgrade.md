---
title: Aktualisieren Ihrer Azure-Computer mit automatischer Verwaltung (Automanage) auf die neueste Version der automatischen Verwaltung
description: Erfahren Sie, wie Sie Ihre Computer auf die neueste Azure Version der automatischen Verwaltung (Automanage) aktualisieren.
author: mmccrory
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/20/2021
ms.author: memccror
ms.openlocfilehash: d7cc53c30ead02a2ca4fc40fe00ae2235016336f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478900"
---
# <a name="upgrade-your-machines-to-the-latest-automanage-version"></a>Aktualisieren Ihrer Computer auf die neueste Version der automatischen Verwaltung

Im November 2021 veröffentlichte Automanage (automatische Verwaltung) eine neue Version des Angebots an bewährten Verfahren für Computer. Die neue API unterstützt jetzt das Erstellen benutzerdefinierter Profile, in denen Sie die Dienste und Einstellungen auswählen können, die Sie auf Ihren Computern anwenden möchten. Mit dieser neuen Version ist außerdem das Konto für die automatische Verwaltung nicht mehr erforderlich. In diesem Abschnitt werden die Unterschiede bei den Versionen und das Aktualisieren beschrieben. 

## <a name="how-to-upgrade-your-machines"></a>Gewusst wie: Durchführen eines Upgrades für Ihren Computer

Im Folgenden finden Sie Anweisungen zum Aktualisieren Ihrer Computer auf die neueste API-Version der automatischen Verwaltung (Automanage). 

### <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), bevor Sie beginnen.

> [!NOTE]
> Mit kostenlosen Testkonten ist kein Zugriff auf die virtuellen Computer möglich, die in diesem Tutorial verwendet werden. Führen Sie ein Upgrade auf ein Abonnement mit nutzungsbasierter Zahlung durch.

> [!IMPORTANT]
> Zum erstmaligen Aktivieren der automatischen Verwaltung in einem Abonnement mit einer neuen Version der automatischen Verwaltung ist die folgende Azure RBAC-Berechtigung erforderlich: Rolle **Besitzer** oder **Mitwirkender** sowie Rollen vom Typ **Benutzerzugriffsadministrator**.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.


### <a name="check-to-see-which-machines-need-to-be-upgraded"></a>Überprüfen Sie, welche Computer aktualisiert werden müssen.

Alle Computer, die aktualisiert werden müssen, weisen den Status **Upgrade erforderlich** auf. Außerdem wird auf der Übersichtsseite für die automatische Verwaltung ein Banner mit dem Hinweis angezeigt, dass Sie ein Upgrade für Ihre Computer durchführen müssen. 

:::image type="content" source="media\automanage-upgrade\overview-blade.png" alt-text="Upgrade-Status erforderlich.":::

### <a name="disable-automanage-machines-that-need-to-be-upgrade"></a>Deaktivieren der automatischen verwalteten Computer, die aktualisiert werden müssen

Bevor ein Computer ein Upgrade auf die neue Version der automatischen Verwaltung (Automanage) durchführen kann, muss der Computer von der vorherigen Version der automatischen Verwaltung (Automanage) deaktiviert werden. Führen Sie die folgenden Schritte aus, um die Computer zu deaktivieren:
1. Aktivieren Sie das Kontrollkästchen neben dem virtuellen Computer, den Sie deaktivieren möchten.
1. Klicken Sie auf die Schaltfläche **Deaktivieren.**
1. Lesen Sie die Meldung im angezeigten Popupelement sorgfältig durch, bevor Sie dem **Deaktivieren** zustimmen.

:::image type="content" source="media\automanage-upgrade\disable-automanage.png" alt-text="Deaktivieren der automatischen Verwaltung.":::

### <a name="re-enable-automanage-on-your-machines"></a>Erneutes Aktivieren der automatischen Verwaltung auf Ihren Computern

Nachdem Ihre Computer aus der automatischen Verwaltung genommen wurden, können Sie die automatische Verwaltung jetzt wieder aktivieren. Wenn Sie die automatische Verwaltung erneut aktivieren, wird Automanage (die automatische Verwaltung) die neueste Version der automatischen Verwaltung verwenden. 

1. Wählen Sie **Für vorhandene VM aktivieren** aus.

    :::image type="content" source="media\automanage-upgrade\zero-vm-list-view.png" alt-text="Für vorhandene VM aktivieren":::

2. Wählen Sie unter **Konfigurationsprofil** Ihren Profiltyp aus: **Azure bewährte Methoden (Best Practices – Produktion** oder **Azure bewährte Methoden (Best Practices) – Dev/Test** oder [**Benutzerdefiniertes Profil**](virtual-machines-custom-profile.md)

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Auswählen der Umgebung":::

    > [!NOTE]
    > Die **Produktions**-Umgebung wird dem Konfigurationsprofil **Azure bewährte Methoden (Best Practices) – Produktion** zugeordnet. Die **Dev/Test**-Umgebung wird dem Konfigurationsprofil **Azure bewährte Methoden (Best Practices) – Produktion** zugeordnet. Wenn Sie die **Konfigurationseinstellungen** genutzt haben, können Sie ein **benutzerdefiniertes Profil** mit den gleichen Änderungen erstellen. 

3. Gehen Sie auf dem Blatt **Computer auswählen** wie folgt vor:
    1. Filtern Sie die Liste nach Ihrem **Abonnement** und der **Ressourcengruppe**.
    1. Aktivieren Sie das Kontrollkästchen für jeden virtuellen Computer, den Sie integrieren möchten.
    1. Klicken Sie auf die Schaltfläche **Auswählen**.
    > [!NOTE]
    > Sie können sowohl Azure-VMs als auch Server mit Azure Arc-Unterstützung auswählen.

    :::image type="content" source="media\automanage-upgrade\existing-vm-select-machine.png" alt-text="Auswählen eines vorhandenen virtuellen Computers aus der Liste verfügbarer virtueller Computer":::

4. Klicken Sie auf die Schaltfläche **Aktivieren**.

Jetzt werden Ihre Computer in die neueste Version der automatischen Verwaltung integriert.

## <a name="differences-in-the-automanage-versions"></a>Unterschiede bei den Versionen der automatischen Verwaltung

### <a name="environment-and-configuration-profiles"></a>Umgebungs- und Konfigurationsprofile
In der vorherigen Version der automatischen Verwaltung (Automanage) haben Sie den Umgebungstyp Dev/Test oder Produktion ausgewählt. In der neuen Version der automatischen Verwaltung (Automanage) wird die Umgebung Konfigurationsprofilen zugeordnet. Die Optionen für Konfigurationsprofile sind Azure bewährte Methoden (Best Practices) – Dev/Test, Azure bewährte Methoden (Best Practices) – Produktion, Benutzerdefiniertes Profil. Die Dienste und Einstellungen aus der **Dev/Test**-Umgebung sind im Konfigurationsprofil **Azure bewährte Methoden (Best Practices) – Dev/Test** identisch. Ebenso sind die Dienste und Einstellungen aus der **Produktions**-Umgebung im Konfigurationsprofil **Azure bewährte Methoden (Best Practices) – Produktion** identisch. 

### <a name="configuration-preferences-and-custom-profiles"></a>Konfigurationseinstellungen und benutzerdefinierte Profile
In der vorherigen Version der automatischen Verwaltung (Automanage) konnten Sie eine Teilmenge der Einstellungen über **Konfigurationseinstellungen** anpassen. In der neuesten Version der automatischen Verwaltung (Automanage) haben wir die Anpassung verbessert, sodass Sie jeden Dienst auswählen können, den Sie integrieren möchten, und das Ändern einiger Einstellungen für die Dienste über **benutzerdefinierte Profile** unterstützen können. 

### <a name="automanage-account-and-first-party-application"></a>Automatisch verwaltetes Konto und Erstanbieteranwendung
In der vorherigen Version der automatischen Verwaltung (Automanage) wurde das automatisch verwaltete Konto als MSI verwendet, um Aktionen auf Ihrem Computer vorab zu formatieren. In der neuesten Version der automatischen Verwaltung Automanage verwendet die automatische Verwaltung jedoch eine Erstanbieteranwendung (Anwendungs-ID: d828acde-4b48-47f5-a6e8-52460104a052), um Aktionen auf den automatisch verwalteten Computern auszuführen. 

Sowohl für die vorherige als auch für die neue Version der automatischen Verwaltung benötigen Sie die folgenden Berechtigungen:
* Wenn Sie die automatische Verwaltung zum ersten Mal in einem Abonnement integrieren, benötigen Sie die Rolle **Besitzer** oder **Mitwirkender** zusammen mit den Rollen **Benutzerzugriffsadministrator**.
* Wenn Sie die automatische Verwaltung in ein Abonnement integrieren, das bereits über automatisch verwaltete Computer verfügt, integrieren, benötigen Sie **Mitwirkender** für die Ressourcengruppe, in der sich der Computer befindet. 
> [!NOTE]
> Wenn der Computer, den Sie in die automatische Verwaltung integrieren, bereits mit einem Protokollanalyse-Arbeitsbereich in einem anderen Abonnement als der Computer verbunden ist, benötigen Sie auch die oben beschriebenen Berechtigungen für das Abonnement des Protokollanalyse-Arbeitsbereichs.

## <a name="next-steps"></a>Nächste Schritte 

Die am häufigsten gestellten Fragen werden in unserem FAQ-Abschnitt (Häufig gestellte Fragen) beantwortet. 

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen](faq.yml)

