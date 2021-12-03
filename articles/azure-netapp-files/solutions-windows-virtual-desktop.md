---
title: Verwenden von Azure Virtual Desktop mit Azure NetApp Files | Microsoft-Dokumentation
description: Hier finden Sie bewährte Methoden und Beispielblaupausen für das Bereitstellen von Azure Virtual Desktop mit Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 8d4d9b393c698b2b638a119b7eb4386e0099d514
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347915"
---
# <a name="benefits-of-using-azure-netapp-files-with-azure-virtual-desktop"></a>Vorteile der Verwendung von Azure NetApp Files mit Azure Virtual Desktop 

Dieser Artikel enthält bewährte Methoden für das Bereitstellen von Azure Virtual Desktop mit Azure NetApp Files.

Azure NetApp Files ist ein äußerst leistungsfähiger Dateispeicherdienst von Azure. Er bietet bis zu 450.000 IOPS sowie Latenz unterhalb des Millisekundenbereichs und kann extrem umfangreiche Bereitstellungen von Azure Virtual Desktop unterstützen. Sie können die Bandbreite anpassen und die Dienstebene Ihrer Azure NetApp Files-Volumes bei Bedarf fast augenblicklich und ohne E/A-Unterbrechung ändern, während der Zugriff auf die Datenebene erhalten bleibt. Mit dieser Funktion können Sie den Umfang Ihrer Azure Virtual Desktop-Bereitstellung problemlos in Hinsicht auf die Kosten optimieren. Sie können auch platzsparende Point-in-Time-Momentaufnahmen von Volumes erstellen, ohne die Volumenleistung zu beeinträchtigen. Diese Funktion ermöglicht es Ihnen, ein Rollback einzelner [FSLogix-Benutzerprofilcontainer](../virtual-desktop/store-fslogix-profile.md) anhand einer Kopie aus dem Verzeichnis `~snapshot` durchzuführen oder ein sofortiges Rollback des gesamten Volumes auf einmal über die Funktion zur Volumewiederherstellung vorzunehmen.  Mit bis zu 255 (rotierenden) Momentaufnahmen zum Schutz eines Volumes vor Datenverlust oder -beschädigung haben Administratoren viele Möglichkeiten, um Vorgänge rückgängig zu machen.

## <a name="sample-blueprints"></a>Beispielblaupausen

In den folgenden Beispielblaupausen wird die Integration von Azure Virtual Desktop in Azure NetApp Files veranschaulicht. In einem Szenario mit gepoolten Desktops werden Benutzer mithilfe von [virtuellen Computern mit mehreren Sitzungen](../virtual-desktop/windows-10-multisession-faq.yml#what-is-windows-10-enterprise-multi-session) an den besten verfügbaren Sitzungshost ([Modus „Breiter Ansatz“](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-algorithm)) im Pool weitergeleitet. Andererseits sind persönliche Desktops für Szenarios reserviert, in denen jeder Benutzer über einen eigenen virtuellen Computer verfügt.

### <a name="pooled-desktop-scenario"></a>Szenario mit gepoolten Desktops

Für das gepoolte Szenario [empfiehlt](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) das Azure Virtual Desktop-Team die folgende Vorgabe nach Benutzeranzahl für vCPU. Beachten Sie, dass bei dieser Empfehlung keine Größe für virtuelle Computer angegeben ist.

|     Workloadtyp     |     Hell    |     Medium    |     Schwer    |
|-----------------------|--------------|---------------|--------------|
|     Benutzer pro vCPU    |     6        |     4         |     2        |


Diese Empfehlung wird durch einen LoginVSI-Test mit 500 Benutzern bestätigt, bei dem etwa 62 „Wissens-/Mediumbenutzer“ bei jedem virtuellen D16as_V4-Computer angemeldet werden. 

Beispielsweise kann Azure NetApp Files bei 62 Benutzern pro virtuellem D16as_V4-Computer problemlos 60.000 Benutzer pro Umgebung unterstützen. Tests zum Bewerten der Obergrenze des virtuellen D32as_v4-Computers dauern noch an. Wenn die Empfehlung für Azure Virtual Desktop-Benutzer pro vCPU auf den D32as_v4 zutrifft, sind mehr als 120.000 Benutzer auf 1.000 virtuellen Computern möglich, bevor [der VNET-Grenzwert von 1.000 IP-Adressen](./azure-netapp-files-network-topologies.md) überschritten wird, wie es in der folgenden Abbildung dargestellt ist.  

![Szenario mit gepoolten Desktops für Azure Virtual Desktop](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Szenario mit persönlichen Desktops 

In einem Szenario mit persönlichen Desktops zeigt die folgende Abbildung die allgemeine Architekturempfehlung. Benutzer sind bestimmten Desktop-Pods zugeordnet, und jeder Pod verfügt über knapp 1.000 virtuelle Computer, sodass Platz für IP-Adressen bleibt, die aus dem Verwaltungs-VNET weitergegeben werden. Azure NetApp Files kann problemlos mehr als 900 persönliche Desktops pro Hostpool-VNET mit Einzelsitzung handhaben, wobei die tatsächliche Anzahl der virtuellen Computer 1.000 minus der Anzahl der Verwaltungshosts im Hub-VNet entspricht. Wenn mehr persönliche Desktops erforderlich sind, können problemlos weitere Pods (Hostpools und virtuelle Netzwerke) hinzugefügt werden, wie es in der folgenden Abbildung dargestellt ist. 

![Szenario mit persönlichen Desktops für Azure Virtual Desktop](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

Beim Erstellen einer solchen POD-basierten Architektur ist es wichtig, die Benutzer bei der Anmeldung dem richtigen Pod zuzuweisen, um sicherzustellen, dass die Benutzer ihre Benutzerprofile immer finden. 

## <a name="next-steps"></a>Nächste Schritte

- [Lösungsarchitekturen mit Azure NetApp Files](azure-netapp-files-solution-architectures.md)