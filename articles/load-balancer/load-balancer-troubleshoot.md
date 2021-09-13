---
title: Beheben gängiger Probleme mit Azure Load Balancer
description: Erfahren Sie, wie Sie bekannte Probleme mit Azure Load Balancer beheben.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: 6ab256d58284e79323b988b2b81f4c664fa3fc16
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456666"
---
# <a name="troubleshoot-azure-load-balancer"></a>Beheben von Problemen mit Azure Load Balancer

Diese Seite enthält Informationen zur Problembehandlung für häufige Fragen zu Azure Load Balancer vom Typ „Basic“ und „Standard“. Weitere Informationen zum Load Balancer vom Typ „Standard“ finden Sie unter [Übersicht: Azure Load Balancer Standard (Preview)](load-balancer-standard-diagnostics.md).

Wenn die Load Balancer-Konnektivität nicht verfügbar ist, liegen meist die folgenden Symptome vor:

- VMs hinter dem Load Balancer antworten nicht auf Integritätstests. 
- VMs hinter dem Load Balancer antworten nicht auf Datenverkehr am konfigurierten Port.

Wenn die externen Clients für die Back-End-VMs den Load Balancer durchlaufen, wird die IP-Adresse der Clients für die Kommunikation verwendet. Stellen Sie sicher, dass die IP-Adressen der Clients der NSG-Zulassungsliste hinzugefügt werden.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Keine ausgehende Verbindung von internen Load Balancern Standard (ILB)

**Überprüfung und Lösung**

Standard-ILBs sind **standardmäßig sicher**. Basic-ILBs erlaubten eine Verbindung mit dem Internet über eine *verborgene* öffentliche IP-Adresse (Standard-IP-Adresse für den ausgehenden Zugriff). Dies wird für Produktionsworkloads nicht empfohlen, da die IP-Adresse weder statisch noch über Netzwerksicherheitsgruppen in Ihrem Besitz gesperrt ist. Wenn Sie vor Kurzem von einem Basic-ILB zu einem Standard-ILB gewechselt sind, sollten Sie eine öffentliche IP-Adresse explizit über die Konfiguration [Nur ausgehend](egress-only.md) erstellen, die die IP-Adresse über Netzwerksicherheitsgruppen sperrt. Sie können in Ihrem Subnetz auch eine [NAT Gateway](../virtual-network/nat-gateway/nat-overview.md)-Instanz verwenden. NAT Gateway ist die empfohlene Lösung für ausgehenden Datenverkehr.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Der Back-End-Port für die vorhandene LB-Regel eines Lastenausgleichs, der eine VM-Skalierungsgruppe im Back-End-Pool bereitgestellt hat, kann nicht geändert werden.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Ursache: Der Back-End-Port kann nicht für eine Lastenausgleichsregel geändert werden, die von einem Integritätstest für den Lastenausgleich verwendet wird, auf den von der VM-Skalierungsgruppe verwiesen wird.

**Lösung** Um den Port zu ändern, können Sie den Integritätstest entfernen, indem Sie die VM-Skalierungsgruppe aktualisieren, den Port aktualisieren und dann den Integritätstest erneut konfigurieren.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Geringer Datenverkehr wird noch über den Lastenausgleich geleitet, nachdem VMs aus dem Back-End-Pool des Lastenausgleichs entfernt wurden.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Ursache: Aus dem Back-End-Pool entfernte virtuelle Computer sollten keinen Datenverkehr mehr empfangen. Die geringe Menge an Netzwerkdatenverkehr könnte im Zusammenhang mit Speicher, DNS und andere Funktionen in Azure stehen.

Um dies zu überprüfen, können Sie eine Netzwerküberwachung durchführen. Die für Ihre Blob-Speicherkonten verwendeten vollqualifizierten Namen (FQDN) werden in den Eigenschaften des jeweiligen Speicherkontos aufgeführt.  Auf einer VM innerhalb Ihres Azure-Abonnements können Sie einen nslookup-Vorgang durchführen, um die Azure-IP-Adresse zu ermitteln, die diesem Speicherkonto zugewiesen ist.

## <a name="additional-network-captures"></a>Zusätzliche Netzwerkerfassungen

Wenn Sie eine Supportanfrage öffnen möchten, erfassen Sie die folgenden Informationen, um eine schnellere Lösung zu ermöglichen. Wählen Sie eine einzelne Back-End-VM für die Durchführung der folgenden Tests aus:

- Verwenden Sie PsPing von einer der Back-End-VMs im VNet, um die Testportantwort zu testen (Beispiel: „psping 10.0.0.4:3389“), und zeichnen Sie die Ergebnisse auf. 
- Wenn bei diesen Pingtests keine Antwort empfangen wird, führen Sie beim Ausführen von „PsPing“ eine gleichzeitige Netsh-Ablaufverfolgung auf der Back-End-VM und der VNET-Test-VM aus, und beenden Sie dann die Netsh-Ablaufverfolgung.

## <a name="load-balancer-in-failed-state"></a>Load Balancer in fehlerhaftem Zustand

**Lösung**

- Nachdem Sie die Ressource identifiziert haben, die sich in einem fehlerhaften Zustand befindet, wechseln Sie zum [Azure-Ressourcen-Explorer](https://resources.azure.com/), und identifizieren Sie die Ressource in diesem Zustand.
- Aktualisieren Sie die Umschaltfläche rechts oben auf „Lesen/Schreiben“.
- Klicken Sie für die Ressource im fehlerhaften Zustand auf „Bearbeiten“.
- Klicken Sie auf „PUT“, gefolgt von „GET“, um sicherzustellen, dass der Bereitstellungszustand auf „Erfolgreich“ aktualisiert wurde.
- Sie können dann mit anderen Aktionen fortfahren, da die Ressource nicht mehr fehlerhaft ist.

## <a name="next-steps"></a>Nächste Schritte

Sollte sich das Problem mit den oben genannten Schritten nicht beheben lassen, erstellen Sie ein [Supportticket](https://azure.microsoft.com/support/options/).
