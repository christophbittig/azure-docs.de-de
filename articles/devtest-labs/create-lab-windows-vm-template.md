---
title: Erstellen eines Labs in Azure DevTest Labs mithilfe einer Azure Resource Manager-Vorlage
description: In dieser Schnellstartanleitung erstellen Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) in Azure DevTest Labs ein Lab. Ein Lab-Administrator richtet ein Lab ein, erstellt virtuelle Computer im Lab und konfiguriert Richtlinien.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/29/2021
ms.openlocfilehash: 7d6a1390c935d68b978146d9eb06e8d2681e50c2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847508"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>Schnellstart: Verwenden einer ARM-Vorlage zum Erstellen eines Labs in DevTest Labs

In dieser Schnellstartanleitung wird erläutert, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) zum Erstellen eines Labs in Azure DevTest Labs verwenden. Das Lab enthält einen virtuellen Computer mit Windows Server 2019 Datacenter.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm-claimed/azuredeploy.json":::

In der Vorlage sind drei Azure-Ressourcen definiert:

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs): Erstellen eines DevTest Labs-Labs
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks): Erstellen eines virtuellen DevTest Labs-Netzwerks
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines): Erstellen eines virtuellen DevTest Labs-Computers

Weitere Beispiele für ARM-Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie unten die Schaltfläche **In Azure bereitstellen** aus, um sich bei Azure anzumelden und die ARM-Vorlage zu öffnen.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

1. Geben Sie folgende Werte ein bzw. wählen diese aus:

    |Eigenschaft | BESCHREIBUNG |
    |---|---|
    |Subscription| Wählen Sie in der Dropdownliste das Azure-Abonnement aus, das für das Lab verwendet werden soll.|
    |Resource group| Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
    |Region | Als Wert wird automatisch der Speicherort für die Ressourcengruppe eingefügt.|
    |Labname| Geben Sie einen für das Abonnement eindeutigen Labnamen ein.|
    |Standort| Nehmen Sie keine Änderung vor. |
    |VM-Name| Geben Sie einen für das Abonnement eindeutigen VM-Namen ein.|
    |VM-Größe | Nehmen Sie keine Änderung vor. |
    |Benutzername | Geben Sie einen Benutzernamen für den virtuellen Computer ein.|
    |Kennwort| Geben Sie ein Kennwort ein, das zwischen 8 und 123 Zeichen lang ist.|

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Screenshot: Seite „Lab erstellen“":::

1. Klicken Sie auf **Überprüfen + erstellen** und nach der Überprüfung auf **Erstellen**. Anschließend gelangen Sie zur Seite **Übersicht** der Bereitstellung, auf der Sie den Fortschritt überwachen können. Die Bereitstellungszeiten variieren basierend auf der ausgewählten Hardware, dem Basisimage und den Artefakten. Die Bereitstellungszeit für die in dieser Vorlage verwendeten Konfigurationen beträgt ca. zwölf Minuten.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

1. Wählen Sie nach Abschluss der Bereitstellung auf der Seite **Übersicht** der Vorlage oder unter **Benachrichtigungen** die Option **Zu Ressource wechseln** aus.

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Screenshot: Bereitstellung abgeschlossen und Schaltfläche „Zu Ressourcengruppe wechseln“":::

1. Auf der Seite **Ressourcengruppe** werden die Ressourcen in der Ressourcengruppe aufgelistet, einschließlich Ihres Labs und der abhängigen Ressourcen wie virtuelle Netzwerke und virtuelle Computer. Wählen Sie Ihre **DevTest Lab**-Ressource aus, um zur Seite **Übersicht** Ihres Labs zu navigieren.

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-overview.png" alt-text="Screenshot: Übersicht über Ressourcengruppe":::

1. Auf der Seite **Übersicht** Ihres Labs können Sie im Abschnitt **Meine virtuellen Computer** Ihren virtuellen Computer anzeigen.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Screenshot: Seite „Übersicht“ des Labs mit dem virtuellen Computer":::

1. Gehen Sie einen Schritt zurück, und listen Sie die Ressourcengruppen für Ihr Abonnement auf. Beachten Sie, dass die Bereitstellung eine neue Ressourcengruppe für den virtuellen Computer erstellt hat. Die Syntax umfasst den Labnamen, den VM-Namen und Zufallszahlen. Basierend auf den in diesem Artikel verwendeten Werten lautet der automatisch generierte Name `MyOtherLab-myVM-173385`.

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-list.png" alt-text="Screenshot: Liste der Ressourcengruppen":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie Ressourcen, um Gebühren für die Ausführung des Labs und des virtuellen Computers in Azure zu vermeiden. Wenn Sie das nächste Tutorial durcharbeiten möchten, um auf den virtuellen Computer im Lab zuzugreifen, können Sie die Ressourcen nach Abschluss dieses Tutorials bereinigen. Führen Sie andernfalls die folgenden Schritte aus: 

1. Kehren Sie zur Startseite für das Lab zurück, das Sie erstellt haben.

1. Wählen im oberen Menü **Löschen** aus.

   :::image type="content" source="./media/create-lab-windows-vm-template/portal-lab-delete.png" alt-text="Screenshot: Schaltfläche „Löschen“ für das Lab":::

1. Geben Sie auf der Seite **Möchten Sie den Löschvorgang durchführen?** den Lab-Namen in das Textfeld ein, und wählen Sie dann **Löschen** aus.

1. Während des Löschvorgangs können Sie oben auf dem Bildschirm **Benachrichtigungen** auswählen, um den Status anzuzeigen. Das Löschen des Labs dauert eine Weile. Fahren Sie mit dem nächsten Schritt fort, nachdem das Lab gelöscht wurde.

1. Wenn Sie das Lab in einer vorhandenen Ressourcengruppe erstellt haben, wurden alle Lab-Ressourcen entfernt. Wenn Sie eine neue Ressourcengruppe für dieses Tutorial erstellt haben, ist diese jetzt leer und kann gelöscht werden. Zuvor wäre es nicht möglich gewesen, die Ressourcengruppe zu löschen, während sich das Lab noch in ihr befand.

## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie ein Lab mit einem virtuellen Computer erstellt. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie auf das Lab und den virtuellen Computer zugreifen:

> [!div class="nextstepaction"]
> [Tutorial: Zugreifen auf das Lab](tutorial-use-custom-lab.md)
