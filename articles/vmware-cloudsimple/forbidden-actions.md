---
title: Unzulässige Aktionen bei erhöhten Zugriffsrechten
description: Wenn von der VMware-Engine eine der folgenden unzulässigen Aktionen erkannt wird, werden die Änderungen rückgängig gemacht, um Dienstunterbrechungen zu vermeiden.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 282484e4935e0a5fcc9e79351ffe2dd75b0b5f44
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344806"
---
# <a name="forbidden-actions-during-elevated-access"></a>Unzulässige Aktionen bei erhöhten Zugriffsrechten

Während des Zeitintervalls der Rechteerweiterungen sind einige Aktionen unzulässig. Wenn von der VMware-Engine eine der folgenden unzulässigen Aktionen erkannt wird, werden die Änderungen rückgängig gemacht, um Dienstunterbrechungen zu vermeiden.

## <a name="cluster-actions"></a>Clusteraktionen

- Entfernen eines Clusters über vCenter
- Ändern der VMware vSphere-Hochverfügbarkeit (High Availability, HA) für einen Cluster
- Hinzufügen eines Hosts zum Cluster über vCenter
- Entfernen eines Hosts aus dem Cluster über vCenter

## <a name="host-actions"></a>Hostaktionen

- Entfernen von Datenspeichern für einen ESXi-Host
- Deinstallieren des vCenter-Agents auf dem Host
- Ändern der Hostkonfiguration
- Ändern der Hostprofile
- Versetzen eines Hosts in den Wartungsmodus

## <a name="network-actions"></a>Netzwerkaktionen

- Löschen des Standard-DVS (Distributed Virtual Switch, verteilter virtueller Switch) in einer privaten Cloud
- Entfernen eines Hosts aus dem Standard-DVS
- Importieren einer DVS-Einstellung
- Ändern der Konfiguration einer DVS-Einstellung
- Upgraden eines DVS
- Löschen der Verwaltungsportgruppe
- Bearbeiten der Verwaltungsportgruppe

## <a name="roles-and-permissions-actions"></a>Aktionen im Zusammenhang mit Rollen und Berechtigungen

- Erstellen einer globalen Rolle
- Ändern oder Löschen der Berechtigung für Verwaltungsobjekte
- Ändern oder Entfernen von Standardrollen
- Festlegen von Rollenberechtigungen, die über die Berechtigungen der Cloudbesitzerrolle hinausgehen

## <a name="other-actions"></a>Andere Aktionen

- Entfernen einer Standardlizenz:
  - vCenter Server
  - ESXi-Knoten
  - NSX-T
  - HCX
- Ändern oder Löschen des Verwaltungsressourcenpools
- Klonen virtueller Verwaltungscomputer


## <a name="next-steps"></a>Nächste Schritte
[CloudSimple-Wartung und -Updates](cloudsimple-maintenance-updates.md) 
