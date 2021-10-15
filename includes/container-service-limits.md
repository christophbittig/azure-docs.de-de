---
title: Datei einfügen
description: include file
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 17a98a50f4c6e6d0723b62f1ea402c78f653bf4c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378078"
---
| Resource                                                                                                           | Begrenzung                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maximale Anzahl Cluster pro Abonnement                                                                                  | 5.000                                                                                                                                                                                                        |
| Maximale Knoten pro Cluster mit VM-Verfügbarkeitsgruppen und der Load Balancer Basic-SKU                       | 100                                                                                                                                                                                                         |
| Maximale Knoten pro Cluster mit VM-Skalierungsgruppen und der [Load Balancer Standard-SKU][standard-load-balancer] | 1\.000 (für alle [Knotenpools][node-pool])                                            |
| Maximale Knotenpools pro Cluster                                                                                     | 100                                                                                  |
| Maximale Pods pro Knoten: [Grundlegende Netzwerkunterstützung][basic-networking] mit Kubernetes                                           | Maximal: 250 <br /> Azure CLI-Standardwert: 110 <br /> Standardwert der Azure Resource Manager-Vorlage: 110 <br /> Standardwert für Bereitstellung im Azure-Portal: 30          |
| Maximale Pods pro Knoten: [Erweiterte Netzwerkunterstützung][advanced-networking] mit Azure Container Networking Interface        | Maximal: 250 <br /> Standardwert: 30                                                      |
| Vorschau des OSM-AKS-Add-Ons (Open Service Mesh)                                                                          | Kubernetes-Clusterversion: 1.19+<sup>1</sup><br />OSM-Controller pro Cluster: 1<sup>1</sup><br />Pods pro OSM-Controller: 500<sup>1</sup><br />Von OSM verwaltete Kubernetes-Dienstkonten: 50<sup>1</sup> |

<sup>1</sup> Das OSM-Add-On für AKS befindet sich in der Vorschauphase und wird noch weiter optimiert, bevor es allgemein verfügbar wird. Während der Vorschauphase wird empfohlen, die angezeigten Grenzwerte nicht zu überschreiten.<br />

<br />

| Kubernetes-Steuerungsebene | Begrenzung |  
| -------------- | :--------------------------------------------- |
| Kostenpflichtiger Tarif      | Automatisches Aufskalieren basierend auf der Last     |
| Free-Tarif      | Eingeschränkte Ressourcen mit einem [Grenzwert von ](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) 50 Mutierungs- und 100 schreibgeschützten Aufrufen von In-Flight-Anforderungen   |

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
