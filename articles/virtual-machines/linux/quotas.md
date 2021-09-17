---
title: vCPU-Kontingente
description: Erfahren Sie mehr über vCPU-Kontingente für Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: quota
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: fa0ed99827d4f203b0df55fd83803c976c455757
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687750"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>Überprüfen von vCPU-Kontingenten mithilfe der Azure-Befehlszeilenschnittstelle

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen:heavy_check_mark: Einheitliche Skalierungsgruppen 

Die vCPU-Kontingente für virtuelle Computer und die VM-Skalierungsgruppen werden bei jedem Abonnement und in jeder Region in zwei Ebenen angeordnet. „Regionale vCPUs gesamt“ bilden die erste Ebene, die zweite Ebene besteht aus den Kernen der verschiedenen VM-Größenfamilien, z.B. vCPUs der D-Serie. Bei jeder Bereitstellung eines neuen virtuellen Computers dürfen die vCPUs für den virtuellen Computer das vCPU-Kontingent für die VM-Größenfamilie bzw. das regionale vCPU-Gesamtkontingent nicht überschreiten. Wird eines dieser Kontingente überschritten, wird die VM-Bereitstellung nicht zugelassen. Es gibt auch ein Kontingent für die Gesamtanzahl der virtuellen Computer in der Region. Die Details zu den einzelnen Kontingenten können Sie entweder im [Azure-Portal](https://portal.azure.com) auf der Seite **Abonnement** im Bereich **Nutzung + Kontingente** anzeigen oder die Werte mithilfe der Azure-Befehlszeilenschnittstelle abfragen.

> [!NOTE]
> Das Kontingent wird anhand der Gesamtanzahl der verwendeten Kerne (zugeordnet und nicht zugeordnet) berechnet. Wenn Sie zusätzliche Kerne benötigen, [fordern Sie eine Erhöhung des Kontingents an](../../azure-portal/supportability/resource-manager-core-quotas-request.md), oder löschen Sie nicht mehr benötigte VMs. 


## <a name="check-usage"></a>Überprüfen der Nutzung

Sie können die Nutzung Ihres Kontingents mithilfe von [az vm list-usage](/cli/azure/vm) überprüfen.

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Die Ausgabe sollte ungefähr wie folgt aussehen:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Reservierte VM-Instanzen
Reservierte VM-Instanzen, die einem Einzelabonnement ohne flexible VM-Größe zugeordnet sind, erweitern vCPU-Kontingente um einen neuen Aspekt. Diese Werte beschreiben die Anzahl der Instanzen der angegebenen Größe, die im Abonnement bereitstellbar sein muss. Sie fungieren im Kontingentsystem als Platzhalter, um sicherzustellen, dass ein Kontingent reserviert ist. Dadurch wird gewährleistet, dass Azure-Reservierungen im Abonnement bereitgestellt werden können. Wenn beispielsweise ein bestimmtes Abonnement über 10 Reservierungen der Größe „Standard_D1“ verfügt, ist „10“ der Nutzungsgrenzwert für Standard_D1-Reservierungen. Dadurch wird Azure veranlasst sicherzustellen, dass immer mindestens 10 vCPUs im regionalen vCPU-Gesamtkontingent sowie 10 vCPUs im vCPU-Kontingent für die Standard_D-Familie verfügbar sind, die für Standard_D1-Instanzen verwendet werden sollen.

Wenn eine Erhöhung des Kontingents erforderlich ist, z. B. um ein Einzelabonnement für eine reservierte Instanz (RI) zu erwerben, können Sie für Ihr Abonnement eine [Erhöhung des Kontingents anfordern](../../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Abrechnung und Kontingenten finden Sie unter [Grenzwerte für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json).
