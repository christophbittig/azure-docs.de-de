---
title: 'Bereitstellen eines Azure-Clouddiensts (erweiterter Support): Vorlagen'
description: Bereitstellen eines Azure-Clouddiensts (erweiterter Support) mithilfe von ARM-Vorlagen
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: de46eb9fca550d95caa8aa5d42449ab10d1bb9a2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741114"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Bereitstellen eines Clouddiensts (erweiterter Support) mithilfe von ARM-Vorlagen

In diesem Tutorial erfahren Sie, wie Sie mithilfe von [ARM-Vorlagen](../azure-resource-manager/templates/overview.md) eine Clouddienstbereitstellung mit erweitertem Support erstellen. 

## <a name="before-you-begin"></a>Vorbereitung

1. Informieren Sie sich über die [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Cloud Services (erweiterter Support), und erstellen Sie die entsprechenden Ressourcen.

2. Erstellen Sie über das [Azure-Portal](../azure-resource-manager/management/manage-resource-groups-portal.md) oder mithilfe von [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md) eine neue Ressourcengruppe. Bei Verwendung einer vorhandenen Ressourcengruppe ist dieser Schritt optional.
 
3. Erstellen Sie über das [Azure-Portal](../storage/common/storage-account-create.md?tabs=azure-portal) oder mithilfe von [PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell) ein neues Speicherkonto. Bei Verwendung eines vorhandenen Speicherkontos ist dieser Schritt optional.

4. Laden Sie Ihr Paket (.cspkg) und die Dienstkonfigurationsdatei (.cscfg) per [Azure-Portal](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) oder [PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container) in das Speicherkonto hoch. Rufen Sie die SAS-URIs der beiden Dateien ab. Sie werden später in diesem Tutorial der ARM-Vorlage hinzugefügt.

5. (Optional): Erstellen Sie einen Schlüsseltresor, und laden Sie die Zertifikate hoch.

    -  Zertifikate können an Clouddienste angefügt werden, um eine sichere Kommunikation mit dem Dienst zu ermöglichen. Um Zertifikate verwenden zu können, muss deren Fingerabdruck in Ihrer Dienstkonfigurationsdatei (CSCFG-Datei) angegeben und in einen Schlüsseltresor hochgeladen werden. Ein Schlüsseltresor kann über das [Azure-Portal](../key-vault/general/quick-create-portal.md) oder mithilfe von [PowerShell](../key-vault/general/quick-create-powershell.md) erstellt werden.
    - Der zugeordnete Schlüsseltresor muss in der gleichen Region und im gleichen Abonnement erstellt werden wie der Clouddienst.
    - Für den zugeordneten Schlüsseltresor müssen außerdem geeignete Berechtigungen aktiviert werden, damit die Ressource vom Typ „Cloud Services (erweiterter Support)“ das Zertifikat aus Key Vault abrufen kann. Weitere Informationen finden Sie unter [Verwenden von Zertifikaten mit Azure Cloud Services (erweiterter Support)](certificates-and-key-vault.md).
    - Auf den Schlüsseltresor muss im Abschnitt „OsProfile“ der ARM-Vorlage verwiesen werden, wie in den folgenden Schritten gezeigt.

## <a name="deploy-a-cloud-service-extended-support"></a>Bereitstellen eines Clouddiensts (erweiterter Support)

> [!NOTE]
> Die Erstellung der ARM-Vorlage und Parameterdatei über das [Azure-Portal](https://portal.azure.com) ist einfacher und schneller. Sie können die [generierte ARM-Vorlage über das Portal herunterladen](generate-template-portal.md), um Ihren Clouddienst über PowerShell zu erstellen.
 
1. Erstellen Sie ein virtuelles Netzwerk. Der Name des virtuellen Netzwerks muss den Verweisen in der Dienstkonfigurationsdatei (.cscfg) entsprechen. Sollten Sie ein vorhandenes virtuelles Netzwerk verwenden, überspringen Sie diesen Abschnitt in der ARM-Vorlage.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Wenn Sie ein neues virtuelles Netzwerk erstellen, fügen Sie dem Abschnitt `dependsOn` Folgendes hinzu, um sicherzustellen, dass das virtuelle Netzwerk vor der Erstellung des Clouddiensts erstellt wird:

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Erstellen Sie eine öffentliche IP-Adresse, und legen Sie (optional) die DNS-Bezeichnungseigenschaft der öffentlichen IP-Adresse fest. Wenn Sie eine statische IP-Adresse verwenden, muss darauf in der Dienstkonfigurationsdatei (.cscfg) als reservierte IP verwiesen werden. Wenn Sie eine vorhandene IP-Adresse verwenden, überspringen Sie diesen Schritt, und fügen Sie die IP-Adressinformationen in Ihrer ARM-Vorlage direkt den Konfigurationseinstellungen für den Lastenausgleich hinzu.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Wenn Sie eine neue IP-Adresse erstellen, fügen Sie dem Abschnitt `dependsOn` Folgendes hinzu, um sicherzustellen, dass die IP-Adresse vor der Erstellung des Clouddiensts erstellt wird:
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Erstellen Sie ein Clouddienstobjekt (erweiterter Support), und fügen Sie entsprechende `dependsOn`-Verweise hinzu, wenn Sie virtuelle Netzwerke oder öffentliche IP-Adressen in Ihrer Vorlage bereitstellen. 

    ```json
    {
      "apiVersion": "2021-03-01",
      "type": "Microsoft.Compute/cloudServices",
      "name": "[variables('cloudServiceName')]",
      "location": "[parameters('location')]",
      "tags": {
        "DeploymentLabel": "[parameters('deploymentLabel')]",
        "DeployFromVisualStudio": "true"
      },
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
        "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
      ],
      "properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]"
      }
    }
    ```
4. Erstellen Sie ein Netzwerkprofilobjekt für Ihren Clouddienst, und ordnen Sie die öffentliche IP-Adresse dem Front-End des Lastenausgleichs zu. Ein Lastenausgleich wird automatisch von der Plattform erstellt. 

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

5. Fügen Sie im Abschnitt `OsProfile` der ARM-Vorlage Ihren Schlüsseltresorverweis hinzu. Key Vault wird zum Speichern von Zertifikaten verwendet, die Cloud Services (erweiterter Support) zugeordnet sind. Fügen Sie Key Vault die Zertifikate hinzu, und verweisen Sie dann in der Dienstkonfigurationsdatei (.cscfg) auf die Zertifikatfingerabdrücke. Außerdem müssen Sie in Key Vault „Zugriffsrichtlinien“ für „Azure Virtual Machines für Bereitstellung“ (im Portal) aktivieren, damit Cloud Services (erweiterter Support) als Geheimnisse gespeicherte Zertifikate aus Key Vault abrufen kann. Der Schlüsseltresor muss in der gleichen Region und im gleichen Abonnement erstellt werden wie der Clouddienst und einen eindeutigen Namen besitzen. Weitere Informationen finden Sie unter [Verwenden von Zertifikaten mit Azure Cloud Services (erweiterter Support)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault ist die ARM-Ressourcen-ID für Ihren Schlüsseltresor. Sie finden diese Informationen, indem Sie im Eigenschaftenabschnitt Ihres Schlüsseltresors nach der Ressourcen-ID suchen.
    > - „certificateUrl“ finden Sie, indem Sie im Schlüsseltresor mit dem Bezeichner **Geheimnis-ID** zu dem Zertifikat navigieren.  
   >  - „certificateUrl“ muss folgendes Format haben: https://{Schlüsseltresor-Endpunkt}/secrets/{Geheimnisname}/{Geheimnis-ID}.

6. Erstellen Sie ein Rollenprofil. Stellen Sie sicher, dass die Anzahl von Rollen, Rollennamen und Instanzen in den einzelnen Rollen sowie die Größen in der Dienstkonfiguration (.cscfg), in der Dienstdefinition (.csdef) und im Rollenprofilabschnitt der ARM-Vorlage jeweils identisch sind.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

7. Optional: Erstellen Sie ein Erweiterungsprofil, um Ihrem Clouddienst Erweiterungen hinzuzufügen. In diesem Beispiel fügen wir die Erweiterung für Remotedesktop und für die Windows Azure-Diagnose hinzu.
   > [!Note] 
   > Das Kennwort für Remotedesktop muss zwischen 8 und 123 Zeichen lang sein und mindestens 3 der folgenden Kennwortkomplexitätsanforderungen erfüllen: 1) Enthält einen Großbuchstaben 2) Enthält einen Kleinbuchstaben 3) Enthält eine Ziffer 4) Enthält ein Sonderzeichen 5) Steuerzeichen sind unzulässig.

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

8. Überprüfen Sie die gesamte Vorlage.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2021-03-01",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

9. Stellen Sie die Vorlagen- und Parameterdatei (definierende Parameter in der Vorlagendatei) zum Erstellen der Clouddienstbereitstellung (erweiterter Support) bereit. Weitere Informationen finden Sie bei Bedarf in [diesen Beispielvorlagen](https://github.com/Azure-Samples/cloud-services-extended-support).

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Nächste Schritte 

- Sehen Sie sich die [häufig gestellten Fragen](faq.yml) zu Cloud Services (erweiterter Support) an.
- Stellen Sie einen Clouddienst (erweiterter Support) über das [Azure-Portal](deploy-portal.md), per [PowerShell](deploy-powershell.md), per [Vorlage](deploy-template.md) oder mithilfe von [Visual Studio](deploy-visual-studio.md) bereit.
- Besuchen Sie das [Beispielrepository zu Cloud Services (erweiterter Support)](https://github.com/Azure-Samples/cloud-services-extended-support).
