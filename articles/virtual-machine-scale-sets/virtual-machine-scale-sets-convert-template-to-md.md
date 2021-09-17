---
title: Konvertieren einer Skalierungsgruppenvorlage für die Verwendung verwalteter Datenträger
description: Konvertieren Sie eine Azure Resource Manager-Vorlage für VM-Skalierungsgruppen in eine Skalierungsgruppenvorlage für verwaltete Datenträger.
keywords: Skalierungsgruppen für virtuelle Computer
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2cf6fc21baae657b28b7d3edfb242ee1bcef407c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690501"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konvertieren einer Skalierungsgruppenvorlage in eine Skalierungsgruppenvorlage für verwaltete Datenträger

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Kunden mit einer Resource Manager-Vorlage für die Erstellung einer Skalierungsgruppe ohne verwaltete Datenträger möchten diese Vorlage unter Umständen anpassen, um verwaltete Datenträger zu verwenden. In diesem Artikel wird die Vorgehensweise zur Verwendung von verwalteten Datenträgern am Beispiel eines Pull Requests aus den [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) (communitygestütztes Repository für Resource Manager-Beispielvorlagen) beschrieben. Die vollständige Pull-Anforderung finden Sie hier: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998). Im Anschluss finden Sie die relevanten Teile des Diffs sowie die dazugehörigen Erläuterungen:

## <a name="making-the-os-disks-managed"></a>Umwandeln der Betriebssystem-Datenträger in verwaltete Datenträger

Im folgenden Diff wurden mehrere Variablen für Speicherkonto- und Datenträgereigenschaften entfernt. Der Speicherkontotyp wird nicht mehr benötigt („Standard_LRS“ ist die Standardeinstellung), Sie können ihn aber auf Wunsch trotzdem angeben. Für verwaltete Datenträger werden nur „Standard_LRS“ und „Premium_LRS“ unterstützt. „newStorageAccountSuffix“, „uniqueStringArray“ und „saCount“ wurden in der alten Vorlage zum Generieren von Speicherkontonamen verwendet. Diese Variablen werden in der neuen Vorlage nicht mehr benötigt, da verwaltete Datenträger automatisch Speicherkonten für den Kunden erstellen. Auch der Name des VHD-Containers und der des Betriebssystemdatenträgers werden nicht mehr benötigt, da verwaltete Datenträger die zugrunde liegenden Storage Blob-Container und Datenträger automatisch benennen.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Im folgenden Diff wird Ihre Compute-API auf die Version „2016-04-30-preview“ aktualisiert. Hierbei handelt es sich um die früheste erforderliche Version für die Unterstützung verwalteter Datenträger mit Skalierungsgruppen. In der neuen API-Version können Sie auf Wunsch nicht verwaltete Datenträger mit der alten Syntax verwenden. Wenn Sie nur die Compute-API-Version aktualisieren und alles andere unverändert lassen, sollte die Vorlage genau wie vorher funktionieren.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Im folgenden Diff wird die Speicherkontoressource vollständig aus dem Ressourcenarray entfernt. Die Ressource ist nicht mehr erforderlich, da sie von verwalteten Datenträgern automatisch erstellt wird.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Im folgenden Diff sehen Sie, dass wir die dependsOn-Klausel entfernen, die von der Skalierungsgruppe auf die Schleife für die Speicherkontenerstellung verweist. In der alten Vorlage wurde dadurch sichergestellt, dass Speicherkonten erstellt werden, bevor mit der Erstellung der Skalierungsgruppe begonnen wird. Bei verwalteten Datenträgern ist diese Klausel allerdings nicht mehr erforderlich. Die vhdContainers-Eigenschaft und die osDiskName-Eigenschaft werden ebenfalls entfernt, da diese von verwalteten Datenträgern automatisch behandelt werden. Um Premium-Betriebssystemdatenträger zu erhalten, könnten Sie der osDisk-Konfiguration `"managedDisk": { "storageAccountType": "Premium_LRS" }` hinzufügen. Nur virtuelle Computer, deren SKU ein groß oder klein geschriebenes „s“ enthält, können Premium-Datenträger verwenden.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

In der Skalierungsgruppenkonfiguration steht keine explizite Eigenschaft für die Verwendung verwalteter oder nicht verwalteter Datenträger zur Verfügung. Die zu verwendende Option wird anhand der im Speicherprofil vorhandenen Eigenschaften ermittelt. Daher ist beim Ändern der Vorlage darauf zu achten, dass das Speicherprofil der Skalierungsgruppe die richtigen Eigenschaften enthält.


## <a name="data-disks"></a>Datenträger

Mit den obigen Änderungen verwendet die Skalierungsgruppe verwaltete Datenträger für den Betriebssystem-Datenträger, aber was ist mit „normalen“ Datenträgern? Wenn Sie Datenträger hinzufügen möchten, fügen Sie die dataDisks-Eigenschaft unter „storageProfile“ auf der gleichen Ebene hinzu wie „osDisk“. Der Wert der Eigenschaft ist eine JSON-Liste mit Objekten. Jedes dieser Objekte verfügt über die Eigenschaften „lun“ (muss für jeden Datenträger eines virtuellen Computers eindeutig sein), „createOption“ (einzige derzeit unterstütze Option: „empty“) und „diskSizeGB“ (Größe des Datenträgers in GB; muss größer0 und kleiner 1024 sein), wie im folgenden Beispiel zu sehen:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Wenn Sie in diesem Array `n` Datenträger angeben, erhält jeder virtuelle Computer in der Skalierungsgruppe `n` Datenträger. Beachten Sie jedoch, dass es sich bei diesen Datenträgern um Rohmedien handelt. Sie sind nicht formatiert. Der Kunde muss die Datenträger daher vor der Verwendung anfügen, partitionieren und formatieren. Optional könnten Sie mit `"managedDisk": { "storageAccountType": "Premium_LRS" }` in jedem Datenträgerobjekt angeben, dass es sich um einen Premium-Datenträger handeln soll. Nur virtuelle Computer, deren SKU ein groß oder klein geschriebenes „s“ enthält, können Premium-Datenträger verwenden.

Weitere Informationen zur Verwendung von Datenträgern mit Skalierungsgruppen finden Sie in [diesem Artikel](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie Beispiele für Resource Manager-Vorlagen ansehen möchten, die Skalierungsgruppen verwenden, suchen Sie im [GitHub-Repository für Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) nach „vmss“.

Allgemeine Informationen finden Sie auf der [Hauptseite für Skalierungsgruppen](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

