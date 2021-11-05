---
title: Bereitstellen von verwaltete Datenträgern für Service Fabric-Knotentypen
description: Hier erfahren Sie, wie Sie Service Fabric-Knotentypen mit angefügten verwalteten Datenträgern erstellen und bereitstellen.
author: craftyhouse
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: micraft
ms.openlocfilehash: 11660a3bb10e7004ce78d2788f93ac6fc2fc821d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094950"
---
# <a name="deploy-an-azure-service-fabric-cluster-node-type-with-managed-data-diskspreview"></a>Bereitstellen eines Azure Service Fabric-Clusterknotentyps mit verwalteten Datenträgern (Vorschau)

Service Fabric-Knotentypen verwenden standardmäßig auf jeder VM in der zugrunde liegenden VM-Skalierungsgruppe den temporären Datenträger für die Datenspeicherung. Da der temporäre Datenträger jedoch nicht persistent ist und die Größe des temporären Datenträgers an eine bestimmte VM-SKU gebunden ist, kann dies für einige Szenarios zu restriktiv sein. Mit Azure Managed Disks verfügen Kunden über einen persistenten Datenträger, auf dem sie die Größe und Leistung für einen Knotentyp getrennt von einer VM-SKU angeben können. Das folgende Dokument enthält die Schritte zur Verwendung der nativen Unterstützung von Service Fabric, um Azure Managed Disks als Standarddatenpfad zu konfigurieren und zu verwenden. Service Fabric konfiguriert Azure Managed Disks automatisch bei der Erstellung des Knotentyps und behandelt Situationen, in denen für VMs oder die VM-Skalierungsgruppe ein Reimaging durchgeführt wird.

* Die erforderliche Mindestgröße für den verwalteten Datenträger beträgt 50 GB.
* In Szenarios, in denen mehr als ein verwalteter Datenträger angefügt ist, müssen Kund*innen die Datenträger selbst verwalten.

## <a name="configuring-virtual-machine-scale-set-to-use-managed-data-disks-in-service-fabric"></a>Konfigurieren einer VM-Skalierungsgruppe für die Verwendung von verwalteten Datenträgern in Service Fabric
Konfigurieren Sie die zugrunde liegende VM-Skalierungsgruppenressource wie folgt, um Azure Managed Disks auf einem Knotentyp zu verwenden:

* Fügen Sie in der Vorlage für die VM-Skalierungsgruppe im Abschnitt Datenträger einen verwalteten Datenträger hinzu. 
* Aktualisieren Sie die Service Fabric-Erweiterung mit den folgenden Einstellungen: 
    * Für Windows: **useManagedDataDisk: true** und **dataPath: 'K:\\\\SvcFab'** .  Beachten Sie, dass der Laufwerkbuchstabe „K“ nur eine Darstellung ist. Es kann ein beliebiger Laufwerkbuchstabe sein, der lexikografisch größer ist als die gesamten Laufwerkbuchstaben, die in der SKU der VM-Skalierungsgruppe vorhanden ist.
    * For Linux: **useManagedDataDisk:true** und **dataPath: '\mnt\sfdataroot'** .

>[!NOTE]
> Unterstützung für verwaltete Datenträger für Linux Service Fabric-Cluster ist derzeit nicht für die Produktion verfügbar. Kund*innen können sie jedoch mithilfe der Erweiterung „Service Fabric testen“ für Linux testen.

Azure Resource Manager-Vorlage für die Service Fabric-Erweiterung
```json
{
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": false,
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
                            "dataPath": "K:\\\\SvcFab",
                            "useManagedDataDisk": true,
                            "durabilityLevel": "Bronze",
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            },
                            "systemLogUploadSettings": {
                                "Enabled": true
                            },
                        },
                        "typeHandlerVersion": "1.1"
                    }
                },
            ]
        },
        "storageProfile": 
        {
            "datadisks": [
                {
                    "lun": "1",
                    "createOption": "empty",
                    "diskSizeGB": "100",
                    "managedDisk": { "storageAccountType": "Standard_LRS" }
                }
            ]
        }
    }
}
```

## <a name="migrate-to-using-managed-data-disks-for-service-fabric-node-types"></a>Migrieren zur Verwendung verwalteter Datenträger für Service Fabric-Knotentypen
* Für alle Migrationsszenarios muss ein neuer Knotentyp hinzugefügt werden, der verwaltete Datenträger wie oben angegeben verwendet.
* Migrieren Sie die Workloads zu den neuen Knotentypen, nachdem neue Knotentypen hinzugefügt wurden.
* Nachdem die Bereitstellung der Ressourcen abgeschlossen wurde, können Sie beginnen, die Knoten im Knotentyp zu deaktivieren, den Sie aus dem ursprünglichen Cluster entfernen möchten.

## <a name="next-steps"></a>Nächste Schritte 
* [Service Fabric: Übersicht](service-fabric-reliable-services-introduction.md)
* [Knotentypen und VM-Skalierungsgruppen](service-fabric-cluster-nodetypes.md)
* [Service Fabric-Kapazitätsplanung](service-fabric-best-practices-capacity-scaling.md)
