---
title: Schützen des Datenverkehrs zwischen dem VNET und dem Azure Web PubSub-Dienst über private Endpunkte bei Azure
description: Übersicht über private Endpunkte für sicheren Zugriff auf den Azure Web PubSub-Dienst über virtuelle Netzwerke.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: c05112ec35197f8a17220b31e5032ae5d2469cf8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995046"
---
# <a name="use-private-endpoints-for-azure-web-pubsub-service"></a>Verwenden privater Endpunkte für den Azure Web PubSub-Dienst

Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) für Ihren Azure Web PubSub-Dienst verwenden, um Clients in einem virtuellen Netzwerk (VNET) den sicheren Zugriff auf Daten über [Private Link](../private-link/private-link-overview.md) zu ermöglichen. Der private Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihren Azure Web PubSub-Dienst. Der Netzwerkdatenverkehr zwischen den Clients im VNET und Azure Web PubSub-Dienst wird über eine private Verbindung im Microsoft-Backbonenetzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt.

Das Verwenden privater Endpunkte für Ihren Azure Web PubSub-Dienst ermöglichen Folgendes:

- Sichern des Azure Web PubSub-Dienstes mithilfe der Netzwerkzugriffssteuerung, um alle Verbindungen am öffentlichen Endpunkt für Azure Web PubSub-Dienst zu blockieren.
- Erhöhen der Sicherheit für das virtuelle Netzwerk (VNET), indem Sie die Exfiltration von Daten aus dem VNET blockieren.
- Sicheres Verbinden mit Azure Web PubSub-Dienst aus lokalen Netzwerken, die eine Verbindung mit dem VNET über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoutes](../expressroute/expressroute-locations.md) mit privatem Peering herstellen.

## <a name="conceptual-overview"></a>Konzeptionelle Übersicht

:::image type="content" source="./media/howto-secure-private-endpoints/private-endpoint-overview.png" alt-text="Übersicht über private Endpunkte für den Azure Web PubSub-Dienst.":::

Ein privater Endpunkt ist eine spezielle Netzwerkschnittstelle für einen Azure-Dienst in Ihrem [virtuellen Netzwerk](../virtual-network/virtual-networks-overview.md) (VNET). Wenn Sie einen privaten Endpunkt für Ihren Azure Web PubSub-Dienst erstellen, wird eine sichere Verbindung zwischen Clients in Ihrem VNET und Ihrem Dienst bereitgestellt. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres VNET zugewiesen. Für die Verbindung zwischen dem privaten Endpunkt und Azure Web PubSub-Dienst wird eine sichere private Verbindung verwendet.

Anwendungen im VNET können eine nahtlose Verbindung mit Azure Web PubSub-Dienst über den privaten Endpunkt herstellen, und zwar **mit denselben Verbindungszeichenfolgen und Autorisierungsmechanismen, die auch sonst verwendet würden**. Private Endpunkte können mit allen von Azure Web PubSub-Dienst unterstützten Protokollen verwendet werden, auch mit der REST-API.

Wenn Sie einen privaten Endpunkt für einen Azure Web PubSub-Dienst in Ihrem VNET erstellen, wird an den Besitzer von Azure SignalR Service eine Einwilligungsanforderung zur Genehmigung gesendet. Wenn der Benutzer, der die Erstellung des privaten Endpunkts anfordert, auch Besitzer von Azure Web PubSub-Dienst ist, wird diese Einwilligungsanforderung automatisch genehmigt.

Besitzer von Azure Web PubSub-Dienst können Einwilligungsanforderungen und die privaten Endpunkte über die Registerkarte *Private Endpunkte* für den Azure Web PubSub-Dienst im [Azure-Portal](https://portal.azure.com) verwalten.

> [!TIP]
> Wenn der Zugriff auf Ihren Azure Web PubSub-Dienst nur über den privaten Endpunkt erfolgen soll, [konfigurieren Sie die Netzwerkzugriffssteuerung](howto-secure-network-access-control.md) so, dass der Zugriff über den öffentlichen Endpunkt verweigert oder gesteuert wird.

### <a name="connecting-to-private-endpoints"></a>Herstellen einer Verbindung mit privaten Endpunkten

Clients in einem VNET, die den privaten Endpunkt verwenden, sollten dieselbe Verbindungszeichenfolge für den Azure Web PubSub-Dienst verwenden wie Clients, die eine Verbindung mit dem öffentlichen Endpunkt herstellen. Das automatische Weiterleiten der Verbindungen vom VNET zu Azure Web PubSub-Dienst über eine private Verbindung basiert auf der DNS-Auflösung.

> [!IMPORTANT]
> Verwenden Sie zum Herstellen einer Verbindung mit Azure Web PubSub-Dienst mithilfe privater Endpunkte dieselbe Verbindungszeichenfolge, die Sie auch sonst verwenden würden. Stellen Sie über die URL der `privatelink` Unterdomäne keine Verbindung mit dem Azure Web PubSub-Dienst her.

Wir erstellen standardmäßig eine [private DNS-Zone](../dns/private-dns-overview.md), die an das VNET angehängt ist, mit den erforderlichen Updates für die privaten Endpunkte. Wenn Sie jedoch einen eigenen DNS-Server verwenden, müssen Sie möglicherweise weitere Änderungen an Ihrer DNS-Konfiguration vornehmen. Im folgenden Abschnitt zu [DNS-Änderungen](#dns-changes-for-private-endpoints) werden die für private Endpunkte erforderlichen Updates beschrieben.

## <a name="dns-changes-for-private-endpoints"></a>DNS-Änderungen für private Endpunkte

Wenn Sie einen privaten Endpunkt erstellen, wird der DNS-CNAME-Ressourceneintrag für Azure Web PubSub-Dienst auf einen Alias in einer Unterdomäne mit dem Präfix `privatelink` aktualisiert. Standardmäßig erstellen wir außerdem eine [private DNS-Zone](../dns/private-dns-overview.md), die der Unterdomäne `privatelink` entspricht, mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte.

Wenn Sie den Azure Web PubSub-Dienst-Domänennamen außerhalb des VNET mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt von Azure Web PubSub-Dienst aufgelöst. Bei Auflösung aus dem VNET, das den privaten Endpunkt hostet, wird der Domänenname in die IP-Adresse des privaten Endpunkts aufgelöst.

Beim oben gezeigten Beispiel lauten die DNS-Ressourceneinträge für den Azure Web PubSub-Dienst „foobar“ bei Auflösung von außerhalb des VNET, das den privaten Endpunkt hostet, wie folgt:

| Name                                                  | type  | Wert                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.webpubsub.azure.com``                        | CNAME | ``foobar.privatelink.webpubsub.azure.com``            |
| ``foobar.privatelink.webpubsub.azure.com``            | Ein     | \<Azure Web PubSub service public IP address\>           |

Wie bereits erwähnt, können Sie den Zugriff für Clients außerhalb des VNET über den öffentlichen Endpunkt mithilfe der Netzwerkzugriffssteuerung verweigern oder steuern.

Die DNS-Ressourceneinträge für „foobar“ lauten nach dem Auflösen durch einen Client im VNET, das den privaten Endpunkt hostet, wie folgt:

| Name                                                  | type  | Wert                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.webpubsub.azure.com``                        | CNAME | ``foobar.privatelink.webpubsub.azure.com``            |
| ``foobar.privatelink.webpubsub.azure.com``            | Ein     | 10.1.1.5                                              |

Diese Vorgehensweise ermöglicht Zugriff auf Azure Web PubSub-Dienst **mithilfe derselben Verbindungszeichenfolge** für Clients in dem VNET, das die privaten Endpunkte hostet, und für Clients außerhalb des VNET.

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den FQDN für den Azure Web PubSub-Dienst-Endpunkt in die IP-Adresse des privaten Endpunkts aufzulösen. Sie sollten den DNS-Server so konfigurieren, dass die Unterdomäne der privaten Verbindung an die private DNS-Zone für das VNET delegiert wird, oder konfigurieren Sie die A-Einträge für `foobar.privatelink.webpubsub.azure.com` mit der IP-Adresse des privaten Endpunkts.

> [!TIP]
> Wenn Sie einen benutzerdefinierten oder lokalen DNS-Server verwenden, müssen Sie den DNS-Server so konfigurieren, dass der Azure Web PubSub-Dienst-Name in der Unterdomäne `privatelink` in die IP-Adresse des privaten Endpunkts aufgelöst wird. Hierzu können Sie die Unterdomäne `privatelink` an die private DNS-Zone des VNET delegieren oder die DNS-Zone auf dem DNS-Server konfigurieren und die DNS-A-Einträge hinzufügen.

Der empfohlene DNS-Zonenname für private Endpunkte für Azure Web PubSub-Dienst lautet wie folgt: `privatelink.webpubsub.azure.com`.

Weitere Informationen zum Konfigurieren des eigenen DNS-Servers für die Unterstützung privater Endpunkte finden Sie in den folgenden Artikeln:

- [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-Konfiguration für private Endpunkte](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

### <a name="create-a-private-endpoint-along-with-a-new-azure-web-pubsub-service-in-the-azure-portal"></a>Erstellen eines privaten Endpunkts zusammen mit einem neuen Azure Web PubSub-Dienst im Azure-Portal

1. Wählen Sie beim Erstellen eines neuen Azure Web PubSub-Diensts die Registerkarte **Netzwerk** aus. Wählen Sie **Privater Endpunkt** als Konnektivitätsmethode aus.

    :::image type="content" source="./media/howto-secure-private-endpoints/portal-create-blade-networking-tab.png" alt-text="Erstellen des Azure Web PubSub-Diensts – Registerkarte „Netzwerk“.":::

1. Wählen Sie **Hinzufügen**. Geben Sie das Abonnement, die Ressourcengruppe, den Speicherort und den Namen für den neuen privaten Endpunkt ein. Wählen Sie ein virtuelles Netzwerk und ein Subnetz aus.

1. Klicken Sie auf **Überprüfen + erstellen**.

### <a name="create-a-private-endpoint-for-an-existing-azure-web-pubsub-service-in-the-azure-portal"></a>Erstellen eines privaten Endpunkts für einen vorhandenen Azure Web PubSub-Dienst im Azure-Portal

1. Wechseln Sie zum Azure Web PubSub-Dienst.

1. Wählen Sie das Einstellungsmenü **Private Endpunktverbindungen**.

1. Wählen Sie oben die Schaltfläche **+ Privater Endpunkt**.

1. Geben Sie das Abonnement, die Ressourcengruppe, den Ressourcennamen und die Region für den neuen privaten Endpunkt ein.

1. Wählen Sie die Azure Web PubSub-Dienstressource aus.

1. Auswählen des virtuellen Zielnetzwerks

1. Klicken Sie auf **Überprüfen + erstellen**.

## <a name="pricing"></a>Preise

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Bekannte Probleme

Beachten Sie die folgenden bekannten Probleme im Zusammenhang mit privaten Endpunkten für Azure Web PubSub-Dienst.

### <a name="free-tier"></a>Free-Tarif

Die Azure Web PubSub-Dienstinstanz in der kostenfreien Ebene kann nicht in einen privaten Endpunkt integriert werden.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Zugriffseinschränkungen für Clients in VNETs mit privaten Endpunkten

Für Clients in VNETs mit vorhandenen privaten Endpunkten bestehen Einschränkungen beim Zugriff auf andere Azure Web PubSub-Dienst-Instanzen mit privaten Endpunkten. Angenommen, das VNET N1 verfügt über einen privaten Endpunkt für die Azure Web PubSub-Dienst-Instanz W1. Wenn Azure Web PubSub-Dienst W2 über einen privaten Endpunkt in einem VNET N2 verfügt, müssen Clients in VNET N1 auch über einen privaten Endpunkt auf Azure Web PubSub-Dienst W2 zugreifen. Verfügt Azure Web Pubsub-Dienst W2 über keine privaten Endpunkte, können Clients in VNET N1 ohne privaten Endpunkt auf Azure Web PubSub-Dienst in diesem Konto zugreifen.

Diese Einschränkung ist die Folge der DNS-Änderungen, die vorgenommen werden, wenn Azure Web PubSub-Dienst W2 einen privaten Endpunkt erstellt.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Netzwerksicherheitsgruppen-Regeln für Subnetze mit privaten Endpunkten

Derzeit können keine [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md)-Regeln und benutzerdefinierten Routen für private Endpunkte konfiguriert werden. Netzwerksicherheitsgruppen-Regeln, die auf das Subnetz angewendet werden, das den privaten Endpunkt hostet, werden auf den privaten Endpunkt angewendet. Eine eingeschränkte Umgehung dieses Problems ist das Implementieren Ihrer Zugriffsregeln für private Endpunkte in den Quellsubnetzen, obwohl dieser Ansatz möglicherweise einen höheren Verwaltungsaufwand erfordert.

