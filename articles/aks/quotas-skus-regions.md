---
title: Grenzwerte für Ressourcen, SKUs und Regionen
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie mehr über Standardkontingente, eingeschränkte SKU-Größen für Knoten-VMs und regionale Verfügbarkeit von Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 257598f1a1ea8bbc578fe44c40b8e2dafef36837
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130266047"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kontingente, Größeneinschränkungen für virtuelle Computer und regionale Verfügbarkeit in Azure Kubernetes Service (AKS)

Alle Azure-Dienste legen Standardgrenzwerte und Kontingente für Ressourcen und Features fest, einschließlich Nutzungseinschränkungen für bestimmte VM-SKUs.

In diesem Artikel werden die Standardressourcenlimits für AKS-Ressourcen (Azure Kubernetes Service) und die Verfügbarkeit von AKS in Azure-Regionen ausführlich erläutert.

## <a name="service-quotas-and-limits"></a>Dienstkontingente und Limits

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Bereitgestellte Infrastruktur

Alle anderen Netzwerk-, Compute- und Speichereinschränkungen gelten für die bereitgestellte Infrastruktur. Die relevanten Grenzwerte finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Wenn Sie für einen AKS-Cluster ein Upgrade vornehmen, werden vorübergehend zusätzliche Ressourcen genutzt. Diese Ressourcen umfassen verfügbare IP-Adressen in einem Subnetz eines virtuellen Netzwerks oder ein vCPU-Kontingent für virtuelle Computer. 
>
> Für Windows Server-Container können Sie einen Upgradevorgang ausführen, um die neuesten Knotenupdates anzuwenden. Wenn Sie nicht über den verfügbaren IP-Adressraum oder das vCPU-Kontingent verfügen, um diese temporären Ressourcen zu verarbeiten, schlägt der Clusterupgradeprozess fehl. Weitere Informationen zum Upgradevorgang eines Windows Server-Knotens finden Sie unter [Durchführen eines Upgrades für einen Knotenpool in AKS][nodepool-upgrade].

## <a name="supported-vm-sizes"></a>Unterstützte VM-Größen

Die Liste der unterstützten VM-Größen in AKS wird mit der Veröffentlichung neuer VM-SKUs in Azure weiterentwickelt. Befolgen Sie die [AKS-Versionshinweise](https://github.com/Azure/AKS/releases), um über neue unterstützte SKUs auf dem Laufenden zu bleiben.

## <a name="restricted-vm-sizes"></a>Eingeschränkte VM-Größen

VM-Größen mit weniger als 2 CPUs können nicht mit AKS verwendet werden.

Jeder Knoten in einem AKS-Cluster umfasst eine feste Menge von Computeressourcen wie vCPU- und Arbeitsspeicher. Wenn ein AKS-Knoten unzureichende Computeressourcen aufweist, werden Pods möglicherweise nicht ordnungsgemäß ausgeführt. Um sicherzustellen, dass die erforderlichen *kube-system*-Pods und Ihre Anwendungen zuverlässig geplant werden können, benötigt AKS Knoten mit VM-Größen mit mehr als 2 CPUs.

Weitere Informationen zu VM-Typen und ihren Computeressourcen finden Sie unter [Größen für virtuelle Computer in Azure][vm-skus].

## <a name="region-availability"></a>Regionale Verfügbarkeit

Die aktuelle Liste der Regionen, in denen Sie Cluster bereitstellen und ausführen können, finden Sie unter [Regionale Verfügbarkeit von AKS][region-availability].

## <a name="cluster-configuration-presets-in-the-azure-portal"></a>Voreinstellungen für die Clusterkonfiguration im Azure-Portal

Wenn Sie einen Cluster über das Azure-Portal erstellen, können Sie eine voreingestellte Konfiguration auswählen, um auf Grundlage Ihres Szenarios schnell Anpassungen vorzunehmen. Sie können jeden der voreingestellten Werte jederzeit ändern.

| Voreinstellung           | BESCHREIBUNG                                                            |
|------------------|------------------------------------------------------------------------|
| Standard         | Optimal, wenn Sie nicht sicher sind, was Sie auswählen sollten. Funktioniert gut mit den meisten Anwendungen. |
| Dev/Test         | Optimal zum Experimentieren mit AKS oder zum Bereitstellen einer Testanwendung. |
| Kostenoptimiert   | Optimal zum Senken der Kosten für Produktionsworkloads, die Unterbrechungen tolerieren können. |
| Batchverarbeitung | Optimal für Machine Learning-Workloads, rechenintensive und grafikintensive Workloads. Geeignet für Anwendungen, die schnelles Hoch- und Aufskalieren des Clusters erfordern. |
| Gehärteter Zugriff  | Optimal für große Unternehmen, die vollständige Kontrolle über Sicherheit und Stabilität benötigen. |

## <a name="next-steps"></a>Nächste Schritte

Sie können bestimmte Standardgrenzwerte und Kontingente erhöhen. Wenn Ihre Ressource eine Erhöhung unterstützt, fordern Sie die Erhöhung über eine [Azure-Supportanfrage][azure-support] an (wählen Sie als **Problemtyp** den Typ **Kontingent** aus).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
