---
title: Erstellen eines Labs in Azure DevTest Labs mithilfe einer Azure Resource Manager-Vorlage
description: In dieser Schnellstartanleitung erstellen Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) in Azure DevTest Labs ein Lab. Ein Lab-Administrator richtet ein Lab ein, erstellt virtuelle Computer im Lab und konfiguriert Richtlinien.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/15/2021
ms.openlocfilehash: 0cdc7db0f29a09040b7d40507cfad840b75f0c91
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246881"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>Schnellstart: Verwenden einer ARM-Vorlage zum Erstellen eines Labs in DevTest Labs

In dieser Schnellstartanleitung verwenden Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) zum Erstellen eines Labs in Azure DevTest Labs. Das Lab enthält einen virtuellen Computer mit Windows Server 2019 Datacenter.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

In dieser Schnellstartanleitung führen Sie folgende Aktionen aus:

> [!div class="checklist"]
> * Überprüfen Sie die Vorlage.
> * Stellen Sie die Vorlage bereit.
> * Überprüfen der Bereitstellung
> * Bereinigen der Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

In dieser Schnellstartanleitung wird die Vorlage [Creates a lab in Azure DevTest Labs with a Windows Server VM](https://azure.microsoft.com/resources/templates/dtl-create-lab-windows-vm/) (Erstellen eines Labs in Azure DevTest Labs mit einem virtuellen Windows Server-Computer) verwendet. Die Vorlage definiert die folgenden Ressourcen:

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs)
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm/azuredeploy.json":::

Weitere Beispiele für ARM-Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie die folgende Schaltfläche **In Azure bereitstellen** aus, um die ARM-Vorlage zu verwenden. Mit der Vorlage wird der Bildschirm für die Lab-Erstellung im Azure-Portal geöffnet:

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Füllen Sie auf dem Bildschirm **Creates a lab in Azure DevTest Labs with a Windows Server VM** (Erstellen eines Labs in Azure DevTest Labs mit einem virtuellen Windows Server-Computer) die folgenden Elemente aus:

   - **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe, um die spätere Bereinigung zu erleichtern.
   - **Region**: Wählen Sie einen Standort für die Ressourcengruppe aus.
   - **Labname**: Geben Sie einen Namen für die neue Lab-Instanz ein.
   - **VM-Name**: Geben Sie einen Namen für den zu erstellenden virtuellen Computer ein. 
   - **Benutzername**: Geben Sie einen Namen für den Benutzer ein, der auf den virtuellen Computer zugreifen kann. 
   - **Kennwort**: Geben Sie ein Kennwort für den Benutzer ein.

1. Klicken Sie auf **Überprüfen + erstellen** und nach der Überprüfung auf **Erstellen**.

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Screenshot: Seite „Lab erstellen“":::

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung
1. Während der Bereitstellung können Sie oben auf dem Bildschirm **Benachrichtigungen** und **Bereitstellung wird ausgeführt** auswählen, um die Seite **Übersicht** für die Bereitstellung anzuzeigen. Die Bereitstellung, insbesondere das Erstellen eines virtuellen Computers, nimmt einige Zeit in Anspruch.

   :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Screenshot: Bereitstellung wird ausgeführt.":::

1. Wenn die Bereitstellung abgeschlossen ist, wählen Sie **Zu Ressourcengruppe wechseln** aus, um zur Ressourcengruppe für Ihr Lab zu navigieren.

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Screenshot: Bereitstellung abgeschlossen und Schaltfläche „Zu Ressourcengruppe wechseln“":::

1. Auf der Seite **Ressourcengruppe** werden die Ressourcen in der Ressourcengruppe aufgelistet, einschließlich Ihres Labs und der abhängigen Ressourcen wie virtuelle Netzwerke und virtuelle Computer. Wählen Sie Ihre **DevTest Lab**-Ressource aus, um zur Seite **Übersicht** Ihres Labs zu navigieren.

1. Wählen Sie auf der Seite **Übersicht** Ihres Labs die Option **Abrufbare virtuelle Computer** aus.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Screenshot: Seite „Übersicht“ des Labs mit dem Link „Abrufbare virtuelle Computer“":::

1. Wählen Sie auf der Seite **Abrufbare virtuelle Computer** die Schaltfläche mit den Auslassungspunkten (**Weitere Optionen**) und dann **Computer beanspruchen** aus.

   :::image type="content" source="./media/create-lab-windows-vm-template/claim-vm.png" alt-text="Screenshot: Seite „Abrufbare virtuelle Computer“ mit der Option „Computer beanspruchen“":::

1. Vergewissern Sie sich auf der Seite **Übersicht** des Labs, dass der virtuelle Computer unter **Meine virtuellen Computer** mit dem Status **Wird gestartet** angezeigt wird. Warten Sie, bis sich der Status in **Wird ausgeführt** ändert.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-vm.png" alt-text="Screenshot: Seite „Übersicht“ des Labs mit dem aufgeführten virtuellen Computer":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Schnellstartressourcen, um Gebühren für die Ausführung des Labs und des virtuellen Computers in Azure zu vermeiden. Wenn Sie das nächste Tutorial durcharbeiten möchten, um auf den virtuellen Computer im Lab zuzugreifen, können Sie die Ressourcen nach Abschluss dieses Tutorials bereinigen.

Sie können eine Ressourcengruppe mit einem darin enthaltenen Lab nicht löschen. Löschen Sie zuerst das Lab, damit Sie die Ressourcengruppe löschen können.

1. Wählen Sie auf der Startseite des Labs auf der Symbolleiste die Option **Löschen** aus.

   :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Screenshot: Schaltfläche „Löschen“ auf der Startseite des Labs":::

1. Geben Sie auf der Bestätigungsseite den Lab-Namen ein, und wählen Sie **Löschen** aus.

1. Während des Löschvorgangs können Sie oben auf dem Bildschirm **Benachrichtigungen** auswählen, um den Status anzuzeigen. Das Löschen des Labs dauert eine Weile.

1. Wählen Sie nach dem Löschen des Labs auf der Startseite des Labs die **Ressourcengruppe** aus. Ist die Startseite des Labs nicht mehr verfügbar, suchen Sie im Azure-Suchfeld nach **Ressourcengruppen**, und wählen Sie dann die Ressourcengruppe aus, die Ihr Lab enthielt.

   :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Screenshot: Bestätigung des Löschvorgangs unter „Benachrichtigungen“":::

1. Wählen Sie auf der Seite **Ressourcengruppe** in der Symbolleiste die Option **Ressourcengruppe löschen** aus. Geben Sie auf der Bestätigungsseite den Ressourcengruppennamen ein, und wählen Sie **Löschen** aus. Sie können die Benachrichtigungen überprüfen, um zu bestätigen, dass die Ressourcengruppe gelöscht wurde.
 
   :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Screenshot: Schaltfläche „Ressourcengruppe löschen“":::

## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie ein Lab mit einem virtuellen Computer erstellt. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie auf das Lab und den virtuellen Computer zugreifen:

> [!div class="nextstepaction"]
> [Tutorial: Zugreifen auf das Lab](tutorial-use-custom-lab.md)
