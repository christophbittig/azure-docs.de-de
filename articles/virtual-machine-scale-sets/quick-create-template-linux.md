---
title: 'Schnellstart: Erstellen einer Linux-VM-Skalierungsgruppe mit einer Azure Resource Manager-Vorlage'
description: Es wird beschrieben, wie Sie mit einer Azure Resource Manager-Vorlage schnell eine Linux-VM-Skalierungsgruppe erstellen, mit der eine Beispiel-App bereitgestellt wird und Regeln für die automatische Skalierung konfiguriert werden.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs
ms.openlocfilehash: f205354b0a27404adaec83e071934c2cea4d4ba0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693223"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-arm-template"></a>Schnellstart: Erstellen einer Linux-VM-Skalierungsgruppe mit einer ARM-Vorlage

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Mit einer VM-Skalierungsgruppe können Sie eine Gruppe automatisch skalierender virtueller Computer bereitstellen und verwalten. Sie können die Anzahl virtueller Computer in der Skalierungsgruppe manuell skalieren oder basierend auf der Ressourcennutzung, z.B. CPU-Auslastung, Speicherbedarf oder Netzwerkdatenverkehr, Regeln für die automatische Skalierung definieren. Daraufhin wird der Datenverkehr durch einen Azure-Lastenausgleich auf die VM-Instanzen in der Skalierungsgruppe verteilt. In dieser Schnellstartanleitung erstellen Sie eine VM-Skalierungsgruppe und stellen eine Beispielanwendung mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) bereit.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Mit ARM-Vorlagen können Sie Gruppen mit verwandten Ressourcen bereitstellen. In einer einzelnen Vorlage können Sie die VM-Skalierungsgruppe erstellen, Anwendungen installieren und Regeln für die automatische Skalierung konfigurieren. Bei Nutzung von Variablen und Parametern kann diese Vorlage wiederverwendet werden, um vorhandene Skalierungsgruppen zu aktualisieren oder zusätzliche zu erstellen. Sie können Vorlagen über das Azure-Portal, die Azure CLI oder Azure PowerShell oder über CI/CD-Pipelines (Continuous Integration/Continuous Delivery) bereitstellen.

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fpython%2Fvmss-bottle-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/vmss-bottle-autoscale/).

:::code language="json" source="~/quickstart-templates/application-workloads/python/vmss-bottle-autoscale/azuredeploy.json":::

Diese Ressourcen sind in der Vorlage definiert:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>Definieren einer Skalierungsgruppe

Für die Erstellung einer Skalierungsgruppe mit einer Vorlage definieren Sie die entsprechenden Ressourcen. Dies sind die wichtigsten Bestandteile des Ressourcentyps einer VM-Skalierungsgruppe:

| Eigenschaft                     | Beschreibung der Eigenschaft                                  | Beispiel für Vorlagenwert                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Zu erstellender Azure-Ressourcentyp                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Name der Skalierungsgruppe                                       | myScaleSet                                |
| location                     | Standort für die Erstellung der Skalierungsgruppe                     | East US                                   |
| sku.name                     | VM-Größe für die einzelnen Skalierungsgruppeninstanzen                  | Standard_A1                               |
| sku.capacity                 | Anzahl von VM-Instanzen für die anfängliche Erstellung           | 2                                         |
| upgradePolicy.mode           | Upgrademodus für VM-Instanz bei Auftreten von Änderungen              | Automatic                                 |
| imageReference               | Plattform- oder benutzerdefiniertes Image für die VM-Instanzen | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Namenspräfix für die einzelnen VM-Instanzen                     | myvmss                                    |
| osProfile.adminUsername      | Benutzername für die einzelnen VM-Instanzen                        | azureuser                                 |
| osProfile.adminPassword      | Kennwort für die einzelnen VM-Instanzen                        | P@ssw0rd!                                 |

Zum Anpassen einer Skalierungsgruppenvorlage können Sie die VM-Größe oder die anfängliche Kapazität ändern. Eine weitere Möglichkeit ist die Verwendung einer anderen Plattform oder eines benutzerdefinierten Image.

### <a name="add-a-sample-application"></a>Hinzufügen einer Beispielanwendung

Installieren Sie eine einfache Webanwendung, um Ihre Skalierungsgruppe zu testen. Beim Bereitstellen einer Skalierungsgruppe können über VM-Erweiterungen Aufgaben für die Konfiguration und Automatisierung nach der Bereitstellung, z.B. das Installieren einer App, bereitgestellt werden. Skripts können aus Azure Storage oder GitHub heruntergeladen oder dem Azure-Portal zur Laufzeit für die Erweiterung bereitgestellt werden. Fügen Sie dem obigen Ressourcenbeispiel den Abschnitt *extensionProfile* hinzu, um eine Erweiterung auf Ihre Skalierungsgruppe anzuwenden. Im Erweiterungsprofil werden normalerweise die folgenden Eigenschaften definiert:

- Erweiterungstyp
- Erweiterungsherausgeber
- Erweiterungsversion
- Speicherort der Konfiguration bzw. der Installationsskripts
- Auf den VM-Instanzen auszuführende Befehle

Von der die Vorlage wird die benutzerdefinierte Skripterweiterung verwendet, um [Bottle](https://bottlepy.org/docs/dev/), ein Python-Webframework und einen einfachen HTTP-Server zu installieren.

Zwei Skripts sind in **fileUris** - *installserver.sh* und *workserver.py* definiert. Diese Dateien werden von GitHub heruntergeladen. Anschließend wird von *commandToExecute* der Befehl `bash installserver.sh` ausgeführt, um die App zu installieren und zu konfigurieren.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Sie können die Vorlage bereitstellen, indem Sie weiter unten die Schaltfläche **In Azure bereitstellen** auswählen. Diese Schaltfläche bewirkt Folgendes: Das Azure-Portal wird geöffnet, die vollständige Vorlage wird geladen, und es werden einige Parameter abgefragt, z.B. der Name der Skalierungsgruppe, die Instanzanzahl und die Administratoranmeldeinformationen.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fpython%2Fvmss-bottle-autoscale%2Fazuredeploy.json)

Sie können eine Resource Manager-Vorlage auch mithilfe der Azure-Befehlszeilenschnittstelle bereitstellen:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az deployment group create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/python/vmss-bottle-autoscale/azuredeploy.json
```

Folgen Sie den Aufforderungen zum Angeben des Namens einer Skalierungsgruppe, der Instanzanzahl und der Administratoranmeldeinformationen für die VM-Instanzen. Es dauert einige Minuten, bis die Skalierungsgruppe und die unterstützenden Ressourcen erstellt werden.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Greifen Sie über einen Webbrowser auf die Beispielwebanwendung zu, um Ihre Skalierungsgruppe in Aktion zu sehen. Rufen Sie mit [az network public-ip list](/cli/azure/network/public-ip) die öffentliche IP-Adresse des Lastenausgleichs ab:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Geben Sie die öffentliche IP-Adresse des Lastenausgleichsmoduls in einem Webbrowser im Format *http:\//publicIpAddress:9000/do_work* ein. Das Lastenausgleichsmodul verteilt den Datenverkehr auf eine Ihrer VM-Instanzen. Dies ist im folgenden Beispiel dargestellt:

![Standardwebseite in NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, die Skalierungsgruppe und die dazugehörigen Ressourcen nicht mehr benötigen, können Sie sie wie folgt mit dem Befehl [az group delete](/cli/azure/group) entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Linux-Skalierungsgruppe mit einer ARM-Vorlage erstellt und die benutzerdefinierte Skripterweiterung verwendet, um einen einfachen Python-Webserver auf den VM-Instanzen zu installieren. Im nächsten Tutorial erfahren Sie mehr über die Erstellung und Verwaltung von Azure-VM-Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Azure-VM-Skalierungsgruppen](tutorial-create-and-manage-cli.md)
