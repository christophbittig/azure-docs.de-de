---
title: Verwenden umfangreicher Azure VM-Skalierungsgruppen
description: Was Sie über umfangreiche Azure VM-Skalierungsgruppen wissen müssen, um Sie in Ihrer Anwendung zu verwenden.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c3171b9ffd15fe7dd9e13b3fdde1a3394efb6327
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697375"
---
# <a name="working-with-large-virtual-machine-scale-sets"></a>Verwenden umfangreicher VM-Skalierungsgruppen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Sie können nun [Azure-VM-Skalierungsgruppen](./index.yml) mit einer Kapazität von bis zu 1.000 virtuellen Computern erstellen. Eine _umfangreiche VM-Skalierungsgruppe_ ist in diesem Dokument als Skalierungsgruppe mit mehr als 100 virtuellen Computern definiert. Diese Funktion wird über eine Skalierungsgruppeneigenschaft (_singlePlacementGroup=False_) festgelegt. 

Bestimmte Aspekte umfangreicher Skalierungsgruppen (wie etwa Lastenausgleich und Fehlerdomänen) verhalten sich anders als bei Standardskalierungsgruppen. In diesem Dokument werden die Merkmale umfangreicher Skalierungsgruppen erläutert, und Sie erfahren, wie Sie sie erfolgreich in Ihren Anwendungen verwenden. 

Eine gängige Methode für die Bereitstellung umfangreicher Cloudinfrastrukturen ist die Erstellung eines Satzes von _Skalierungseinheiten_ – beispielsweise durch Erstellen mehrerer VM-Skalierungsgruppen in mehreren VNets und Speicherkonten. Verglichen mit einzelnen virtuellen Computern ermöglicht dieser Ansatz eine einfachere Verwaltung. Mehrere Skalierungseinheiten sind zudem bei zahlreichen Anwendungen hilfreich. Dies gilt insbesondere für Anwendungen, die andere stapelbare Komponenten (etwa mehrere virtuelle Netzwerke und Endpunkte) erfordern. Falls Ihre Anwendung allerdings einen einzelnen großen Cluster erfordert, ist es unter Umständen einfacher, eine einzelne Skalierungsgruppe mit bis zu 1.000 virtuellen Computern bereitzustellen. Beispielszenarien wären etwa zentrale Big Data-Bereitstellungen oder Computenetze, die eine einfache Verwaltung eines umfangreichen Pools von Workerknoten erfordern. In Kombination mit [angefügten Datenträgern](virtual-machine-scale-sets-attached-disks.md) für VM-Skalierungsgruppen können Sie dank umfangreicher Skalierungsgruppen in einem einzelnen Vorgang eine skalierbare Infrastruktur mit Tausenden von vCPUs und einem Speicherplatz von mehreren Petabytes bereitstellen.

## <a name="placement-groups"></a>Platzierungsgruppen 
Die Besonderheit einer _umfangreichen_ Skalierungsgruppe ist nicht die Anzahl virtueller Computer, sondern die Anzahl enthaltener _Platzierungsgruppen_. Eine Platzierungsgruppe ist ein ähnliches Konstrukt wie eine Azure-Verfügbarkeitsgruppe und verfügt über eigene Fehler- und Upgradedomänen. Standardmäßig besteht eine Skalierungsgruppe aus einer einzelnen Platzierungsgruppe mit einer maximalen Größe von 100 virtuellen Computern. Wenn die Skalierungsgruppeneigenschaft _singlePlacementGroup_ auf _false_ festgelegt ist, kann die Skalierungsgruppe mehrere Platzierungsgruppen und bis zu 1.000 virtuelle Computer umfassen. Bei Verwendung des Standardwerts _true_ besteht eine Skalierungsgruppe aus einer einzelnen Platzierungsgruppe und umfasst maximal 100 virtuelle Computer.

## <a name="checklist-for-using-large-scale-sets"></a>Prüfliste für die Verwendung umfangreicher Skalierungsgruppen
Die effektive Nutzung umfangreicher Skalierungsgruppen durch eine Anwendung hängt von folgenden Aspekten ab:

- Wenn Sie eine große Anzahl von virtuellen Computern bereitstellen möchten, ist unter Umständen eine Erhöhung der Compute-vCPU-Kontingentgrenzwerte erforderlich. 
- Skalierungsgruppen, die auf der Grundlage von Azure Marketplace-Images erstellt wurden, können auf bis zu 1.000 virtuelle Computer skaliert werden.
- Skalierungsgruppen, die auf der Grundlage benutzerdefinierter Images (selbst erstellte und hochgeladene VM-Images) erstellt wurden, können derzeit auf bis zu 600 virtuelle Computer skaliert werden.
- Umfangreiche Skalierungsgruppen erfordern Azure Managed Disks. Für Skalierungsgruppen, die nicht mit Managed Disks erstellt wurden, sind mehrere Speicherkonten (jeweils eins pro 20 virtuelle Computer) erforderlich. Umfangreiche Skalierungsgruppen wurden exklusiv für die Verwendung mit Managed Disks konzipiert, um die Speicherverwaltung zu vereinfachen und um zu verhindern, dass Abonnementgrenzwerte für Speicherkonten erreicht werden. 
- Umfangreiche Skalierung (SPG=false) unterstützt keine InfiniBand-Netzwerkfunktionen.
- Für den Layer-4-Lastenausgleich mit Skalierungsgruppen bestehend aus mehreren Platzierungsgruppen ist die [Standard-SKU von Azure Load Balancer](../load-balancer/load-balancer-overview.md) erforderlich. Mit der Standard-SKU von Azure Load Balancer profitieren Sie von weiteren Vorteilen, z.B. der Möglichkeit zum Durchführen des Lastenausgleichs zwischen mehreren Skalierungsgruppen. Außerdem ist es für die Standard-SKU erforderlich, dass der Skalierungsgruppe eine Netzwerksicherheitsgruppe zugeordnet ist. Andernfalls funktionieren NAT-Pools nicht richtig. Falls Sie die Azure Load Balancer Basic-SKU verwenden müssen, sollten Sie darauf achten, dass die Skalierungsgruppe für die Verwendung einer einzelnen Platzierungsgruppe konfiguriert ist. Dies ist die Standardeinstellung.
- Layer-7-Lastenausgleich mit Azure Application Gateway wird für alle Skalierungsgruppen unterstützt.
- Eine Skalierungsgruppe ist mit einem einzelnen Subnetz definiert. Vergewissern Sie sich daher, dass Ihr Subnetz über einen ausreichend großen Adressbereich für alle benötigten virtuellen Computer verfügt. Zur Verbesserung der Zuverlässigkeit und der Leistung der Bereitstellung findet bei Skalierungsgruppen standardmäßig eine Überbereitstellung statt. (Zur Bereitstellungszeit oder beim horizontalen Hochskalieren werden also zusätzliche virtuelle Computer erstellt.) Die Größe des Adressraums sollte daher die geplante Anzahl von virtuellen Computern, auf die Sie skalieren möchten, um etwa 20 Prozent übersteigen.
- Fehler- und Upgradedomänen sind nur innerhalb einer Platzierungsgruppe konsistent. Diese Architektur ändert nichts an der allgemeinen Verfügbarkeit einer Skalierungsgruppe, da virtuelle Computer gleichmäßig auf unterschiedliche physische Hardware verteilt werden. Wenn Sie jedoch sicherstellen müssen, dass sich zwei virtuelle Computer auf unterschiedlicher Hardware befinden, müssen Sie sich vergewissern, dass sie sich in unterschiedlichen Fehlerdomänen in der gleichen Platzierungsgruppe befinden. Weitere Informationen finden Sie unter [Verfügbarkeitsoptionen für virtuelle Computer in Azure](../virtual-machines/availability.md). 
- Fehlerdomäne und Platzierungsgruppen-ID werden in der _Instanzansicht_ eines virtuellen Skalierungsgruppencomputers angezeigt. Die Instanzansicht eines virtuellen Skalierungsgruppencomputers können Sie im [Azure-Ressourcen-Explorer](https://resources.azure.com/) anzeigen.

## <a name="creating-a-large-scale-set"></a>Erstellen einer umfangreichen Skalierungsgruppe
Geben Sie beim Erstellen einer Skalierungsgruppe über das Azure-Portal einfach einen Wert von bis zu 1.000 für die *Anzahl von Instanzen* an. Bei mehr als 100 Instanzen wird *Skalierung auf über 100 Instanzen aktivieren*  auf *Ja* festgelegt, um die Skalierung auf mehrere Platzierungsgruppen zu ermöglichen. 

![Diese Abbildung zeigt das „Instanzen“-Blatt des Azure-Portals. Es sind Optionen zum Auswählen der Instanzanzahl und der Instanzgröße verfügbar.](./media/virtual-machine-scale-sets-placement-groups/portal-large-scale.png)

Über die [Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-cli) kann mit dem Befehl _az vmss create_ eine umfangreiche VM-Skalierungsgruppe erstellt werden. Dieser Befehl legt auf der Grundlage des Arguments _instance-count_ intelligente Standardwerte wie etwa die Subnetzgröße fest:

```azurecli
az group create -l southcentralus -n biginfra
az vmss create -g biginfra -n bigvmss --image ubuntults --instance-count 1000
```

Der Befehl _vmss create_ verwendet standardmäßig bestimmte Konfigurationswerte, wenn Sie diese nicht angeben. Mit dem folgenden Befehl können Sie die überschreibbaren Optionen anzeigen:

```azurecli
az vmss create --help
```

Wenn Sie eine umfangreiche Skalierungsgruppe mithilfe einer Azure Resource Manager-Vorlage erstellen, achten Sie darauf, dass die Vorlage eine auf Azure Managed Disks basierende Skalierungsgruppe erstellt. Die Eigenschaft _singlePlacementGroup_ kann im Abschnitt _properties_ der Ressource _Microsoft.Compute/virtualMachineScaleSets_ auf _false_ festgelegt werden. Das folgende JSON-Fragment zeigt den Anfang einer Skalierungsgruppenvorlage mit einer Kapazität von 1.000 virtuellen Computern und der Einstellung _"singlePlacementGroup" : false_:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "location": "australiaeast",
  "name": "bigvmss",
  "sku": {
    "name": "Standard_DS1_v2",
    "tier": "Standard",
    "capacity": 1000
  },
  "properties": {
    "singlePlacementGroup": false,
    "upgradePolicy": {
      "mode": "Automatic"
    }
```

Ein vollständiges Beispiel für eine Vorlage für umfangreiche Skalierungsgruppen finden Sie unter [https://github.com/gbowerman/azure-myriad/blob/main/bigtest/bigbottle.json](https://github.com/gbowerman/azure-myriad/blob/main/bigtest/bigbottle.json).

## <a name="converting-an-existing-scale-set-to-span-multiple-placement-groups"></a>Konvertieren einer vorhandenen Skalierungsgruppe in eine Skalierungsgruppe, die mehrere Platzierungsgruppen umfasst
Wenn eine vorhandene VM-Skalierungsgruppe auf über 100 VMs skalierbar sein soll, müssen Sie die Eigenschaft _singlePlacementGroup_ im Skalierungsgruppenmodell auf _false_ festlegen. Sie können das Ändern dieser Eigenschaft mit dem [Azure-Ressourcen-Explorer](https://resources.azure.com/) testen. Navigieren Sie zu einer vorhandenen Skalierungsgruppe, wählen Sie _Bearbeiten_ aus, und ändern Sie die Eigenschaft _singlePlacementGroup_. Sollte die Eigenschaft nicht angezeigt werden, betrachten Sie die Skalierungsgruppe möglicherweise mit einer älteren Version der Microsoft.Compute-API.

> [!NOTE]
> Eine Skalierungsgruppe, die nur eine einzelne Platzierungsgruppe unterstützt (Standardverhalten), kann in eine Skalierungsgruppe konvertiert werden, die mehrere Skalierungsgruppen unterstützt, aber nicht umgekehrt. Machen Sie sich daher vor der Konvertierung sorgfältig mit den Eigenschaften umfangreicher Skalierungsgruppen vertraut.
