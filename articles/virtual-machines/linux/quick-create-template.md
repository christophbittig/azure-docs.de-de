---
title: 'Schnellstart: Verwenden einer Resource Manager-Vorlage zum Erstellen einer Ubuntu Linux-VM'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie eine Resource Manager-Vorlage zum Erstellen eines virtuellen Linux-Computers verwenden.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 173adbaa32444a2eb28c050266a503c9f448f927
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693930"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>Schnellstart: Erstellen eines virtuellen Ubuntu Linux-Computers mithilfe einer ARM-Vorlage

**Gilt für**: :heavy_check_mark: Linux-VMs 

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) zum Bereitstellen eines virtuellen Ubuntu Linux-Computers (VM) in Azure verwenden.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/vm-simple-linux/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.compute/vm-simple-linux/azuredeploy.json":::


In der Vorlage sind mehrere Ressourcen definiert:

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): Dient zum Erstellen eines Subnetzes.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): Dient zum Erstellen eines Speicherkontos.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): Dient zum Erstellen einer Netzwerkschnittstelle (NIC).
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): Dient zum Erstellen einer Netzwerksicherheitsgruppe.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): Dient zum Erstellen eines virtuellen Netzwerks.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): Dient zum Erstellen einer öffentlichen IP-Adresse.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): Dient zum Erstellen eines virtuellen Computers.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt einen Schlüsseltresor und ein Geheimnis.

    [![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-linux%2fazuredeploy.json)

1. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein. Verwenden Sie die Standardwerte, sofern diese verfügbar sind.

    - **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    - **Ressourcengruppe**: Wählen Sie in der Dropdownliste eine bereits vorhandene Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und klicken Sie anschließend auf **OK**.
    - **Standort**: Wählen Sie einen Standort aus.  Beispiel: **USA, Mitte**.
    - **Administratorbenutzername**: Geben Sie einen Benutzernamen an, z. B. *azureuser*.
    - **Authentifizierungstyp:** Sie können zwischen der Verwendung eines SSH-Schlüssels und eines Kennworts wählen.
    - **Administratorkennwort oder Schlüssel**: Dies richtet sich danach, was Sie als Authentifizierungstyp ausgewählt haben:
        - Bei Auswahl von **Kennwort** muss das Kennwort mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](faq.yml#what-are-the-password-requirements-when-creating-a-vm-) erfüllen.
        - Fügen Sie bei Auswahl von **sshPublicKey** den Inhalt Ihres öffentlichen Schlüssels ein.
    - **Präfix der DNS-Bezeichnung**: Geben Sie einen eindeutigen Bezeichner ein, der als Teil der DNS-Bezeichnung verwendet wird.
    - **Ubuntu-Betriebssystemversion**: Wählen Sie aus, welche Version von Ubuntu auf der VM ausgeführt werden soll.
    - **Standort**: Hierfür wird standardmäßig derselbe Standort wie für die Ressourcengruppe verwendet, falls diese bereits vorhanden ist.
    - **VM-Größe**: Wählen Sie die [Größe](../sizes.md) aus, die für die VM verwendet werden soll.
    - **Name des virtuellen Netzwerks**: Der Name, der für das VNET verwendet werden soll.
    - **Subnetzname**: Der Name des Subnetzes, das von der VM verwendet werden sollte.
    - **Name der Netzwerksicherheitsgruppe**: Der Name für die Netzwerksicherheitsgruppe.
1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach Abschluss der Überprüfung die Option **Erstellen** aus, um die VM zu erstellen und bereitzustellen.


Zum Bereitstellen der Vorlage wird das Azure-Portal verwendet. Neben dem Azure-Portal können Sie auch die Azure CLI, Azure PowerShell und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Im Azure-Portal können Sie das Vorhandensein der VM und der anderen erstellten Ressourcen überprüfen. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln** aus, um die VM und die anderen Ressourcen anzuzeigen.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, wenn Sie die Ressourcen nicht mehr benötigen. Hierbei werden die VM und alle in der Ressourcengruppe enthaltenen Ressourcen gelöscht.

1. Wählen Sie die **Ressourcengruppe** aus.
1. Wählen Sie auf der Seite für die Ressourcengruppe die Option **Löschen** aus.
1. Geben Sie bei entsprechender Aufforderung den Namen der Ressourcengruppe ein, und klicken Sie dann auf **Löschen**.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mit einer ARM-Vorlage einen einfachen virtuellen Computer bereitgestellt. Fahren Sie mit dem Tutorial für virtuelle Linux-Computer fort, um weitere Informationen zu virtuellen Azure-Computern zu erhalten.


> [!div class="nextstepaction"]
> [Tutorials für virtuelle Azure Linux-Computer](./tutorial-manage-vm.md)