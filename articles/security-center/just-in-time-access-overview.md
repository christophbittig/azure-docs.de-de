---
title: Grundlegendes zu Just-In-Time-Zugriff auf virtuelle Computer in Microsoft Defender für Cloud
description: In diesem Dokument wird erläutert, wie der Just-In-Time-Zugriff auf VMs in Microsoft Defender für Cloud Sie beim Steuern des Zugriffs auf Ihre virtuellen Azure-Computer unterstützt.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 20475db8bd2ca96ee95d4a1011ad8bdb1bec47cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095890"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Grundlegendes zum Just-In-Time(JIT)-VM-Zugriff

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Auf dieser Seite werden die Prinzipien des Just-In-Time-VM-Zugriffsfeatures (JIT) von Microsoft Defender für Cloud sowie die Logik hinter der Empfehlung erläutert.

Informationen dazu, wie Sie den JIT-Zugriff über das Azure-Portal (entweder Defender für Cloud oder Azure Virtual Machines) oder programmgesteuert auf Ihre VMs anwenden, finden Sie unter [Sichern Ihrer Verwaltungsports mit Just-In-Time-Zugriff](just-in-time-access-usage.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Das Risiko offener Verwaltungsports auf einem virtuellen Computer

Bedrohungsakteure suchen aktiv nach zugänglichen Computer mit offenen Verwaltungsports wie RDP oder SSH. Alle Ihre virtuellen Computer sind potenzielle Ziele für Angriffe. Wenn eine VM erfolgreich kompromittiert wurde, wird sie als Einstiegspunkt verwendet, um weitere Ressourcen in Ihrer Umgebung anzugreifen.



## <a name="why-jit-vm-access-is-the-solution"></a>Darum ist JIT-VM-Zugriff die Lösung 

Wie bei allen Techniken zur Verbesserung der Cybersicherheit sollte das Ziel darin bestehen, die Angriffsfläche zu reduzieren. In diesem Fall bedeutet das, dass weniger offene Ports vorhanden sind, insbesondere Verwaltungsports.

Ihre legitimen Benutzer verwenden diese Ports ebenfalls, daher ist es nicht sinnvoll, sie geschlossen zu halten.

Als Lösung für dieses Problem bietet Microsoft Defender für Cloud den JIT-Zugriff. Mit JIT kann der eingehende Datenverkehr auf Ihren VMs gesperrt werden, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einen einfachen Zugriff auf Verbindungen mit VMs bereitzustellen.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Funktionsweise von JIT mit Netzwerksicherheitsgruppen und Azure Firewall

Wenn Sie den Just-in-Time-VM-Zugriff aktivieren, können Sie die Ports auf der VM auswählen, für die eingehender Datenverkehr blockiert wird. Defender für Cloud stellt sicher, dass für die von Ihnen ausgewählten Ports in der [Netzwerksicherheitsgruppe ](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) sowie in den [Regeln für Azure Firewall](../firewall/rule-processing.md) Regeln vorhanden sind, mit denen der gesamte eingehende Datenverkehr abgelehnt wird. Diese Regeln schränken den Zugriff auf die Verwaltungsports ihrer Azure-VMs ein und schützen sie vor Angriffen. 

Sind für die ausgewählten Ports bereits andere Regeln vorhanden, dann haben diese Vorrang vor den neuen Regeln zum Ablehnen von sämtlichem eingehenden Datenverkehr. Wenn es für die ausgewählten Ports keine Regeln gibt, dann haben die neuen Regeln in der NSG und Azure Firewall höchste Priorität.

Wenn ein Benutzer oder eine Benutzerin Zugriff auf eine VM anfordert, überprüft Defender für Cloud, ob diesem bzw. dieser über die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) Berechtigungen für diese VM erteilt wurden. Wenn die Anforderung genehmigt wird, konfiguriert Defender für Cloud die NSGs und Azure Firewall so, dass eingehender Datenverkehr zu den ausgewählten Ports von der entsprechenden IP-Adresse (oder dem entsprechenden Bereich) für den angegebenen Zeitraum zugelassen wird. Nach Ablauf dieser Zeitspanne stellt Defender für Cloud den jeweiligen vorherigen Status der NSGs wieder her. Bereits eingerichtete Verbindungen werden nicht unterbrochen.

> [!NOTE]
> JIT unterstützt keine VMs, die durch Azure Firewall-Instanzen geschützt werden, die von [Azure Firewall Manager](../firewall-manager/overview.md) gesteuert werden.  Die Azure Firewall muss mit Regeln (klassisch) konfiguriert sein und kann keine Firewallrichtlinien verwenden.




## <a name="how-defender-for-cloud-identifies-which-vms-should-have-jit-applied"></a>So identifiziert Defender für Cloud die VMs, auf die der JIT-Zugriff angewendet werden soll

Das folgende Diagramm zeigt die Logik, die Defender für Cloud bei der Entscheidung über die Kategorisierung Ihrer unterstützten VMs anwendet: 

[![Logikfluss von Just-In-Time (JIT) für virtuelle Computer (VM)](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Wenn Defender für Cloud einen Computer findet, der von JIT-Zugriff profitieren kann, wird dieser Computer der Registerkarte **Fehlerhafte Ressourcen** der Empfehlung hinzugefügt. 

![Empfehlung für Just-In-Time-Zugriff (JIT) auf virtuelle Computer (VM)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---just-in-time-virtual-machine-access"></a>Häufig gestellte Fragen: Just-In-Time-Zugriff auf virtuelle Computer

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Welche Berechtigungen sind erforderlich, um JIT zu konfigurieren und zu verwenden?

Damit ein JIT-Zugriff möglich ist, muss für das Abonnement [Microsoft Defender für Server](defender-for-servers-introduction.md) aktiviert sein. 

Die Rollen **Reader** und **SecurityReader** können sowohl den JIT-Status als auch die Parameter anzeigen.

Wenn Sie benutzerdefinierte Rollen erstellen möchten, die mit JIT arbeiten können, benötigen Sie die Details aus der folgenden Tabelle.

> [!TIP]
> Verwenden Sie das im Communitybereich von GitHub für Defender für Cloud verfügbare [Skript Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/main/Powershell%20scripts/JIT%20Scripts/JIT%20Custom%20Role), um eine geringstprivilegierte Rolle für Benutzer*innen zu erstellen, die JIT-Zugriff auf eine VM anfordern, aber keine weiteren JIT-Vorgänge ausführen müssen.

| Optionen, die Benutzern ermöglicht werden können: | Festzulegende Berechtigungen|
| --- | --- |
|Konfigurieren oder Bearbeiten einer JIT-Richtlinie für einen virtuellen Computer | *Weisen Sie der Rolle diese Aktionen zu:*  <ul><li>Im Bereich eines Abonnements oder einer Ressourcengruppe, das oder die dem virtuellen Computer zugeordnet ist:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Im Bereich eines Abonnements oder einer Ressourcengruppe eines virtuellen Computers: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Anfordern von JIT-Zugriff auf einen virtuellen Computer | *Weisen Sie dem Benutzer diese Aktionen zu:*  <ul><li>Im Bereich eines Abonnements oder einer Ressourcengruppe, das oder die dem virtuellen Computer zugeordnet ist:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Im Bereich eines Abonnements oder einer Ressourcengruppe, das oder die dem virtuellen Computer zugeordnet ist:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Im Bereich eines Abonnements, einer Ressourcengruppe oder eines virtuellen Computers:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Im Bereich eines Abonnements, einer Ressourcengruppe oder eines virtuellen Computers:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Lesen von JIT-Richtlinien| *Weisen Sie dem Benutzer diese Aktionen zu:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde erläutert, _warum_ Just-in-time(JIT)-VM-Zugriff verwendet werden sollte. Informationen zur _Vorgehensweise_ beim Aktivieren von JIT und Anfordern des Zugriffs auf Ihre JIT-fähigen VMs finden Sie unter folgendem Thema:

> [!div class="nextstepaction"]
> [Sichern Ihrer Verwaltungsports mit Just-in-Time-Zugriff (JIT)](just-in-time-access-usage.md)
