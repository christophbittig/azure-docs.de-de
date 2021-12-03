---
title: Häufig gestellte Fragen zum Netzwerkfunktionsmanager
titleSuffix: Azure Network Function Manager
description: Hier finden Sie häufig gestellte Fragen zum Netzwerkfunktionsmanager.
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 5cf6b9bdc9982adf2da86956ce41e7cd5e7458ec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004934"
---
# <a name="azure-network-function-manager-faq"></a>Häufig gestellte Fragen zu Azure Network Function Manager

## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="i-am-a-network-function-partner-and-want-to-onboard-to-network-function-manager-how-do-i-offer-my-network-function-with-nfm"></a>Ich bin Netzwerkfunktionspartner und möchte das Onboarding für den Netzwerkfunktionsmanager durchführen. Wie kann ich meine Netzwerkfunktion mit dem NFM anbieten?

Wir möchten Kunden ein umfangreiches Angebot an Netzwerkfunktionen ihrer Wahl bieten, die in Azure Stack Edge verfügbar sind. Senden Sie uns eine E-Mail ( **aznfmpartner@microsoft.com** ), um die Onboardinganforderungen zu besprechen.

### <a name="does-network-function-manager-support-other-azure-edge-devices-in-addition-to-azure-stack-edge-pro-with-gpu"></a>Unterstützt Azure Network Function Manager neben Azure Stack Edge Pro mit GPU auch noch andere Azure Edge-Geräte?

NFM ist derzeit für allgemein verfügbare Azure Stack Edge Pro-Geräte mit GPU verfügbar. Bei ASE Pro handelt es sich um eine Hardware-as-a-Service-Lösung, die für die Ausführung spezialisierter Netzwerkfunktionen wie Mobile Packet Core und SD-WAN-Edge entwickelt wurde. Das Gerät verfügt über sechs physische Ports mit Netzwerkbeschleunigungsunterstützung an den Ports 5 und 6. Die Spezifikationen der Netzwerkschnittstelle für Azure Stack Edge Pro-Geräte mit GPU finden Sie [hier](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#network-interface-specifications). Netzwerkfunktionspartner können SR-IOV- und DPDK-Funktionen nutzen, um eine herausragende Netzwerkleistung für ihre Netzwerkfunktionen zu bieten.

### <a name="what-additional-capabilities-are-available-on-azure-stack-edge-pro-with-gpu-in-addition-to-running-network-functions"></a>Welche zusätzlichen Funktionen sind für Azure Stack Edge Pro mit GPU neben der Ausführung von Netzwerkfunktionen noch verfügbar?

Azure Stack Edge (ASE) Pro mit GPU sowie der Azure-Netzwerkfunktionsmanager sind Teil der Lösung [privates MEC in Azure](../private-multi-access-edge-compute-mec/index.yml). Sie können jetzt ein privates mobiles Netzwerk und einen virtuellen Computer oder eine containerbasierte Edgeanwendung auf Ihrem ASE-Gerät ausführen. Dies ermöglicht die Erstellung innovativer Lösungen mit berechenbaren SLAs für Ihre kritischen Geschäftsanwendungen. Azure Stack Edge Pro ist auch mit einer oder zwei [GPUs](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#compute-acceleration-specifications) ausgestattet, was Szenarien wie Videorückschlüsse und maschinelles Lernen am Edge ermöglicht.

### <a name="what-is-the-pricing-for-network-function-manager"></a>Welche Kosten fallen für Azure Network Function Manager an?

Azure Network Function Manager wird ohne zusätzliche Kosten auf Ihrem Azure Stack Edge Pro-Gerät angeboten. Von Netzwerkfunktionspartnern, die ihre Netzwerkfunktionen mit dem NFM-Dienst anbieten, wird unter Umständen eine separate Gebühr erhoben. Preisdetails erhalten Sie von Ihrem Netzwerkfunktionspartner.

### <a name="if-my-azure-stack-edge-pro-device-is-in-a-disconnected-mode-or-partially-connected-mode-will-the-network-functions-already-deployed-stop-working"></a>Funktionieren die bereits bereitgestellten Netzwerkfunktionen nicht mehr, wenn sich mein Azure Stack Edge Pro-Gerät in einem getrennten oder teilweise verbundenen Modus befindet?

Für Verwaltungsvorgänge zum Erstellen oder Löschen von Netzwerkfunktionen sowie zum Überwachen der auf Ihrem Gerät ausgeführten Netzwerkfunktionen und zum Behandeln von Problemen benötigt der Netzwerkfunktionsmanager-Dienst Netzwerkkonnektivität mit dem ASE-Gerät. Wenn die Netzwerkfunktion auf dem ASE-Gerät bereitgestellt wurde und die Verbindung mit dem Gerät getrennt wird oder das Gerät nur teilweise mit der zugrunde liegenden Netzwerkfunktion verbunden ist, sollten virtuelle Computer weiterhin ohne Unterbrechung funktionieren. Für diese virtuellen Computern bereitgestellte Netzwerkfunktionen können je nach Konfigurationsverwaltung unterschiedliche Anforderungen haben, und es können zusätzliche Netzwerkverbindungsanforderungen von Netzwerkfunktionspartnern gelten. Informieren Sie sich bei Ihrem Partner über die Netzwerkkonnektivitätsanforderungen und Betriebsmodi.

### <a name="which-regions-are-supported-for-nfm-will-you-add-support-for-additional-azure-regions"></a>Welche Regionen werden für NFM unterstützt? Kommen später weitere unterstützte Azure-Regionen hinzu?

Azure Network Function Manager ist in folgenden Regionen verfügbar:

[!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

Azure Stack Edge Pro mit GPU ist in mehreren Ländern verfügbar, um Netzwerkfunktionen Ihrer Wahl bereitzustellen und auszuführen. Eine Liste aller Länder/Regionen, in denen das Azure Stack Edge Pro-Gerät mit GPU verfügbar ist, finden Sie auf der Seite [Azure Stack Edge – Preise](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). Sehen Sie sich auf der Registerkarte **Azure Stack Edge Pro** den Abschnitt  **Verfügbarkeit** an.

Sie können die Ressourcen für das Azure Stack Edge-Gerät und Azure Network Function Manager basierend auf Ihren gesetzlichen Anforderungen und Datenhoheitsanforderungen registrieren. Die zugeordnete Azure-Region von Netzwerkfunktionsmanager-Ressourcen wird verwendet, um die Verwaltungsvorgänge vom Clouddienst zum physischen Gerät zu leiten.

### <a name="when-i-delete-the-managed-application-for-my-network-function-running-on-azure-stack-edge-will-the-billing-for-network-functions-automatically-stop"></a>Endet die Abrechnung für Netzwerkfunktionen automatisch, wenn ich die verwaltete Anwendung für meine in Azure Stack Edge ausgeführte Netzwerkfunktion lösche?

Informieren Sie sich bei Ihrem Netzwerkfunktionspartner über den Abrechnungszyklus für Netzwerkfunktionen, die über den Netzwerkfunktionsmanager bereitgestellt werden. Jeder Partner hat eine andere Abrechnungsrichtlinie für seine Netzwerkfunktionsangebote.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in der [Übersicht](overview.md).
