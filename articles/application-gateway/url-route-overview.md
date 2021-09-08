---
title: Übersicht über URL-basiertes Inhaltsrouting mit Azure Application Gateway
description: Dieser Artikel bietet eine Übersicht über das URL-basierte Inhaltsrouting mit Azure Application Gateway sowie über das UrlPathMap-Konfigurationselement und die PathBasedRouting-Regel.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7a24b9631362618ee3be5e94066ac5267ac85962
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113504871"
---
# <a name="url-path-based-routing-overview"></a>Routing auf URL-Pfadbasis – Übersicht

Mit dem Routing auf URL-Pfadbasis kann Datenverkehr basierend auf URL-Pfaden von Anforderungen an Back-End-Serverpools weitergeleitet werden. 

Ein mögliches Szenario ist die Weiterleitung von Anforderungen für unterschiedliche Inhaltstypen an verschiedene Back-End-Serverpools.

Im folgenden Beispiel verarbeitet Application Gateway Datenverkehr für „contoso.com“ aus drei Back-End-Serverpools: VideoServerPool, ImageServerPool und DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Anforderungen für http\://contoso.com/video/* werden an VideoServerPool und Anforderungen für http\://contoso.com/images/* an ImageServerPool weitergeleitet. DefaultServerPool wird ausgewählt, wenn keines der Pfadmuster zutrifft.

> [!IMPORTANT]
> Regeln werden sowohl bei der v1- als auch v2-SKU in der Reihenfolge verarbeitet, in der sie im Portal aufgeführt sind. Wenn ein einfacher Listener zuerst aufgeführt wird und sich dafür eine Übereinstimmung mit einer eingehenden Anforderung ergibt, wird die Verarbeitung von diesem Listener durchgeführt. Es wird jedoch dringend empfohlen, vor dem Konfigurieren eines einfachen Listeners zunächst Listener für mehrere Standorte zu konfigurieren. So wird sichergestellt, dass der Datenverkehr an das richtige Back-End geleitet wird.

## <a name="urlpathmap-configuration-element"></a>urlPathMap-Konfigurationselement

Mit dem urlPathMap-Element werden Pfadmuster für Zuordnungen zu Back-End-Serverpools angegeben. Das folgende Codebeispiel ist der Codeausschnitt des urlPathMap-Elements aus der Vorlagendatei.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>PathPattern

Eine Liste der Pfadmuster für den Abgleich. Jedes muss mit „/“ beginnen, und ein „*“ ist nur am Ende nach „/“ zulässig. Die Zeichenfolge, die in den Pfadabgleich eingegeben wird, enthält keinen Text nach dem ersten Fragezeichen (?) oder der ersten Raute (#), und diese Zeichen sind hier nicht zulässig. Ansonsten sind in PathPattern alle Zeichen zulässig, die auch in einer URL zulässig sind.

Die unterstützten Muster sind davon abhängig, ob Sie Application Gateway v1 oder v2 bereitgestellt haben:

#### <a name="v1"></a>v1

Bei den Pfadregeln wird die Groß-/Kleinschreibung nicht beachtet.

|v1-Pfadmuster  |Unterstützt?  |
|---------|---------|
|`/images/*`     |ja|
|`/images*`     |Ja|
|`/images/*.jpg`     |Nein|
|`/*.jpg`     |Nein|
|`/Repos/*/Comments/*`     |Nein|
|`/CurrentUser/Comments/*`     |ja|

#### <a name="v2"></a>V2

Bei den Pfadregeln wird die Groß-/Kleinschreibung nicht beachtet.

|v2-Pfadmuster  |Unterstützt?  |
|---------|---------|
|`/images/*`     |ja|
|`/images*`     |Ja|
|`/images/*.jpg`     |Nein|
|`/*.jpg`     |Nein|
|`/Repos/*/Comments/*`     |Nein|
|`/CurrentUser/Comments/*`     |ja|

Weitere Informationen erhalten Sie in einer [Resource Manager-Vorlage mit URL-basiertem Routing](https://azure.microsoft.com/resources/templates/application-gateway-url-path-based-routing) .

## <a name="pathbasedrouting-rule"></a>PathBasedRouting-Regel

RequestRoutingRule vom Typ PathBasedRouting wird verwendet, um einen Listener an ein urlPathMap-Element zu binden. Alle Anforderungen, die für diesen Listener empfangen werden, werden basierend auf einer in urlPathMap angegebenen Richtlinie weitergeleitet.
Codeausschnitt einer PathBasedRouting-Regel:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}"
    }

}
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem URL-basierten Inhaltsrouting vertraut gemacht haben, können Sie mit [Erstellen eines Anwendungsgateways mit URL-basiertem Routing](create-url-route-portal.md) fortfahren, um ein Anwendungsgateway mit URL-Routingregeln zu erstellen.
