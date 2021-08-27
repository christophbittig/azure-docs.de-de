---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: aabb613b1de043568f8fb3d5161517b511375b26
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605149"
---
|Name |BESCHREIBUNG |Richtlinien |Version |
|---|---|---|---|
|[\[Vorschau\]: Konfigurieren von Azure Defender für SQL-Agents auf virtuellen Computern](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_AzureDefenderForSql.json) |Konfigurieren Sie virtuelle Computer für die automatische Installation von Azure Defender für SQL-Agent dort, wo der Azure Monitor-Agent installiert ist. Security Center sammelt Ereignisse von den Agents und verwendet diese, um Sicherheitswarnungen und maßgeschneiderte Härtungsempfehlungen bereitzustellen. Erstellt eine Ressourcengruppe und einen Log Analytics-Arbeitsbereich in der Region, in der sich der Computer befindet. Diese Richtlinie gilt nur für virtuelle Computer in einigen Regionen. |2 |1.0.0-preview |
|[Azure Monitor-Agent für virtuelle Linux-Computer konfigurieren und der Datensammlungsregel zuordnen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AMCS_LinuxPlatform_EnableDCR.json) |Stellen Sie den Azure Monitor-Agent für virtuelle Linux-Computer bereit, wenn das Image (Betriebssystem) des virtuellen Computers und der Standort in der Liste definiert sind und der Agent nicht installiert ist.  Stellen Sie dann die Zuordnung bereit, um den virtuellen Computer mit der angegebenen Datensammlungsregel zu verknüpfen. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, sobald Unterstützung hinzugefügt wird. |2 |1.0.0 |
|[Azure Monitor-Agent für virtuelle Windows-Computer konfigurieren und der Datensammlungsregel zuordnen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AMCS_WindowsPlatform_EnableDCR.json) |Stellen Sie den Azure Monitor-Agent für Windows-VMs bereit, wenn das VM-Image (Betriebssystem) und der Standort in der Liste definiert sind und der Agent nicht installiert ist.  Stellen Sie dann die Zuordnung bereit, um den virtuellen Computer mit der angegebenen Datensammlungsregel zu verknüpfen. Die Liste der Betriebssystemimages wird mit der Zeit aktualisiert, sobald Unterstützung hinzugefügt wird. |2 |1.0.0 |
|[\[Preview\]: Konfigurieren Sie Computer so, dass der Azure Monitor- und der Azure Security-Agent automatisch auf VMs installiert werden.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Konfigurieren Sie Computer so, dass der Azure Monitor- und der Azure Security-Agent automatisch installiert werden. Security Center sammelt Ereignisse von den Agents und verwendet diese, um Sicherheitswarnungen und maßgeschneiderte Härtungsempfehlungen bereitzustellen. Erstellen Sie eine Ressourcengruppe und einen Log Analytics-Arbeitsbereich in derselben Region, in der sich auch der Computer zum Speichern der Überwachungsdatensätze befindet. Diese Richtlinie gilt nur für virtuelle Computer in einigen Regionen. |6 |2.0.0-preview |
|[Aktivieren von Azure Monitor für VM-Skalierungsgruppen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Hiermit aktivieren Sie Azure Monitor für die VM-Skalierungsgruppen im angegebenen Bereich (Verwaltungsgruppe, Abonnement oder Ressourcengruppe). Akzeptiert den Log Analytics-Arbeitsbereich als Parameter. Hinweis: Wenn „upgradePolicy“ für Ihre Skalierungsgruppe auf „Manuell“ festgelegt ist, müssen Sie die Erweiterung auf alle VMs in der Gruppe anwenden, indem Sie dafür ein Upgrade aufrufen. Führen Sie dazu in der CLI „az vmss update-instances“ aus. |6 |1.0.1 |
|[Azure Monitor für VMs aktivieren](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Hiermit aktivieren Sie Azure Monitor für die virtuellen Computer (VMs) in dem angegebenen Bereich (Verwaltungsgruppe, Abonnement oder Ressourcengruppe). Akzeptiert den Log Analytics-Arbeitsbereich als Parameter. |10 |2.0.0 |
