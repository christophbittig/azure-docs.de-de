---
title: Sicherheitsverwaltungsregeln in Azure Virtual Network Manager (Vorschau)
description: Erfahren Sie, was Sicherheitsverwaltungsregeln in Azure Virtual Network Manager sind.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c56df99e68dc40886f01a65fe101fa142a04c43e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095503"
---
# <a name="security-admin-rules-in-azure-virtual-network-manager-preview"></a>Sicherheitsverwaltungsregeln in Azure Virtual Network Manager (Vorschau)

Azure Virtual Network Manager bietet zwei verschiedene Arten von Konfigurationen, die Sie in Ihren virtuellen Netzwerken einsetzen können, eine davon ist eine *SecurityAdmin*-Konfiguration. Eine Sicherheitsverwaltungskonfiguration enthält einen Satz von Regelsammlungen. Jede Regelsammlung enthält eine oder mehrere Sicherheitsverwaltungsregeln. Anschließend verknüpfen Sie die Regelsammlung mit den Netzwerkgruppen, auf die Sie die Sicherheitsverwaltungsregeln anwenden möchten.

> [!IMPORTANT]
> Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="security-admin-rules"></a>Sicherheitsverwaltungsregeln

Mit einer Sicherheitsverwaltungsregel können Sie Sicherheitsrichtlinienkriterien durchsetzen, die den festgelegten Bedingungen entsprechen. Sie können nur administrative Sicherheitsregeln für Ressourcen innerhalb des Bereichs der Azure Virtual Network Manager-Instanz definieren. Diese Sicherheitsregeln haben eine höhere Priorität als die Regeln der Network Security Group (NSG) und werden vor den NSG-Regeln ausgewertet. So kann ein Administrator beispielsweise alle risikoreichen Ports oder Protokolle aus dem Internet mit Security-Admin-Regeln sperren, und diese Regeln setzen zulässige NSG-Regeln außer Kraft, die auf VM- oder Subnet-Ebene erstellt wurden.

> [!IMPORTANT]
> Einige Dienste verfügen über Netzwerkrichtlinien, um sicherzustellen, dass der Netzwerkverkehr wie für ihre Dienste erforderlich funktioniert. Wenn Sie Sicherheitsverwaltungsregeln verwenden, könnten Sie die für diese Dienste erstellten Netzwerkrichtlinien verletzen. So kann zum Beispiel die Erstellung einer Deny-Admin-Regel einen Teil des Datenverkehrs blockieren, der vom Dienst *SQL Managed Instance* zugelassen ist, der durch die Netzwerkrichtlinie definiert ist. Stellen Sie sicher, dass Sie Ihre Umgebung überprüfen, bevor Sie eine Sicherheitsverwaltungskonfiguration anwenden. 

Die folgenden Felder können Sie in einer Sicherheitsverwaltungsregel definieren:

## <a name="required-fields"></a>Pflichtfelder

### <a name="priority"></a>Priorität

Die Priorität der Sicherheitsregel wird durch eine ganze Zahl zwischen 0 und 99 bestimmt. Je niedriger der Wert, desto höher ist die Priorität der Regel. Eine Verweigerungsregel mit einer Priorität von 10 hat beispielsweise Vorrang vor einer Erlaubnisregel mit einer Priorität von 20. 

### <a name="action"></a><a name = "action"></a>Aktion

Sie können eine von drei Aktionen für eine Sicherheitsregel definieren:

* **Zulassen**: Erlaubt den Verkehr über den spezifischen Port, das Protokoll und die Quell-/Ziel-IP-Präfixe in der angegebenen Richtung.
* **Verweigern** : Blockiert den Verkehr auf dem angegebenen Port, dem Protokoll und den Quell-/Ziel-IP-Präfixen in der angegebenen Richtung.
* **Immer erlauben**: Unabhängig von anderen Regeln mit niedrigerer Priorität oder benutzerdefinierten NSGs wird der Verkehr auf dem angegebenen Port, dem Protokoll und den Quell-/Ziel-IP-Präfixen in der angegebenen Richtung zugelassen.

### <a name="direction"></a>Direction

Sie können die Richtung des Datenverkehrs angeben, für den die Regel gilt. Sie können entweder eingehend oder ausgehend definieren.

### <a name="protocol"></a>Protocol

Folgende Protokolle werden derzeit mit Sicherheitsverwaltungsregeln unterstützt:

* TCP
* UDP
* ICMP
* ESP
* AH
* Beliebige Protokolle

## <a name="optional-fields"></a>Optionale Felder

### <a name="source-and-destination-types"></a>Quell- und Zieltypen

* **IP-Adressen**: Sie können IPv4- oder IPv6-Adressen oder Adressblöcke in CIDR-Notation angeben. Um mehrere IP-Adressen aufzulisten, trennen Sie die einzelnen IP-Adressen durch ein Komma.
* **Dienst-Tag**: Sie können bestimmte Service-Tags auf der Grundlage von Regionen oder eines ganzen Service definieren. Siehe [Verfügbare Service-Tags](../virtual-network/service-tags-overview.md#available-service-tags) für die Liste der unterstützten Tags.

### <a name="source-and-destination-ports"></a>Quell- und Zielhäfen

Sie können bestimmte gemeinsame Ports definieren, die von der Quelle oder dem Ziel blockiert werden sollen. Eine Liste der gängigen TCP-Ports finden Sie unten:

| Ports | Dienstname |
| ----- | ------------ |
| 20, 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3389 | RDP |

## <a name="next-steps"></a>Nächste Schritte 

Erfahren Sie, wie Sie den Netzwerkverkehr mit einer [SecurityAdmin-Konfiguration](how-to-block-network-traffic-portal.md) blockieren können.
