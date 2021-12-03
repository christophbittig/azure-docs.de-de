---
title: Agent-basierte Empfehlungen
description: Lernen Sie das Konzept der Sicherheitsempfehlungen kennen, und erfahren Sie, wie sie in Defender für IoT-Geräte verwendet werden.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 4e0266d06f5be7937c056430aadd2d49f2f6a86d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343760"
---
# <a name="security-recommendations-for-iot-devices"></a>Sicherheitsempfehlungen für IoT-Geräte

Defender für IoT scannt Ihre Azure-Ressourcen und IoT-Geräte und gibt Sicherheitsempfehlungen zur Reduzierung der Angriffsfläche.
Sicherheitsempfehlungen sind umsetzbar und zielen darauf ab, Kunden bei der Einhaltung von Best Practices im Bereich der Sicherheit zu unterstützen.

In diesem Artikel finden Sie eine Liste von Empfehlungen, die für Ihre IoT-Geräte ausgelöst werden können.

## <a name="agent-based-recommendations"></a>Agent-basierte Empfehlungen

Geräteempfehlungen liefern Einblicke und Vorschläge zur Verbesserung der Gerätesicherheit und des Geräteverhaltens.

| severity | Name | Data source | BESCHREIBUNG |
|--|--|--|--|
| Medium | Offene Ports am Gerät | Klassischer Defender-IoT-Micro-Agent| Auf dem Gerät wurde ein lauschender Endpunkt gefunden. |
| Medium | In einer der Ketten wurde eine zu wenig einschränkende Firewallrichtlinie gefunden. | Klassischer Defender-IoT-Micro-Agent| Zulässige Firewallrichtlinie gefunden(INPUT/OUTPUT). Die Firewallrichtlinie sollte standardmäßig den gesamten Datenverkehr verweigern und Regeln definieren, die die notwendige Kommunikation mit dem/vom Gerät ermöglichen. |
| Medium | In der INPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden | Klassischer Defender-IoT-Micro-Agent| In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält. |
| Medium | In der OUTPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden | Klassischer Defender-IoT-Micro-Agent| In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält. |
| Medium | Fehler bei der Überprüfung der Betriebssystembaseline | Klassischer Defender-IoT-Micro-Agent| Gerät entspricht nicht den [CIS-Linux-Benchmarks](https://www.cisecurity.org/cis-benchmarks/) |

### <a name="agent-based-operational-recommendations"></a>Agent-basierte Betriebsempfehlungen

Operative Empfehlungen liefern Einblicke und Vorschläge zur Verbesserung der Konfiguration des Sicherheits-Agent.

| severity | Name | Data source | BESCHREIBUNG |
|--|--|--|--|
| Niedrig | Der Agent sendet ungenutzte Meldungen | Klassischer Defender-IoT-Micro-Agent | 10 % oder mehr der Sicherheitsmeldungen in den letzten 24 Stunden waren kleiner als 4 KB. |
| Niedrig | Sicherheitszwillingskonfiguration nicht optimal | Klassischer Defender-IoT-Micro-Agent | Die Konfiguration des Sicherheitszwillings ist nicht optimal. |
| Niedrig | Konflikt bei Sicherheitszwillingskonfiguration | Klassischer Defender-IoT-Micro-Agent | In der Konfiguration des Sicherheitszwillings wurden Konflikte identifiziert. |

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht](overview.md) über den Defender für IoT-Dienst
- Erfahren Sie, wie Sie auf Ihre [Sicherheitsdaten zugreifen](how-to-security-data-access.md).
- Erfahren Sie mehr über das [Untersuchen eines Geräts](how-to-investigate-device.md).
