---
title: Verwenden von Diensttags
titleSuffix: Azure Web PubSub service
description: Verwenden von Diensttags für das Zulassen von ausgehendem Datenverkehr zu Ihrer Azure Web PubSub Service-Instanz
author: ArchangelSDY
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 10/21/2021
ms.author: dayshen
ms.openlocfilehash: 2422fb24fd3608d035fe374bd08dcae49321f4f2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270682"
---
# <a name="use-service-tags-for-azure-web-pubsub-service"></a>Verwenden von Diensttags für Azure Web PubSub Service

Sie können [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) für Azure Web PubSub Service verwenden, wenn Sie [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#network-security-groups) konfigurieren. So können Sie Sicherheitsregeln für ein-/ausgehenden Datenverkehr für Azure Web PubSub Service-Endpunkte definieren, ohne dass dafür IP-Adressen hartcodiert werden müssen.

Azure Web PubSub Service verwaltet diese Diensttags. Sie können kein eigenes Diensttag erstellen oder ein vorhandenes Tag ändern. Microsoft verwaltet diese Adresspräfixe, die dem Diensttag entsprechen, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

> [!Note]
> Ab dem 15. August 2021 unterstützt Azure Web PubSub Service bidirektionale Diensttags für ein- und ausgehenden Datenverkehr.

## <a name="use-service-tag-via-azure-cli"></a>Verwenden des Diensttags über die Azure CLI

### <a name="configure-outbound-traffic"></a>Konfigurieren von ausgehendem Datenverkehr

Sie können ausgehenden Datenverkehr zu Web PubSub Service zulassen, indem Sie eine neue Sicherheitsregel für ausgehenden Datenverkehr im Netzwerk festlegen:

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Outbound --destination-address-prefixes AzureWebPubSub
```

### <a name="configure-inbound-traffic"></a>Konfigurieren von eingehendem Datenverkehr

Wenn Sie [Ereignishandler](concept-service-internals.md#event_handler) verwenden, können Sie auch eingehenden Datenverkehr von Azure Web PubSub Service zulassen, indem Sie eine neue Netzwerksicherheitsregel für eingehenden Datenverkehr hinzufügen:

```azurecli-interactive
az network nsg rule create -n <rule-name> --nsg-name <nsg-name> -g <resource-group> --priority 100 --direction Inbound --source-address-prefixes AzureWebPubSub
```

## <a name="next-steps"></a>Nächste Schritte

- [Netzwerksicherheitsgruppen: Diensttags](../virtual-network/network-security-groups-overview.md#security-rules)