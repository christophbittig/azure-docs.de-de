---
title: Sicherheitsempfehlungen für IoT Hub
description: Lernen Sie das Konzept der Sicherheitsempfehlungen kennen, und erfahren Sie, wie sie in Defender für IoT Hub verwendet werden.
ms.topic: conceptual
ms.date: 09/26/2021
ms.openlocfilehash: 6e4af6e4cadbd45e91219d2fce6d87c4fb555ece
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083332"
---
# <a name="security-recommendations-for-iot-hub"></a>Sicherheitsempfehlungen für IoT Hub

Defender für IoT scannt Ihre Azure-Ressourcen und IoT-Geräte und gibt Sicherheitsempfehlungen zur Reduzierung der Angriffsfläche.
Sicherheitsempfehlungen sind umsetzbar und zielen darauf ab, Kunden bei der Einhaltung von Best Practices im Bereich der Sicherheit zu unterstützen.

In diesem Artikel finden Sie eine Liste von Empfehlungen, die für Ihren IoT Hub ausgelöst werden können.

## <a name="built-in-recommendations-in-iot-hub"></a>Integrierte Empfehlungen in IoT Hub

Empfehlungsbenachrichtigungen bieten Einblicke und Vorschläge für Aktionen zum Verbessern des Sicherheitszustands Ihrer Umgebung.

| severity | Name | Data source | BESCHREIBUNG |
|--|--|--|--|
| High | Identische Authentifizierungsinformationen wurden von mehreren Geräten verwendet | IoT Hub | Von mehreren Geräten wurden die gleichen IoT Hub-Authentifizierungsinformationen verwendet. Dieser Prozess kann auf ein unzulässiges Gerät hinweisen, das die Identität eines legitimen Geräts vorgibt. Die Verwendung doppelter Berechtigungen erhöht das Risiko eines Geräteidentitätswechsels durch einen bösartigen Akteur. |
| Medium | Die Standard-IP-Filterrichtlinie muss auf „Verweigern“ festgelegt sein | IoT Hub | Die IP-Filterkonfiguration sollte Regeln für zulässigen Datenverkehr definieren und standardmäßig den gesamten übrigen Datenverkehr ablehnen. |
| Medium | IP-Filterregel umfasst großen IP-Adressbereich | IoT Hub | Der Quell-IP-Adressbereich einer Filterregel für zulässige IP-Adressen ist zu groß. Durch zu wenig einschränkende Regeln ist Ihr IoT Hub möglicherweise böswilligen Akteuren ausgesetzt. |
| Niedrig | Diagnoseprotokolle in IoT Hub aktivieren | IoT Hub | Aktivieren Sie Protokolle, und bewahren Sie sie bis zu ein Jahr lang auf. Durch Aufbewahrung der Protokolle können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist. |
| High | Dieselben Authentifizierungsdaten werden von mehreren Geräten verwendet | IoT Hub | Von mehreren Geräten wurden die gleichen IoT Hub-Authentifizierungsinformationen verwendet. Dies könnte darauf hindeuten, dass sich ein unrechtmäßiges Gerät als rechtmäßiges Gerät ausgibt, und birgt das Risiko, dass sich ein böswilliger Akteur als Gerät ausgibt. |
| High | Hohe Berechtigungen, die im Zwilling des loT Edge-Modells für das loT Edge-Modul konfiguriert sind | IoT Hub | Das loT-Edge-Modul ist so konfiguriert, dass es im privilegierten Modus läuft, mit umfangreichen Linux-Funktionen oder mit Netzzugang auf Host-Ebene (Senden/Empfangen von Daten an den Host-Computer). |
| Medium | Dienstprinzipal wird mit ACR-Repository nicht verwendet | IoT Hub | Das Authentifizierungsschema, das zum Abrufen eines loT Edge-Moduls aus einem ACR-Repository verwendet wird, nutzt die Dienstprinzipal -Authentifizierung nicht. |
| Medium | TLS-Cipher-Suite-Upgrade erforderlich | IoT Hub | Es wurden Ungesicherte TLS-Konfigurationen entdeckt. Es werden eine sofortige Aktualisierung der TLS-Cipher-Suite empfohlen. |
| Medium | Die Standard-IP-Filterrichtlinie muss auf „Verweigern“ festgelegt sein | IoT Hub | Standardmäßig muss die IP-Filterkonfiguration Regeln für erlaubten Datenverkehr definieren und sollte allen anderen Datenverkehr verweigern. |
| Medium | Die IP-Filterregel umfasst einen großen IP-Adressbereich. | IoT Hub | Ein erlaubbarer IP-Adressbereich für eine IP-Filterregelquelle ist zu groß. Regeln mit übermäßiger Berechtigung können Ihren loT Hub für böswillige Akteure angreifbar machen. |
| Medium | SecurityGroup hat inkonsistente Moduleinstellungen | IoT Hub | Innerhalb dieser Gerätesicherheitsgruppe weist ein anomales Gerät im Vergleich zum Rest der Sicherheitsgruppe inkonsistente loT Edge-Moduleinstellungen auf. |
| Niedrig | loT Edge Hub-Speicher kann optimiert werden | IoT Hub | Optimieren Sie die Speichernutzung Ihres loT Edge Hub, indem Sie die Protokollköpfe für alle Protokolle deaktivieren, die nicht von Edge-Modulen in Ihrer Lösung verwendet werden. |
| Niedrig | Es ist kein Protokollieren für loT Edge Modul konfiguriert | IoT Hub | Die Protokollierung für dieses loT Edge Modul ist deaktiviert. |

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht](overview.md) über den Defender für IoT-Dienst
- Erfahren Sie, wie Sie auf Ihre [Sicherheitsdaten zugreifen](how-to-security-data-access.md).
- Erfahren Sie mehr über das [Untersuchen eines Geräts](how-to-investigate-device.md).
