---
title: Sicherheitsempfehlungen für IoT Hub
description: Lernen Sie das Konzept der Sicherheitsempfehlungen kennen, und erfahren Sie, wie sie in Defender für IoT Hub verwendet werden.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 5a4d93c30a01cc0fa93cbb3e4835bf8a86ccc622
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331583"
---
# <a name="security-recommendations-for-iot-hub"></a>Sicherheitsempfehlungen für IoT Hub

Defender für IoT scannt Ihre Azure-Ressourcen und IoT-Geräte und gibt Sicherheitsempfehlungen zur Reduzierung der Angriffsfläche.
Sicherheitsempfehlungen sind umsetzbar und zielen darauf ab, Kunden bei der Einhaltung von Best Practices im Bereich der Sicherheit zu unterstützen.

In diesem Artikel finden Sie eine Liste von Empfehlungen, die für Ihren IoT Hub ausgelöst werden können.

## <a name="built-in-recommendations-in-iot-hub"></a>Integrierte Empfehlungen in IoT Hub

Empfehlungsbenachrichtigungen bieten Einblicke und Vorschläge für Aktionen zum Verbessern des Sicherheitszustands Ihrer Umgebung.

### <a name="high-severity"></a>Hoher Schweregrad

| severity | Name | Data source | BESCHREIBUNG | RecommendationType |
|--|--|--|--|--|
| High | Dieselben Authentifizierungsdaten werden von mehreren Geräten verwendet | IoT Hub | Von mehreren Geräten wurden die gleichen IoT Hub-Authentifizierungsinformationen verwendet. Dies könnte darauf hindeuten, dass sich ein unrechtmäßiges Gerät als rechtmäßiges Gerät ausgibt, und birgt das Risiko, dass sich ein böswilliger Akteur als Gerät ausgibt. | IoT_SharedCredentials |
| High | Hohe Berechtigungen, die im Zwilling des loT Edge-Modells für das loT Edge-Modul konfiguriert sind | IoT Hub | Das loT-Edge-Modul ist so konfiguriert, dass es im privilegierten Modus läuft, mit umfangreichen Linux-Funktionen oder mit Netzzugang auf Host-Ebene (Senden/Empfangen von Daten an den Host-Computer). | IoT_PrivilegedDockerOptions |

### <a name="medium-severity"></a>Mittlerer Schweregrad

| severity | Name | Data source | BESCHREIBUNG | RecommendationType |
|--|--|--|--|--|
| Medium | Dienstprinzipal wird mit ACR-Repository nicht verwendet | IoT Hub | Das Authentifizierungsschema, das zum Abrufen eines loT Edge-Moduls aus einem ACR-Repository verwendet wird, nutzt die Dienstprinzipal -Authentifizierung nicht. | IoT_ACRAuthentication |
| Medium | TLS-Cipher-Suite-Upgrade erforderlich | IoT Hub | Es wurden Ungesicherte TLS-Konfigurationen entdeckt. Es werden eine sofortige Aktualisierung der TLS-Cipher-Suite empfohlen. | IoT_VulnerableTLSCipherSuite |
| Medium | Die Standard-IP-Filterrichtlinie muss auf „Verweigern“ festgelegt sein | IoT Hub | Standardmäßig muss die IP-Filterkonfiguration Regeln für erlaubten Datenverkehr definieren und sollte allen anderen Datenverkehr verweigern. | IoT_IPFilter_DenyAll |
| Medium | Die IP-Filterregel umfasst einen großen IP-Adressbereich. | IoT Hub | Ein erlaubbarer IP-Adressbereich für eine IP-Filterregelquelle ist zu groß. Regeln mit übermäßiger Berechtigung können Ihren loT Hub für böswillige Akteure angreifbar machen. | IoT_IPFilter_PermissiveRule |
| Medium | Empfohlene Regeln für IP-Adressfilter | IoT Hub | Es empfiehlt sich, Ihren IP-Adressfilter in die folgenden Regeln zu ändern, die anhand des IotHub-Verhaltens ermittelt wurden. | IoT_RecommendedIpRulesByBaseLine |
| Medium | SecurityGroup hat inkonsistente Moduleinstellungen | IoT Hub | Innerhalb dieser Gerätesicherheitsgruppe weist ein anomales Gerät im Vergleich zum Rest der Sicherheitsgruppe inkonsistente loT Edge-Moduleinstellungen auf. | IoT_InconsistentModuleSettings |

### <a name="low-severity"></a>Niedriger Schweregrad

| severity | Name | Data source | BESCHREIBUNG | RecommendationType |
|--|--|--|--|--|
| Niedrig | loT Edge Hub-Speicher kann optimiert werden | IoT Hub | Optimieren Sie die Speichernutzung Ihres loT Edge Hub, indem Sie die Protokollköpfe für alle Protokolle deaktivieren, die nicht von Edge-Modulen in Ihrer Lösung verwendet werden. | IoT_EdgeHubMemOptimize |
| Niedrig | Es ist kein Protokollieren für loT Edge Modul konfiguriert | IoT Hub | Die Protokollierung für dieses loT Edge Modul ist deaktiviert. | IoT_EdgeLoggingOptions |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Sicherheitswarnungen in Bezug auf Classic Defender für IoT-Geräte](agent-based-security-alerts.md)
