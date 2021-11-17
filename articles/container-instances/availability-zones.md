---
title: Bereitstellen einer zonalen Containergruppe in Azure Container Instances (ACI)
description: Erfahren Sie, wie Sie eine Containergruppe in einer Verfügbarkeitszone bereitstellen.
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: a2cce289dc95ecc876ba308ed9406fffb37b9e18
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858587"
---
# <a name="deploy-an-azure-container-instances-aci-container-group-in-an-availability-zone-preview"></a>Bereitstellen einer ACI-Containergruppe (Azure Container Instances) in einer Verfügbarkeitszone (Vorschau)

Eine [Verfügbarkeitszone][availability-zone-overview] ist eine physisch separate Zone in einer Azure-Region. Sie können Verfügbarkeitszonen verwenden, um Ihre containerisierten Anwendungen vor einem unwahrscheinlichen Ausfall oder Verlust eines ganzen Rechenzentrums zu schützen. Azure Container Instances (ACI) unterstützt zonale Containergruppenbereitstellungen, was bedeutet, dass die Instanz an eine bestimmte, selbst ausgewählte Verfügbarkeitszone angeheftet ist. Die Verfügbarkeitszone wird auf Containergruppenebene angegeben. Container innerhalb einer Containergruppe können keine eindeutigen Verfügbarkeitszonen haben. Um die Verfügbarkeitszone Ihrer Containergruppe zu ändern, müssen Sie die Containergruppe löschen und eine weitere Containergruppe mit der neuen Verfügbarkeitszone erstellen.

> [!IMPORTANT]
> Diese Funktion steht derzeit als Vorschau zur Verfügung. Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die zusätzlichen Nutzungsbedingungen akzeptieren.

> [!IMPORTANT]
> Zonale Containergruppenbereitstellungen werden in den meisten Regionen unterstützt, in denen ACI für Linux- und Windows Server 2019-Containergruppen verfügbar ist. Details hierzu finden Sie unter [Verfügbarkeit von Regionen und Ressourcen][container-regions].

> [!NOTE]
> Beispiele in diesem Artikel sind für die Ausführung in der Bash-Shell formatiert. Wenn Sie eine andere Shell bevorzugen, passen Sie die Zeilenfortsetzungszeichen entsprechend an.

## <a name="limitations"></a>Einschränkungen

> [!IMPORTANT]
> Dieses Feature ist derzeit für das Azure-Portal nicht verfügbar.

* Containergruppen mit GPU-Ressourcen unterstützen derzeit keine Verfügbarkeitszonen.
* In virtuelle Netzwerke eingefügte Containergruppen unterstützen derzeit keine Verfügbarkeitszonen.
* Windows Server 2016-Containergruppen unterstützen derzeit keine Verfügbarkeitszonen.

### <a name="version-requirements"></a>Versionsanforderungen

* Wenn Sie Azure CLI verwenden, stellen Sie sicher, dass Version `2.30.0` oder höher installiert ist.
* Wenn Sie PowerShell verwenden, stellen Sie sicher, dass Version `2.1.1-preview` oder höher installiert ist.
* Wenn Sie das Java-SDK verwenden, stellen Sie sicher, dass Version `2.9.0` oder höher installiert ist.
* Die Unterstützung von Verfügbarkeitszonen ist nur für die ACI-API-Version `09-01-2021` oder höher verfügbar.

## <a name="deploy-a-container-group-using-an-azure-resource-manager-arm-template"></a>Bereitstellen einer Containergruppe mit einer Azure Resource Manager-Vorlage (ARM)

### <a name="create-the-arm-template"></a>Erstellen der ARM-Vorlage

Beginnen Sie, indem Sie den folgenden JSON-Code in eine neue Datei mit dem Namen `azuredeploy.json` kopieren. Diese Beispielvorlage stellt eine Containergruppe mit einem einzelnen Container in der Verfügbarkeitszone 1 in der Region „USA, Osten“ bereit.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "_generator": {
            "name": "bicep",
            "version": "0.4.1.14562",
            "templateHash": "12367894147709986470"
        }
    },
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "acilinuxpublicipcontainergroup",
            "metadata": {
                "description": "Name for the container group"
            }
        },
        "image": {
            "type": "string",
            "defaultValue": "mcr.microsoft.com/azuredocs/aci-helloworld",
            "metadata": {
                "description": "Container image to deploy. Should be of the form repoName/imagename:tag for images stored in public Docker Hub, or a fully qualified URI for other registries. Images from private registries require additional registry credentials."
            }
        },
        "port": {
            "type": "int",
            "defaultValue": 80,
            "metadata": {
                "description": "Port to open on the container and the public IP address."
            }
        },
        "cpuCores": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The number of CPU cores to allocate to the container."
            }
        },
        "memoryInGb": {
            "type": "int",
            "defaultValue": 2,
            "metadata": {
                "description": "The amount of memory to allocate to the container in gigabytes."
            }
        },
        "restartPolicy": {
            "type": "string",
            "defaultValue": "Always",
            "allowedValues": [
                "Always",
                "Never",
                "OnFailure"
            ],
            "metadata": {
                "description": "The behavior of Azure runtime if container has stopped."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "eastus",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2021-09-01",
            "zones": [
                "1"
            ],
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "properties": {
                "containers": [
                    {
                        "name": "[parameters('name')]",
                        "properties": {
                            "image": "[parameters('image')]",
                            "ports": [
                                {
                                    "port": "[parameters('port')]",
                                    "protocol": "TCP"
                                }
                            ],
                            "resources": {
                                "requests": {
                                    "cpu": "[parameters('cpuCores')]",
                                    "memoryInGB": "[parameters('memoryInGb')]"
                                }
                            }
                        }
                    }
                ],
                "osType": "Linux",
                "restartPolicy": "[parameters('restartPolicy')]",
                "ipAddress": {
                    "type": "Public",
                    "ports": [
                        {
                            "port": "[parameters('port')]",
                            "protocol": "TCP"
                        }
                    ]
                }
            }
        }
    ],
    "outputs": {
        "containerIPv4Address": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups', parameters('name'))).ipAddress.ip]"
        }
    }
}
```

### <a name="deploy-the-arm-template"></a>Bereitstellen der ARM-Vorlage

Erstellen Sie mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe:

```azurecli
az group create --name myResourceGroup --location eastus
```

Stellen Sie die Vorlage mit dem Befehl [az deployment group create][az-deployment-group-create] bereit:

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

## <a name="get-container-group-details"></a>Abrufen von Containergruppendetails

Um zu überprüfen, ob die Containergruppe erfolgreich in einer Verfügbarkeitszone bereitgestellt wurde, zeigen Sie die Details der Containergruppe mit dem Befehl [az container show][az-container-show] an:

```azurecli
az containershow --name acilinuxcontainergroup --resource-group myResourceGroup
```

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[container-regions]: container-instances-region-availability.md
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment#az_deployment_group_create
[availability-zone-overview]: /availability-zones/az-overview.md