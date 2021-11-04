---
title: Unterstützte Ressourcentypen und Rollenzuweisungen für Chaos Studio
description: Verstehen Sie die Liste der unterstützten Ressourcentypen und welche Rollenzuweisung erforderlich ist, damit ein Experiment einen Fehler für diesen Ressourcentyp ausführen kann.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10bb9b826d5c749b283dd38caa77ae03b7695b8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095293"
---
# <a name="supported-resource-types-and-role-assignments-for-chaos-studio"></a>Unterstützte Ressourcentypen und Rollenzuweisungen für Chaos Studio

Im Folgenden sind die unterstützten Ressourcentypen für Fehler, die Zieltypen und die vorgeschlagenen Rollen aufgeführt, die zu verwenden sind, wenn einer Ressource dieses Typs eine Experimentiergenehmigung erteilt wird.

| Ressourcentyp | Zielname | Vorgeschlagene Rollenzuweisung |
| - | - | - |
| Microsoft.Compute/virtualMachines (agentenbasiert) | Microsoft-Agent | Leser |
| Microsoft.Compute/virtualMachineScaleSets (agentenbasiert) | Microsoft-Agent | Leser |
| Microsoft.Compute/virtualMachines (dienstübergreifend) | Microsoft-VirtualMachine | Mitwirkender von virtuellen Computern |
| Microsoft.Compute/virtualMachines (dienstübergreifend) | Microsoft-VirtualMachineScaleSet | Mitwirkender von virtuellen Computern |
| Microsoft.DocumentDb/databaseAccounts (CosmosDB, service-direct) | Microsoft-CosmosDB | Cosmos DB-Operator |
| Microsoft.ContainerService/managedClusters (service-direct) | Microsoft-AzureKubernetesServiceChaosMesh | Benutzerrolle für Azure Kubernetes Service-Cluster |
| Microsoft.Network/networkSecurityGroups (service-direct) | Microsoft-NetworkSecurityGroup | Mitwirkender von virtuellem Netzwerk |
| Microsoft.Cache/Redis (service-direct) | Microsoft-AzureCacheForRedis | Mitwirkender von Redis-Cache |
