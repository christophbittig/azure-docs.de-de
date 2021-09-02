---
title: Azure Percept-Sicherheit
description: Hier finden Sie weitere Informationen zur Sicherheit von Azure Percept.
author: mimcco
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: cfee4f14b5e7f135c7bbe609b5f3453b5fce8589
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221935"
---
# <a name="azure-percept-security"></a>Azure Percept-Sicherheit

Azure Percept-Geräte sind mit einem Vertrauensanker in Hardware konzipiert. Diese integrierte Sicherheit hilft, Rückschlussdaten und datenschutzrelevante Sensoren wie Kameras und Mikrofone zu schützen und ermöglicht die Authentifizierung und Autorisierung von Geräten für Azure Percept Studio-Dienste.

> [!NOTE]
> Azure Percept DK ist nur für die Verwendung in Entwicklungs- und Testumgebungen lizenziert.

## <a name="devices"></a>Geräte

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK verfügt über ein TPM (Trusted Platform Module) der Version 2.0, das eine besonders sichere Verbindung zwischen dem Gerät und Azure-Gerätebereitstellungsdiensten (DPS) ermöglicht. TPM ist ein branchenweiter ISO-Standard der Trusted Computing Group. Weitere Informationen über die vollständige TPM 2.0-Spezifikation oder die ISO/IEC 11889-Spezifikation finden Sie auf der Website der [Trusted Computing Group](https://trustedcomputinggroup.org/resource/tpm-library-specification/). Weitere Informationen darüber, wie DPS Geräte auf sichere Weise bereitstellen kann, finden Sie unter [Azure IoT Hub-Gerätebereitstellungsdienst – TPM-Nachweis](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept-SoMs (System on Modules)

Das Azure Percept Vision-SoM (System on Module) und das Azure Percept Audio-SoM enthalten beide einen Mikrocontroller (MCU), um den Zugriff auf die eingebetteten KI-Sensoren zu schützen. Bei jedem Start wird der KI-Beschleuniger durch die MCU-Firmware und unter Verwendung der DICE-Architektur (Device Identifier Composition Engine) mit Azure Percept Studio-Diensten authentifiziert und autorisiert. DICE unterteilt den Startvorgang in Ebenen und erstellt eindeutige Gerätegeheimnisse (Unique Device Secret, UDS) für jede Ebene und Konfiguration. Sollte an einem beliebigen Punkt in der Kette ein anderer Code oder eine andere Konfiguration gestartet werden, stimmen die Geheimnisse nicht überein. Weitere Informationen zu DICE finden Sie in der [Spezifikation der DICE-Arbeitsgruppe](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Informationen zum Konfigurieren des Zugriffs auf Azure Percept Studio und auf erforderliche Dienste finden Sie im Artikel zum [Konfigurieren von Firewalls für Azure Percept DK](concept-security-configuration.md).

Von Azure Percept-Geräten wird der Vertrauensanker in Hardware verwendet, um die Firmware zu schützen. Das Start-ROM gewährleistet die Integrität der Firmware zwischen ROM und Betriebssystemladeprogramm, was wiederum die Integrität der anderen Softwarekomponenten gewährleistet. Dadurch entsteht eine Vertrauenskette.

## <a name="services"></a>Dienste

### <a name="iot-edge"></a>IoT Edge

Von Azure Percept DK wird unter Verwendung des TLS-Protokolls (Transport Layer Security) eine besonders sichere Verbindung mit Azure Percept Studio und anderen Azure-Diensten hergestellt. Bei Azure Percept DK handelt es sich um ein Gerät mit Azure IoT Edge-Unterstützung. Die IoT Edge-Runtime ist eine Sammlung von Programmen, die aus einem Gerät ein IoT Edge-Gerät machen. Die Sammlung der IoT Edge-Runtime-Komponenten ermöglicht IoT Edge-Geräten den Empfang von Code für die Ausführung im Edgebereich und das Kommunizieren der Ergebnisse. Azure Percept DK nutzt Docker-Container, um IoT Edge-Workloads vom Hostbetriebssystem und von Anwendungen mit Edge-Unterstützung zu isolieren. Weitere Informationen zum Azure IoT Edge-Sicherheitsframework finden Sie in der Dokumentation zu [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Device Update für IoT Hub

Device Update für IoT Hub ermöglicht sicherere, skalierbare und zuverlässige OTA-Updates (Over the Air; per Funk) und bietet somit erneuerbare Sicherheit für Azure Percept-Geräte. Hierzu stehen umfassende Verwaltungsfunktionen sowie Informationen zur Updatekonformität zur Verfügung. Azure Percept DK beinhaltet eine vorab integrierte Geräteupdatelösung für resiliente Updates (A/B) von -Firmware- bis hin zu Betriebssystemebenen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zum Konfigurieren von Firewalls und zu Sicherheitsempfehlungen](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Erwerben von Azure Percept DK im Microsoft-Onlinestore](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
