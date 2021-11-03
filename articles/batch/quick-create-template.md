---
title: 'Azure-Schnellstart: Erstellen eines Batch-Kontos – Azure Resource Manager-Vorlage'
description: In dieser Schnellstartanleitung wird gezeigt, wie Sie mithilfe einer ARM-Vorlage ein Batch-Konto erstellen.
ms.date: 05/25/2021
ms.topic: quickstart
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: fcc0876bc9a165b06705e4c57090b084205dd0c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086828"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>Schnellstart: Erstellen eines Batch-Kontos mithilfe einer ARM-Vorlage

Steigen Sie in die Verwendung von Azure Batch ein, indem Sie eine ARM-Vorlage (Azure Resource Manager) verwenden, um ein Batch-Konto, einschließlich Speicher, zu erstellen. Sie benötigen ein Batch-Konto zum Erstellen von Computeressourcen (Pools mit Computeknoten) und Batch-Aufträgen. Sie können ein Azure Storage-Konto mit Ihrem Batch-Konto verknüpfen. Dies ist nützlich zum Bereitstellen von Anwendungen und Speichern von Eingabe- und Ausgabedaten für die meisten Workloads in der Praxis.

Nach Abschluss dieser Schnellstartanleitung sind Sie mit den wichtigsten Konzepten des Batch-Diensts vertraut und können Batch mit realistischeren Workloads und in größerem Umfang ausprobieren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.batch%2Fbatchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein aktives Azure-Abonnement.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/batchaccount-with-storage/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.batch/batchaccount-with-storage/azuredeploy.json":::

Zwei Azure-Ressourcen sind in der Vorlage definiert:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): Erstellt ein Speicherkonto.
- [Microsoft.Batch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts): Erstellt ein Batch-Konto

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt ein Azure Batch-Konto und ein Speicherkonto.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.batch%2Fbatchaccount-with-storage%2Fazuredeploy.json)

1. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

   :::image type="content" source="media/quick-create-template/batch-template.png" alt-text="Resource Manager-Vorlage: Batch-Kontoerstellung – Bereitstellen (Portal)":::

   - **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
   - **Ressourcengruppe**: Wählen Sie die Option **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und klicken Sie dann auf **OK**.
   - **Standort**: Wählen Sie einen Standort aus. Beispiel: **USA, Mitte**.
   - **Name des Batchkontos**: Behalten Sie den Standardwert bei.
   - **Speicherkonto-SKU**: Wählen Sie einen Speicherkontotyp aus. Beispielsweise **Standard_LRS**.
   - **Standort**: Behalten Sie die Standardeinstellung bei, sodass sich die Ressourcen am gleichen Ort wie Ihre Ressourcengruppe befinden.

1. Klicken Sie auf **Überprüfen + erstellen** und dann auf **Erstellen**.

Nach einigen Minuten sollte eine Benachrichtigung angezeigt werden, dass das Batch-Konto erfolgreich erstellt wurde.

In diesem Beispiel wird das Azure-Portal zum Bereitstellen der Vorlage verwendet. Neben dem Azure-Portal können Sie auch Azure PowerShell, die Azure-Befehlszeilenschnittstelle (Azure CLI) und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Sie können die Bereitstellung im Azure-Portal validieren, indem Sie zu der von Ihnen erstellten Ressourcengruppe navigieren. Vergewissern Sie sich im Bildschirm **Übersicht**, dass das Batch-Konto und das Speicherkonto vorhanden sind.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit weiteren unserer [Tutorials](./tutorial-parallel-dotnet.md) fortfahren möchten, kann es ratsam sein, diese Ressourcen beizubehalten. Sie können [die Ressourcengruppe auch löschen](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), wenn Sie die Ressourcen nicht mehr benötigen, wodurch auch das von Ihnen erstellte Batch-Konto und Speicherkonto gelöscht werden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Batch-Konto und ein Speicherkonto erstellt. Weitere Informationen zu Azure Batch finden Sie in den Azure Batch-Tutorials.

> [!div class="nextstepaction"]
> [Azure Batch-Tutorials](./tutorial-parallel-dotnet.md)
