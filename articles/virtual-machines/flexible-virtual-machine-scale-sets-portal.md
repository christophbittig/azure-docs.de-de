---
title: Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure CLI
description: Erfahren Sie, wie Sie im Orchestrierungsmodus „Flexibel“ eine VM-Skalierungsgruppe im Azure-Portal erstellen.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/25/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3f6b0512d97b78111be5d88d20bf7800ebe9937c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008501"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Erstellen von VMs in einer flexiblen Skalierungsgruppe mit Azure CLI

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen

In diesem Artikel wird beschrieben, wie Sie das Azure-Portal verwenden sollten, um eine VM-Skalierungsgruppe im flexiblen Orchestrierungsmodus zu erstellen. Weitere Informationen über flexible Skalierungsgruppen finden Sie unter [Flexibler Orchestrierungsmodus für Skalierungsgruppen für VM](flexible-virtual-machine-scale-sets.md). 


> [!CAUTION]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.


## <a name="create-a-virtual-machine-scale-set"></a>Erstellen einer Skalierungsgruppe für virtuelle Computer

Sie können eine Skalierungsgruppe mit einem Windows Server-Image oder Linux-Image wie RHEL, CentOS, Ubuntu oder SLES bereitstellen.

1. Suchen Sie in der Suchleiste von Azure-Portal nach **Virtual Machine Scale Sets**, und wählen Sie diese Option aus.
1. Wählen Sie auf der Seite **VM-Skalierungsgruppen** die Option **Erstellen** aus.

1. Stellen Sie auf der Registerkarte **Grundlagen** unter **Projektdetails** sicher, dass das richtige Abonnement ausgewählt ist, und wählen Sie in der Liste der Ressourcengruppen den Eintrag *myVMSSResourceGroup* aus.  
1. Legen Sie unter **Details zur Skalierungsgruppe** für Ihren Skalierungsgruppennamen *myScaleSet* fest, und wählen Sie eine **Region** aus, die in Ihrer Nähe liegt.
1. Wählen Sie unter **Orchestrierung** die Option *Flexibel* für **Orchestrierungsmodus** aus. 
1. Wählen Sie unter **Instanzdetails** ein Marketplace-Bild für **Bild** aus. In diesem Beispiel wurde *Ubuntu Server 18.04 LTS* ausgewählt.
1. Geben Sie den gewünschten Benutzernamen ein, und wählen Sie den bevorzugten Authentifizierungstyp aus.
   - Ein **Kennwort** muss 12 Zeichen lang sein und zur Erfüllung der Komplexitätsanforderungen drei der folgenden vier Elemente enthalten: einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen. Weitere Informationen finden Sie im Artikel zu den [Anforderungen an Benutzernamen und Kennwörter](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-).
   - Wenn Sie ein Datenträgerimage für Linux-Betriebssysteme auswählen, können Sie stattdessen einen **öffentlichen SSH-Schlüssel** auswählen. Geben Sie nur Ihren öffentlichen Schlüssel an (z.B. *~/.ssh/id_rsa.pub*). Sie können Azure Cloud Shell aus dem Portal verwenden, um [SSH-Schlüssel zu erstellen und zu verwenden](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Wählen Sie **Weiter** aus, um auf die nächste Seite zu gelangen. 

1. Belassen Sie die Standardwerte für die **Datenträger**-Seite.

1. Wählen Sie **Weiter** aus, um auf die nächste Seite zu gelangen. 

1. Aktivieren Sie auf der Seite **Netzwerk** unter **Lastenausgleich** das Kontrollkästchen **Lastenausgleichsmodul verwenden**, um die Skalierungsgruppeninstanzen hinter einen Lastenausgleich zu setzen. 
1. Wählen Sie unter **Optionen für den Lastenausgleich** die Option **Azure Load Balancer** aus.
1. Wählen Sie unter **Load Balancer auswählen** einen Lastenausgleich aus, oder erstellen Sie einen neuen Lastenausgleich.
1. Wählen Sie unter **Back-End-Pool auswählen** die Option **Neuen erstellen** aus, geben Sie *myBackendPool* ein, und wählen Sie dann **Erstellen** aus.

    > [!NOTE]
    > Verwandte Informationen zu Netzwerken für flexible Skalierungsgruppen finden Sie unter [Skalierbare Netzwerkkonnektivität für flexible Skalierungsgruppen](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity).

1. Wählen Sie **Weiter** aus, um auf die nächste Seite zu gelangen.

1. Legen Sie auf der Seite **Skalierung** das Feld **Anzahl der Anfangsinstanzen** auf *5* fest. Sie können diese Zahl auf bis zu 1000 einstellen. 
1. Behalten Sie für die **Skalierungsrichtlinie** die Einstellung *Manuell* bei. 

1. Wählen Sie abschließend **Überprüfen + Erstellen** aus. 
1. Wählen Sie nach der erfolgreichen Überprüfung **Erstellen** aus, um die Skalierungsgruppe bereitzustellen.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Ressourcengruppe, Skalierungsgruppe und alle zugehörigen Ressourcen nicht mehr benötigt werden, löschen Sie sie. Wählen Sie hierzu die Ressourcengruppe für die Skalierungsgruppe und anschließend **Löschen** aus.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Erfahren Sie, wie Sie eine flexible Skalierung mit Azure CLI erstellen.](flexible-virtual-machine-scale-sets-cli.md)