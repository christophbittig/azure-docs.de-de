---
title: Übersicht über die Web Application Firewall für Azure Application Gateway-Bot-Schutz
titleSuffix: Azure Web Application Firewall
description: Dieser Artikel enthält eine Übersicht über die Web Application Firewall (WAF) für den Application Gateway-Bot-Schutz.
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7cc82630b2f65bdd94e02e71b3c2521fc5734a9e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315041"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Übersicht über die Azure Web Application Firewall für Azure Application Gateway-Bot-Schutz

Etwa 20 % des gesamten Internetdatenverkehrs stammt von böswilligen Bots. Diese Bots führen Scraping und Scans durch, um Sicherheitsrisiken in Ihrer Webanwendung zu ermitteln. Wenn diese Bots durch die WAF (Web Application Firewall) aufgehalten werden, können diese Sie nicht angreifen. Außerdem können sie nicht Ihre Ressourcen und Dienste wie z. B. Ihre Back-Ends und andere zugrunde liegende Infrastruktur nutzen.

Sie können einen verwalteten Bot-Schutzregelsatz für Ihre WAF aktivieren, um Anforderungen von IP-Adressen, die als schädlich bekannt sind, zu blockieren oder zu protokollieren. Die IP-Adressen stammen aus dem Microsoft Threat Intelligence-Feed. Der Intelligente Sicherheitsgraph stellt die Grundlage für die Bedrohungsanalyse von Microsoft dar und wird von mehreren Diensten genutzt, darunter auch Microsoft Defender für Cloud.

## <a name="use-with-owasp-rulesets"></a>Verwendung mit OWASP-Regelsätzen

Sie können den Bot-Schutzregelsatz mit beliebigen OWASP-Regelsätzen verwenden (2.2.9, 3.0 und 3.1). Es kann aber immer nur ein OWASP-Regelsatz verwendet werden. Der Bot-Schutzregelsatz enthält eine zusätzliche Regel, die in ihrem eigenen Regelsatz enthalten ist. Dieser heißt **Microsoft_BotManagerRuleSet_0.1**. Sie können ihn wie die anderen OWASP-Regeln aktivieren oder deaktivieren.

![Bot-Regelsatz](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Updates für Regelsätze

Die Liste bekannter böswilliger IP-Adressen des Bot-Schutzregelsatzes wird mehrmals täglich über den Microsoft Threat Intelligence-Feed aktualisiert, damit sie mit den Bots Schritt hält. Ihre Webanwendungen werden fortlaufend geschützt, auch wenn sich die Angriffsvektoren der Bots ändern.

## <a name="log-example"></a>Protokollbeispiel

Hier ist ein Beispiel für einen Protokolleintrag für den Bot-Schutz:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren des Bot-Schutzes für Web Application Firewall (WAF) in Azure Application Gateway](bot-protection.md)
