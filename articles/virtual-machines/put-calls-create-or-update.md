---
title: PUT-Aufrufe für Erstellungs- oder Aktualisierungsvorgänge
description: PUT-Aufrufe für Erstellungs- oder Aktualisierungsvorgänge bei Computeressourcen
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: 07d22c4535bc10012ea5df6cfb222f5a53eb0a81
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697285"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>PUT-Aufrufe für die Erstellung oder Aktualisierung von Computeressourcen

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen


`Microsoft.Compute`-Ressourcen unterstützen nicht die konventionelle Definition der *HTTP PUT*-Semantik. Stattdessen verwenden diese Ressourcen sowohl für PUT- als auch PATCH-Verben die PATCH-Semantik.

Bei **Erstellungsvorgängen** werden ggf. Standardwerte angewendet. Bei **Ressourcenupdates** mittels PUT oder PATCH werden jedoch keine Standardeigenschaften angewendet. Bei **Aktualisierungsvorgängen** wird eine strenge PATCH-Semantik angewendet.

Beispielsweise wird die Datenträger-`caching`-Eigenschaft eines virtuellen Computers standardmäßig auf `ReadWrite` festgelegt, wenn die Ressource ein Betriebssystemdatenträger ist.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Wenn jedoch bei **Aktualisierungsvorgängen** eine Eigenschaft ausgelassen oder ein *NULL*-Wert übergeben wird, bleibt sie unverändert, und es sind keine Standardwerte vorhanden.

Dies ist wichtig, wenn Aktualisierungsvorgänge mit der Absicht, eine Zuordnung zu entfernen, an eine Ressource gesendet werden. Wenn diese Ressource eine `Microsoft.Compute`-Ressource ist, muss die entsprechende Eigenschaft, die Sie entfernen möchten, explizit aufgerufen und ihr ein Wert zugewiesen werden. Um dies zu erreichen, können Benutzer eine leere Zeichenfolge wie **""** übergeben. Dadurch wird die Plattform angewiesen, diese Zuordnung zu entfernen.

> [!IMPORTANT]
> Das „Patchen“ eines Arrayelements wird nicht unterstützt. Stattdessen muss der Client eine PUT- oder PATCH-Anforderung mit dem gesamten Inhalt des aktualisierten Arrays ausführen. Um beispielsweise einen Datenträger von einem virtuellen Computer zu trennen, führen Sie eine GET-Anforderung aus, um das aktuelle VM-Modell zu erhalten, entfernen Sie den zu trennenden Datenträger aus `properties.storageProfile.dataDisks`, und führen Sie eine PUT-Anforderung mit der aktualisierten VM-Entität aus.

## <a name="examples"></a>Beispiele

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Korrekte Nutzlast zum Entfernen einer Näherungsplatzierungsgruppen-Zuordnung

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Falsche Nutzlasten zum Entfernen einer Näherungsplatzierungsgruppen-Zuordnung

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Erstellungs- oder Aktualisierungsaufrufen für [VMs](/rest/api/compute/virtualmachines/createorupdate) und [VM-Skalierungsgruppen](/rest/api/compute/virtualmachinescalesets/createorupdate).