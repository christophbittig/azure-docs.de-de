---
title: Verwenden von Erweiterungen mit Azure Batch-Pools
description: Erweiterungen sind kleine Anwendungen, die Konfiguration und Setup nach der Bereitstellung auf Azure Batch-Computeknoten erleichtern.
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: bac3c6c53552ac99aef6210cbe184da754580ca0
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508682"
---
# <a name="use-extensions-with-batch-pools"></a>Verwenden von Erweiterungen mit Azure Batch-Pools

Erweiterungen sind kleine Anwendungen, die Konfiguration und Setup nach der Bereitstellung auf Azure Batch-Computeknoten erleichtern. Sie können jede der von Azure Batch zugelassenen Erweiterungen auswählen und sie auf den Computeknoten bei deren Bereitstellung installieren lassen. Danach kann die Erweiterung ihre vorgesehene Aufgabe erfüllen.

Sie können den Livestatus der von Ihnen verwendeten Erweiterungen überprüfen und die von den Erweiterungen zurückgegebenen Informationen abrufen, um etwaige Erkennungs-, Korrektur- oder Diagnosefunktionen zu verfolgen.

## <a name="prerequisites"></a>Voraussetzungen

- Pools mit Erweiterungen müssen die [VM-Konfiguration](nodes-and-pools.md#virtual-machine-configuration) verwenden.
- Der Erweiterungstyp „CustomScript“ ist für den Azure Batch-Dienst reserviert und kann nicht außer Kraft gesetzt werden.
- Einige Erweiterungen benötigen möglicherweise eine verwaltete Identität auf Poolebene, auf die im Kontext eines Computeknotens zugegriffen werden kann, um ordnungsgemäß zu funktionieren. Weitere Informationen finden Sie unter [Konfigurieren von verwalteten Identitäten in Batchpools](managed-identity-pools.md), falls dies für die Erweiterung(en) gilt.

## <a name="supported-extensions"></a>Unterstützte Erweiterungen

Die folgenden Erweiterungen können derzeit beim Erstellen eines Azure Batch-Pools installiert werden:

- [Azure Key Vault Erweiterung für Linux](../virtual-machines/extensions/key-vault-linux.md)
- [Azure Key Vault Erweiterung für Windows](../virtual-machines/extensions/key-vault-windows.md)
- [Azure Monitor Protokollanalysen und Überwachungserweiterung für Linux](../virtual-machines/extensions/oms-linux.md)
- [Azure Monitor Protokollanalysen und Überwachungserweiterung für Windows](../virtual-machines/extensions/oms-windows.md)
- [Erweiterung der Azure Desired State Configuration (DSC, Konfigurieren des gewünschten Status)](../virtual-machines/extensions/dsc-overview.md)
- [Azure Diagnostics Erweiterung für virtuelle Windows-Computer](../virtual-machines/windows/extensions-diagnostics.md)
- [HPC-GPU-Treibererweiterung für Windows auf AMD](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md)
- [HPC-GPU-Treibererweiterung für Windows auf NVIDIA](../virtual-machines/extensions/hpccompute-gpu-windows.md)
- [Microsoft Antimalware-Erweiterung für Windows](../virtual-machines/extensions/iaas-antimalware-windows.md)

Sie können Unterstützung für weitere Herausgeber und/oder Erweiterungstypen anfordern, indem Sie eine Supportanfrage öffnen.

## <a name="create-a-pool-with-extensions"></a>Erstellen eines Pools mit Erweiterungen

Im folgenden Beispiel wird ein Azure Batch-Pool mit Linux-Knoten erstellt, in dem die Azure Key Vault-Erweiterung verwendet wird.

REST-API-URI

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Anforderungstext

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Erweiterungsdaten aus einem Pool abrufen

Im folgenden Beispiel werden Daten aus der Azure Key Vault-Erweiterung abgerufen.

REST-API-URI

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Antworttext

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die verschiedenen Möglichkeiten, [Anwendungen und Daten auf Poolknoten zu kopieren](batch-applications-to-pool-nodes.md).
- Erfahren Sie mehr über das Arbeiten mit [Knoten und Pools](nodes-and-pools.md).
