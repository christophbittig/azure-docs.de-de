---
title: Threat Intelligence-gestütztes Filtern für Azure Firewall
description: Das Filtern auf Basis von Threat Intelligence kann für Ihre Firewall aktiviert werden, damit diese Sie bei Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnt und diesen verweigert.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/04/2021
ms.author: victorh
ms.openlocfilehash: 5f318eabe0b1793856de040402c82951a36e00f2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278244"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Threat Intelligence-gestütztes Filtern für Azure Firewall

Das Filtern auf Basis von Threat Intelligence kann für Ihre Firewall aktiviert werden, damit diese Sie bei Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnt und diesen verweigert. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed, der mehrere Quellen umfasst, einschließlich des Microsoft Cyber Security-Teams. [Der Intelligente Sicherheitsgraph](https://www.microsoft.com/security/operations/intelligence) ist die Grundlage für die Microsoft-Bedrohungsdaten und wird von mehreren Diensten genutzt, darunter Microsoft Defender für Cloud.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Threat Intelligence für die Firewall" border="false":::

Wenn Sie das Threat Intelligence-gestützte Filtern aktiviert haben, werden die zugehörigen Regeln vor allen anderen Regeln (NAT-Regeln, Netzwerkregeln oder Anwendungsregeln) verarbeitet.

Beim Auslösen einer Regel können Sie wahlweise nur eine Warnung protokollieren oder den Modus „Warnen und Verweigern“ verwenden.

Für das Threat Intelligence-gestützte Filtern ist standardmäßig der Warnmodus aktiviert. Bis die Portalschnittstelle in Ihrer Region verfügbar wird, können Sie diese Funktion nicht deaktivieren oder den Modus ändern.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Portalschnittstelle für Threat-Intelligence-gestütztes Filtern":::

## <a name="logs"></a>Protokolle

Der folgende Protokollausschnitt zeigt eine ausgelöste Regel:

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testen

- **Testen bei ausgehendem Datenverkehr**: Warnungen bei ausgehendem Datenverkehr sollten selten auftreten, da dies bedeutet, dass Ihre Umgebung kompromittiert ist. Um zu testen, dass Warnungen bei ausgehendem Datenverkehr funktionieren, wurde ein FQDN erstellt, der eine Warnung auslöst. Verwenden Sie `testmaliciousdomain.eastus.cloudapp.azure.com` für Ihre ausgehenden Tests.

- **Testen bei eingehendem Datenverkehr**: Sie können davon ausgehen, das Warnungen bei eingehendem Datenverkehr angezeigt werden, wenn DNAT-Regeln in der Firewall konfiguriert sind. Dies gilt selbst dann, wenn nur bestimmte Quellen in der DNAT-Regel zulässig sind und der sonstige Datenverkehr verweigert wird. Azure Firewall gibt keine Warnungen für alle bekannten Portscanner aus, sondern nur für Scanner, die für ihre gelegentliche Beteiligung an schädlichen Aktivitäten bekannt sind.

## <a name="next-steps"></a>Nächste Schritte

- Siehe [Log Analytics-Beispiele für Azure Firewall](./firewall-workbook.md)
- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).
- Lesen Sie den [Microsoft Security Intelligence Report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report).
