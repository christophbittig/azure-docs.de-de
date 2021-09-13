---
title: Informationen zu Clientadresspools für P2S-Benutzer-VPN
titleSuffix: Azure Virtual WAN
description: Erfahren Sie mehr über Clientadresspools für P2S-Benutzer-VPN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: 58b642eeff7b20ccb04e2b5817f8f5a8ee5bcc2c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340029"
---
# <a name="about-client-address-pools-for-point-to-site-configurations"></a>Informationen zu Clientadresspools für Point-to-Site-Konfigurationen

In diesem Artikel werden Virtual WAN Richtlinien und Anforderungen für die Zuweisung von Clientadressräumen beschrieben, wenn die Point-to-Site-**Gatewayskalierungseinheiten** des virtuellen Hubs 40 oder höher sind.

Point-to-Site-VPN-Gateways im Virtual WAN-Hub werden mit mehreren Instanzen bereitgestellt. Jede Instanz eines Point-to-Site-VPN-Gateways kann bis zu 10.000 gleichzeitige Point-to-Site-Benutzerverbindungen unterstützen. Für Skalierungseinheiten über 40 muss daher von Virtual WAN zusätzliche Kapazität bereitgestellt werden. Hierzu muss für verschiedene Skalierungseinheiten eine Mindestanzahl von Adresspools zugewiesen werden.

Wenn für die Skalierungseinheit also beispielsweise der Wert „100“ ausgewählt wird, werden für das Point-to-Site-VPN-Gateway im virtuellen Hub fünf Instanzen bereitgestellt. Von dieser Bereitstellung können 50.000 gleichzeitige Verbindungen und **mindestens** fünf verschiedene Adresspools unterstützt werden.

**Verfügbare Skalierungseinheiten**

| Skalierungseinheit | Maximal unterstützte Clientanzahl | Mindestanzahl von Adresspools |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30.000 | 3 |
| 80 | 40.000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80.000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100.000 | 10 |

## <a name="specifying-address-pools"></a><a name="specify-address-pools"></a>Angeben von Adresspools

Zuweisungen von Point-to-Site-VPN-Adresspools werden automatisch von Virtual WAN vorgenommen. Informationen zum Angeben von Adresspools finden Sie in den folgenden grundlegenden Richtlinien.

* Pro Gatewayinstanz sind maximal 10.000 gleichzeitige Verbindungen möglich. Daher muss jeder Adresspool mindestens 10.000 eindeutige RFC1918-IP-Adressen enthalten.
* Mehrere Adresspoolbereiche werden automatisch kombiniert und einer **einzelnen** Gatewayinstanz zugewiesen. Für Gatewayinstanzen mit weniger als 10.000 IP-Adressen wird das Roundrobin-Verfahren verwendet. So kann beispielsweise ein Pool mit 5.000 Adressen von Virtual WAN automatisch mit einem anderen Pool kombiniert werden, der über 8.000 Adressen verfügt und einer einzelnen Gatewayinstanz zugewiesen ist.
* Ein einzelner Adresspool wird nur von Virtual WAN einer einzelnen Gatewayinstanz zugewiesen.
* Adresspools müssen unterschiedlich sein. Zwischen Adresspools darf es keine Überschneidungen geben.

> [!NOTE] 
> Ist ein Adresspool einer Gatewayinstanz zugeordnet, die gerade gewartet wird, kann der Adresspool nicht einer anderen Instanz zugewiesen werden.

### <a name="example"></a>Beispiel 

Im folgenden Beispiel werden durch 60 Skalierungseinheiten zwar bis zu 30.000 Verbindungen unterstützt, der zugeordnete Adresspool führt jedoch zu weniger als 30.000 gleichzeitigen Verbindungen.

In diesem Setup werden insgesamt 28.192 gleichzeitige Verbindungen unterstützt. Die erste Gatewayinstanz unterstützt 10.000 Adressen, die zweite Instanz unterstützt 8.192 Verbindungen, und die dritte Instanz unterstützt wieder 10.000 Adressen.

| Adresspoolnummer | Adresspool | Unterstützte Verbindungen |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

#### <a name="recommendations"></a>Empfehlungen

* Stellen Sie sicher, dass der zweite Adresspool mindestens 10.000 unterschiedliche IP-Adressen enthält. (Beispiel: 10.13.0.0/15)
* Fügen Sie einen weiteren Adresspool hinzu. (Beispiel: Adresspool 4 – 10.15.0.0/21 mit 2.048 Adressen). Die Adresspools 2 und 4 werden automatisch kombiniert und ermöglichen es der Gatewayinstanz, 10.000 gleichzeitige Verbindungen zu unterstützen.

## <a name="configure-or-modify-this-setting"></a>Konfigurieren oder Ändern dieser Einstellung

Diese Einstellung wird auf der Seite **Point-to-Site** konfiguriert, wenn Sie Ihren virtuellen Hub erstellen. Sie können sie später ändern.

So ändern Sie diese Einstellung:

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Konfigurationsschritte finden Sie unter [Konfigurieren von P2S-Benutzer-VPN](virtual-wan-point-to-site-portal.md).
