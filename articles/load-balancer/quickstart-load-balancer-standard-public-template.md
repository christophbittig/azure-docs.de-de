---
title: 'Schnellstart: Erstellen eines öffentlichen Lastenausgleichs – Azure-Vorlage'
titleSuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Lastenausgleich per Azure Resource Manager-Vorlage erstellen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: a5cb80924daf3328da6a3a9fd7fc1c7c7212bed6
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129090930"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer mithilfe einer ARM-Vorlage

Durch die Verteilung der eingehenden Anforderungen auf mehrere virtuelle Computer (VMs) bietet ein Lastenausgleich ein höheres Maß an Verfügbarkeit und Skalierbarkeit.

In dieser Schnellstartanleitung erfahren Sie, wie Sie für den Lastenausgleich virtueller Computer einen Lastenausgleich im Tarif „Standard“ bereitstellen.

Bei Verwendung einer ARM-Vorlage werden im Vergleich zu anderen Bereitstellungsmethoden weniger Schritte benötigt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fload-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/load-balancer-standard-create/).

Die Lastenausgleichs-SKU und die SKU für öffentliche IP-Adressen müssen übereinstimmen. Wenn Sie einen Lastenausgleich im Tarif „Standard“ erstellen, müssen Sie auch eine neue öffentliche Standard-IP-Adresse erstellen, die als Front-End für diesen Standard-Lastenausgleich konfiguriert ist. Wenn Sie einen Lastenausgleich im Tarif „Basic“ erstellen möchten, verwenden Sie [diese Vorlage](https://azure.microsoft.com/resources/templates/2-vms-loadbalancer-natrules/). Microsoft empfiehlt für Produktionsworkloads die Verwendung der Standard-SKU.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/load-balancer-standard-create/azuredeploy.json":::

In der Vorlage wurden mehrere Azure-Ressourcen definiert:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): Für den Lastenausgleich, den Bastionhost und jeweils die drei virtuellen Computer.
- [**Microsoft.Network/bastionHosts**](/azure/templates/microsoft.network/bastionhosts)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) (3).
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3).
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3): Für die Konfiguration von IIS (Internet Information Server) und der Webseiten.

Weitere Vorlagen zum Azure Load Balancer finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie **Try it** (Ausprobieren) im folgenden Codeblock aus, um Azure Cloud Shell zu öffnen. Folgen Sie dann den Anweisungen, um sich bei Azure anzumelden.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Warten Sie, bis die Aufforderung in der Konsole angezeigt wird.

1. Wählen Sie **Copy** (Kopieren) im vorherigen Codeblock aus, um das PowerShell-Skript zu kopieren.

1. Klicken Sie mit der rechten Maustaste auf den Shellkonsolenbereich, und wählen Sie **Einfügen** aus.

1. Gehen Sie die Werte ein.

   Die Vorlagenbereitstellung erstellt drei Verfügbarkeitszonen. Verfügbarkeitszonen werden nur in [bestimmten Regionen](../availability-zones/az-overview.md) unterstützt. Verwenden Sie eine der unterstützten Regionen. Wenn Sie nicht sicher sind, geben Sie **centralus** ein.

   Der Ressourcengruppenname ist der Projektname mit dem Zusatz **rg**. Sie benötigen den Ressourcengruppennamen im nächsten Abschnitt.

Das Bereitstellen der Vorlage dauert ungefähr 10 Minuten. Nach Abschluss des Vorgangs sieht die Ausgabe in etwa wie folgt aus:

![Azure Load Balancer Standard – Resource Manager-Vorlage: PowerShell-Bereitstellungsausgabe](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell wird verwendet, um die Vorlage bereitzustellen. Sie können auch das Azure-Portal, die Azure CLI und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-portal.md).

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Bereich **Ressourcengruppen** aus.

1. Wählen Sie die Ressourcengruppe aus, die Sie im vorherigen Abschnitt erstellt haben. Der Ressourcengruppenname entspricht standardmäßig dem Projektnamen mit dem Zusatz **rg**.

1. Wählen Sie den Load Balancer aus. Sein Standardname entspricht dem Projektnamen mit dem Zusatz **-lb**.

1. Kopieren Sie nur den IP-Adressteil der öffentlichen IP-Adresse, und fügen Sie ihn in die Adressleiste des Browsers ein.

   ![Azure Load Balancer Standard – Resource Manager-Vorlage: Öffentliche IP-Adresse](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    Der Browser zeigt die Standardseite des Internetinformationsdienste-Webservers (Internet Information Services, IIS) an.

   ![IIS-Webserver](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Sie können vom Clientcomputer aus eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Lastenausgleich den Datenverkehr auf alle drei virtuellen Computer verteilt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcen, wenn Sie sie nicht mehr benötigen:

* Resource group
* Load Balancer
* Verwandte Ressourcen

Wählen Sie im Azure-Portal die Ressourcengruppe aus, die den Lastenausgleich enthält, und wählen Sie anschließend **Ressourcengruppe löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung führen Sie die folgenden Schritte aus:

* Erstellen eines virtuellen Netzwerks für Lastenausgleich und virtuelle Computer.
* Erstellen eines Azure-Bastionhosts für die Verwaltung.
* Erstellen eines Lastenausgleichs im Tarif „Standard“ und Anfügen von virtuellen Computern an diesen
* Konfigurieren der Datenverkehrsregel für den Lastenausgleich sowie des Integritätstests
* Testen des Lastenausgleichs

Weitere Informationen zum Lastenausgleich finden Sie in den Tutorials zum Azure Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer-Tutorials](./quickstart-load-balancer-standard-public-portal.md)
