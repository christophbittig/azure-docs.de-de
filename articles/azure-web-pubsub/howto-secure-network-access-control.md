---
title: Schützen und Steuern des Netzwerkzugriffs auf den Azure Web PubSub-Dienstendpunkt
description: Übersicht über die Steuerung des Netzwerkzugriffs für den Azure Web PubSub-Dienst
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 56e467b392a40a7d9e5389aa31f41468db7730c9
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997626"
---
# <a name="configure-network-access-control-for-azure-web-pubsub-service"></a>Konfigurieren der Netzwerkzugriffssteuerung für den Azure Web PubSub-Dienst

Der Azure Web PubSub-Dienst ermöglicht es Ihnen, die Zugriffsebene für Ihren Dienstendpunkt basierend auf dem Anforderungstyp und der Teilmenge der verwendeten Netzwerke zu schützen und zu steuern. Wenn Netzwerkregeln konfiguriert wurden, können nur Anwendungen, die Daten über die angegebenen Netzwerke anfordern, auf den Azure Web PubSub-Dienst zugreifen.

Der Azure Web PubSub-Dienst verfügt über einen öffentlichen Endpunkt, auf den über das Internet zugegriffen werden kann. Sie können auch [private Endpunkte für Ihren Azure Web PubSub-Dienst](howto-secure-private-endpoints.md) erstellen. Der private Endpunkt weist dem Azure Web PubSub-Dienst eine private IP-Adresse aus Ihrem VNet zu und schützt den gesamten Datenverkehr zwischen Ihrem VNet und dem Azure Web PubSub-Dienst über eine private Verbindung. Die Netzwerkzugriffssteuerung des Azure Web PubSub-Diensts bietet Zugriffssteuerung für öffentliche und für private Endpunkte.

Optional können Sie auch bestimmte Anforderungstypen für den öffentlichen Endpunkt und jeden privaten Endpunkt zulassen oder verweigern. 

Eine Anwendung, die bei aktivierten Netzwerk-Zugriffssteuerungsregeln auf einen Azure Web PubSub-Dienst zugreift, benötigt weiterhin eine ordnungsgemäße Autorisierung für die Anforderung.

## <a name="scenario-a---no-public-traffic"></a>Szenario A: Kein öffentlicher Datenverkehr

Um den gesamten öffentlichen Datenverkehr vollständig zu verweigern, sollten Sie zunächst die öffentliche Netzwerkregel so konfigurieren, dass kein Anforderungstyp zulässig ist. Anschließend sollten Sie Regeln konfigurieren, die den Zugriff auf Datenverkehr aus bestimmten VNETs gewähren. Mit dieser Konfiguration können Sie eine sichere Netzwerkgrenze für Ihre Anwendungen erstellen.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Szenario B: Nur Clientverbindungen aus öffentlichem Netzwerk

In diesem Szenario können Sie die öffentliche Netzwerkregel so konfigurieren, dass nur Clientverbindungen aus dem öffentlichen Netzwerk zulässig sind. Anschließend können Sie private Netzwerkregeln konfigurieren, um andere Typen von Anforderungen zu ermöglichen, die von einem bestimmten VNET stammen. Diese Konfiguration blendet ihre App-Server aus dem öffentlichen Netzwerk aus und stellt sichere Verbindungen zwischen Ihren App-Servern und dem Azure Web PubSub-Dienst her.

## <a name="managing-network-access-control"></a>Verwalten der Netzwerkzugriffssteuerung

Sie können Netzwerkzugriffssteuerung für den Azure Web PubSub-Dienst über das Azure-Portal verwalten.

### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zu dem Azure Web PubSub-Dienst, den Sie schützen möchten.

1. Wählen Sie das Einstellungsmenü namens **Netzwerkzugriffssteuerung** aus.

    :::image type="content" source="./media/howto-secure-network-access-control/portal-network-access-control.png" alt-text="Netzwerkzugriffssteuerung im Azure-Portal":::

1. Um die Standardaktion zu bearbeiten, schalten Sie die Schaltfläche **Zulassen/Verweigern** um.

    > [!TIP]
    > Die Standardaktion ist die Aktion, die ausgeführt wird, wenn keine Übereinstimmungen mit der ACL-Regel vorhanden sind. Wenn die Standardaktion z. B. **Verweigern** ist, werden Anforderungstypen, die nicht explizit genehmigt sind, verweigert.

1. Wählen Sie zum Bearbeiten der öffentlichen Netzwerkregel zulässige Anforderungstypen unter **Öffentliches Netzwerk** aus.

    :::image type="content" source="./media/howto-secure-network-access-control/portal-public-network.png" alt-text="Bearbeiten der ACL für das öffentliche Netzwerk im Azure-Portal":::

1. Wählen Sie zum Bearbeiten der Netzwerkregeln für den privaten Endpunkt zulässige Anforderungstypen unter **Private Endpunktverbindungen** in jeder Zeile aus.

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.
