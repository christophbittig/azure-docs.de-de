---
title: Informationen zu Azure ExpressRoute Direct
description: Hier erfahren Sie mehr über die wichtigsten Features von Azure ExpressRoute Direct und erhalten die Informationen, die Sie für das Onboarding für ExpressRoute Direct benötigen, z. B. verfügbare SKUs und technische Anforderungen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: duau
ms.openlocfilehash: 01de2c75cbb6c1f8b2e153e18add612608e75ac7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433979"
---
# <a name="about-expressroute-direct"></a>Informationen zu ExpressRoute Direct

ExpressRoute Direct bietet Ihnen die Möglichkeit, sich direkt mit dem globalen Netzwerk von Microsoft zu verbinden, und zwar an strategisch über die ganze Welt verteilten Peeringstandorten. ExpressRoute Direct bietet duale Konnektivität mit 100 oder 10 GBit/s, die eine Aktiv/Aktiv-Konnektivität nach Maß unterstützt. Sie können mit jedem beliebigen Dienstanbieter für ER-Direct arbeiten.

ExpressRoute Direct bietet u.a. folgende Leistungsmerkmale:

* Umfangreiche Datenerfassung in Diensten wie Azure Storage und Cosmos DB
* Physische Isolierung für Branchen, die Regulierung sowie dedizierte und isolierte Konnektivität erfordern, z. B. das Bankwesen, Behörden und der Einzelhandel.
* Präzise Steuerung der Leitungsverteilung basierend auf Unternehmensbereichen

## <a name="onboard-to-expressroute-direct"></a>Onboarding für ExpressRoute Direct

Bevor Sie ExpressRoute Direct nutzen können, müssen Sie zunächst Ihr Abonnement registrieren. Führen Sie die folgenden Befehle mithilfe von Azure PowerShell aus, um sich zu registrieren:

1.  Melden Sie sich bei Azure an, und wählen Sie das Abonnement aus, für das Sie sich registrieren möchten.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Registrieren Sie Ihr Abonnement für die Public Preview mit dem folgenden Befehl:

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Überprüfen Sie nach der Registrierung, ob der Ressourcenanbieter **Microsoft.Network** in Ihrem Abonnement registriert ist. Durch Registrieren eines Ressourcenanbieters wird Ihr Abonnement für die Verwendung mit dem Ressourcenanbieter konfiguriert.

1. Greifen Sie wie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md) beschrieben auf die Abonnementeinstellungen zu.

1. Überprüfen Sie, ob in Ihrem Abonnement unter **Ressourcenanbieter** der Anbieter **Microsoft.Network** den Status **Registriert** aufweist. Wenn der Ressourcenanbieter Microsoft.Network nicht in der Liste der registrierten Anbieter aufgeführt ist, fügen Sie ihn hinzu.

Wenn Sie ExpressRoute Direct zum ersten Mal verwenden und bemerken, dass an dem von Ihnen ausgewählten Peeringstandort keine Ports verfügbar sind, übermitteln Sie eine Supportanfrage, um mehr Bestand anzufordern.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Vergleich von ExpressRoute mit Dienstanbieter und ExpressRoute Direct

| **ExpressRoute mit Dienstanbieter** | **ExpressRoute Direct** | 
| --- | --- |
| Verwendet Dienstanbieter, um schnelles Onboarding und schnelle Konnektivität mit der vorhandenen Infrastruktur zu ermöglichen | Erfordert eine Infrastruktur mit 100GBit/s bzw. 10GBit/s und die vollständige Verwaltung aller Ebenen
| Integration mit Hunderten von Dienstanbietern, einschließlich Ethernet und MPLS | Direkte/Dedizierte Kapazität für regulierte Branchen und Erfassung sehr umfangreicher Datenmengen |
| Leitungs-SKUs zwischen 50 MBit/s und 10 GBit/s | Kunden können eine Kombination der folgenden Leitungs-SKUs auf 100-GBit/s-ExpressRoute Direct auswählen: <ul><li>5 GBit/s</li><li>10 GBit/s</li><li>40 GBit/s</li><li>100GBit/s</li></ul> Kunden können eine Kombination der folgenden Leitungs-SKUs auf 10-GBit/s-ExpressRoute Direct auswählen:<ul><li>1 GBit/s</li><li>2 GBit/s</li><li>5 GBit/s</li><li>10 GBit/s</li></ul>
| Für Einzelmandanten optimiert | Optimiert für einen einzelnen Mandanten mit mehreren Geschäftseinheiten und mehreren Arbeitsumgebungen

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct-Leitungen

Mit Microsoft Azure ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, in die Microsoft Cloud erweitern. Mit ExpressRoute können Sie Verbindungen mit Microsoft-Clouddiensten wie Microsoft Azure und Microsoft 365 herstellen.

Jeder Peeringstandort hat Zugriff auf das globale Netzwerk von Microsoft und kann standardmäßig auf jede Region in einer geopolitischen Zone auf alle globalen Regionen zugreifen. Mit einer Premiumleitung können Sie ebenfalls auf alle globalen Regionen zugreifen.  

Die Funktionalität ist in den meisten Szenarios vergleichbar mit der von Leitungen, die für den Betrieb einen ExpressRoute-Dienstanbieter in Anspruch nehmen. Um eine größere Detailliertheit und neue Funktionen von ExpressRoute Direct zu unterstützen, werden von ExpressRoute Direct-Leitungen bestimmte Schlüsselfunktionen geboten.

## <a name="circuit-skus"></a>Leitungs-SKUs

ExpressRoute Direct unterstützt Szenarien zur Erfassung umfangreicher Datenmengen in Azure Storage und anderen Big Data-Diensten. ExpressRoute-Leitungen in ExpressRoute Direct mit 100 GBit/s unterstützen nun auch die Leitungs-SKUs mit **40 GBit/s** und **100 GBit/s**. Die physischen Portpaare unterstützen nur **100 oder 10 Gbit/s** und können über mehrere virtuelle Leitungen verfügen. Leitungsgrößen:

| **100-GBit/s-ExpressRoute Direct** | **10-GBit/s-ExpressRoute Direct** | 
| --- | --- |
| **Abonnierte Bandbreite**: 200GBit/s | **Abonnierte Bandbreite**: 20GBit/s |
| <ul><li>5 GBit/s</li><li>10 GBit/s</li><li>40 GBit/s</li><li>100GBit/s</li></ul> | <ul><li>1 GBit/s</li><li>2 GBit/s</li><li>5 GBit/s</li><li>10 GBit/s</li></ul>

## <a name="technical-requirements"></a>Technische Anforderungen

* Microsoft Enterprise Edge Router (MSEE)-Schnittstellen:
    * Dual 10 Gigabit- oder 100 Gigabit-Ethernet-Ports nur über das Routerpaar
    * LR-Fiber-Konnektivität im Einzelmodus
    * IPv4 und IPv6
    * IP MTU 1500 Bytes

* Switch-/Routerebene 2/Konnektivität der Ebene 3:
    * Muss Tagkapselung mit 1 802.1Q (Dot1Q)-Tag oder zwei Tags 802.1Q (QinQ) unterstützen.
    * Ethernettyp = 0x8100
    * Muss das äußere VLAN-Tag (STAG), basierend auf der von Microsoft angegebenen VLAN-ID, hinzufügen – *gilt nur für QinQ*.
    * Muss mehrere BGP-Sitzungen (VLANs) pro Port und Gerät unterstützen.
    * IPv4- und IPv6-Konnektivität. *Für IPv6 wird keine zusätzliche untergeordnete Schnittstelle erstellt. Eine IPv6-Adresse wird der vorhandenen untergeordneten Schnittstelle hinzugefügt*. 
    * Optional: Unterstützung der [bidirektionalen Weiterleitungserkennung (Bidirectional Forwarding Detection, BFD)](./expressroute-bfd.md), die für alle privaten Peerings in ExpressRoute-Leitungen standardmäßig konfiguriert ist

## <a name="vlan-tagging"></a>VLAN-Kennzeichnung

ExpressRoute Direct unterstützt die VLAN-Kennzeichnungen QinQ und Dot1Q.

* Die **VLAN-Kennzeichnung QinQ** ermöglicht isolierte Routingdomänen auf Basis von ExpressRoute-Leitungen. Azure weist bei der Leitungserstellung dynamisch ein S-Tag zu, das nicht geändert werden kann. Jedes Peering auf der Leitung (privat und Microsoft) verwendet ein eindeutiges C-Tag als VLAN. Das C-Tag muss an den ExpressRoute Direct-Ports nicht leitungsübergreifend eindeutig sein.

* Die **VLAN-Kennzeichnung Dot1Q** ermöglicht ein einzelnes gekennzeichnetes VLAN basierend auf Paaren von ExpressRoute Direct-Ports. Das in einem Peering verwendete C-Tag muss für alle Leitungen und Peerings im Paar der ExpressRoute Direct-Ports eindeutig sein.

## <a name="workflows"></a>Workflows

### <a name="set-up-expressroute-direct"></a>Einrichten von ExpressRoute Direct

:::image type="content" source="./media/expressroute-erdirect-about/set-up-workflow.png" alt-text="Abbildung des Einrichtungsworkflows von ExpressRoute Direct." lightbox="./media/expressroute-erdirect-about/set-up-workflow-expanded.png":::

### <a name="delete-expressroute-direct"></a>Löschen von ExpressRoute Direct

:::image type="content" source="./media/expressroute-erdirect-about/delete-workflow.png" alt-text="Abbildung des Löschworkflows von ExpressRoute Direct." lightbox="./media/expressroute-erdirect-about/delete-workflow-expanded.png":::

## <a name="sla"></a>SLA

ExpressRoute Direct bietet die gleiche erstklassige SLA mit redundanten Aktiv/Aktiv-Verbindungen in das globale Microsoft-Netzwerk. Die ExpressRoute-Infrastruktur ist redundant, und auch die Konnektivität mit dem globalen Microsoft-Netzwerk ist redundant und vielfältig und kann den Kundenanforderungen entsprechend skaliert werden.

## <a name="pricing"></a>Preise

Ausführliche Informationen dazu, wie ExpressRoute Direct abgerechnet wird, finden Sie unter [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md#when-does-billing-start-and-stop-for-the-expressroute-direct-port-pairs). Ausführliche Informationen zu den Preisen finden Sie unter [ExpressRoute – Preise](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie [ExpressRoute Direct konfiguriert](expressroute-howto-erdirect.md) wird.
