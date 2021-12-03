---
title: Threat Intelligence-Konfiguration von Azure Firewall
description: Erfahren Sie, wie Sie die auf Threat Intelligence basierende Filterung für Ihre Azure Firewall-Richtlinie konfigurieren, um vor Datenverkehr von und zu bekannten schädlichen IP-Adressen und Domänen zu warnen und diesen zu verweigern.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 819509e08a5604957f01452e5c6ec33c20de2b2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297485"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Threat Intelligence-Konfiguration von Azure Firewall

Das Filtern auf Basis von Threat Intelligence kann für Ihre Azure Firewall-Richtlinie konfiguriert werden, damit diese Sie vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnt und diesen verweigert. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. [Der Intelligente Sicherheitsgraph](https://www.microsoft.com/security/operations/intelligence) (Intelligent Security Graph) ist die Grundlage für Microsofts Bedrohungsanalyse und wird von mehreren Diensten genutzt, darunter Microsoft Defender für Cloud.<br>

Wenn Sie Threat Intelligence-gestütztes Filtern konfiguriert haben, werden die zugehörigen Regeln vor allen anderen Regeln (NAT-Regeln, Netzwerkregeln oder Anwendungsregeln) verarbeitet.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Threat Intelligence-Richtlinie":::

## <a name="threat-intelligence-mode"></a>Threat Intelligence-Modus

Sie können Threat Intelligence in einem der drei Modi konfigurieren, die in der folgenden Tabelle beschrieben werden. Für das Threat Intelligence-gestützte Filtern ist standardmäßig der Warnmodus aktiviert.

|Mode |BESCHREIBUNG  |
|---------|---------|
|`Off`     | Das Feature Threat Intelligence ist für Ihre Firewall nicht aktiviert. |
|`Alert only`     | Sie erhalten Warnungen mit hoher Zuverlässigkeit, wenn Datenverkehr an oder von bekannten schädlichen IP-Adressen und Domänen Ihre Firewall passiert. |
|`Alert and deny`     | Datenverkehr wird blockiert, und Sie erhalten Warnungen mit hoher Zuverlässigkeit, wenn Versuche erkannt werden, Datenverkehr von oder an bekannte schädliche IP-Adressen und Domänen über Ihre Firewall zu senden. |

> [!NOTE]
> Untergeordnete Richtlinien erben den Threat Intelligence-Modus von übergeordneten Richtlinien. Untergeordnete Richtlinien müssen mit dem gleichen oder einem strengeren Modus als die übergeordnete Richtlinie konfiguriert werden.

## <a name="allowlist-addresses"></a>Adressen aus Positivlisten

Möglicherweise löst Threat Intelligence falsch positive Ergebnisse aus und blockiert Datenverkehr, der tatsächlich gültig ist. Sie können eine Liste zulässiger IP-Adressen konfigurieren, damit die von Ihnen angegebenen Adressen, Bereiche oder Subnetze von Threat Intelligence nicht gefiltert werden.  

![Adressen aus Positivlisten](media/threat-intelligence-settings/allow-list.png)

Sie können die Positivliste mit mehreren Einträgen gleichzeitig aktualisieren, indem Sie eine CSV-Datei hochladen. Die CSV-Datei darf nur IP-Adressen und IP-Adressbereiche enthalten. Die Datei darf keine Überschriften enthalten.

> [!NOTE]
> Untergeordnete Richtlinien erben die Adressen in Threat Intelligence-Positivlisten von übergeordneten Richtlinien. Alle IP-Adressen oder IP-Adressbereiche, die einer übergeordneten Richtlinie hinzugefügt werden, gelten auch für alle untergeordneten Richtlinien.

## <a name="logs"></a>Protokolle

Der folgende Protokollauszug zeigt eine ausgelöste Regel für ausgehenden Datenverkehr an eine schädliche Website:

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

- **Testen bei ausgehendem Datenverkehr**: Warnungen bei ausgehendem Datenverkehr sollten selten auftreten, da dies bedeutet, dass Ihre Umgebung kompromittiert ist. Um zu testen, dass Warnungen bei ausgehendem Datenverkehr funktionieren, wurde ein FQDN erstellt, der eine Warnung auslöst. Verwenden Sie **testmaliciousdomain.eastus.cloudapp.azure.com** für die Tests bei ausgehendem Datenverkehr.

- **Testen bei eingehendem Datenverkehr**: Sie können davon ausgehen, das Warnungen bei eingehendem Datenverkehr angezeigt werden, wenn DNAT-Regeln in der Firewall konfiguriert sind. Dies gilt selbst dann, wenn nur bestimmte Quellen in der DNAT-Regel zulässig sind und der sonstige Datenverkehr verweigert wird. Azure Firewall gibt keine Warnungen für alle bekannten Portscanner aus, sondern nur für Scanner, die für ihre gelegentliche Beteiligung an schädlichen Aktivitäten bekannt sind.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Microsoft Security Intelligence Report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report).
