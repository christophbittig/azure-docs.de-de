---
title: Baselineüberprüfungen und benutzerdefinierte Überprüfungen
description: Erfahren Sie mehr über das Konzept von Microsoft Defender für die IoT-Baseline.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 80cb204008c60964ed90c63885ce8b99d23ca30b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284130"
---
# <a name="microsoft-defender-for-iot-baseline-and-custom-checks"></a>Microsoft Defender für IoT–Baseline und benutzerdefinierte Überprüfungen

Dieser Artikel erläutert die Baseline von Defender für IoT und fasst alle zugehörigen Eigenschaften von benutzerdefinierten Überprüfungen der Baseline zusammen.

## <a name="baseline"></a>Grundwert

Eine Baseline richtet das Standardverhalten für jedes Gerät ein und erleichtert die Unterscheidung von ungewöhnlichem oder abweichendem Verhalten von der erwarteten Norm.

## <a name="baseline-custom-checks"></a>Benutzerdefinierte Überprüfungen der Baseline

Benutzerdefinierte Überprüfungen der Baseline richten eine benutzerdefinierte Liste von Überprüfungen für die Baseline jedes Geräts ein und verwenden dazu den **Zwilling der Modulkennung** des Geräts.

## <a name="setting-baseline-properties"></a>Festlegen von Baseline-Eigenschaften

1. Suchen und wählen Sie in Ihrem IoT Hub das Gerät aus, das Sie ändern möchten.

1. Klicken Sie auf das Gerät, und klicken Sie dann auf das Modul **azureiotsecurity**.

1. Klicken Sie auf **Zwilling der Modulkennung**.

1. Laden Sie die Datei mit den **benutzerdefinierten Baseline-Überprüfungen** auf das Gerät hoch.

1. Fügen Sie dem Defender-IoT-micro-agent Baseline-Eigenschaften hinzu, und klicken Sie auf **Speichern**.

### <a name="baseline-custom-check-file-example"></a>Beispiel für eine Datei mit benutzerdefinierten Baseline-Überprüfungen

So konfigurieren Sie benutzerdefinierte Baseline-Überprüfungen:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Eigenschaften von benutzerdefinierten Baseline-Überprüfungen

| Name| Status | Gültige Werte| Standardwerte| BESCHREIBUNG |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Erforderlich: WAHR |Gültige Werte: **Boolescher Wert** |Standardwert: **FALSCH** |Maximales Zeitintervall, bevor Nachrichten mit hoher Priorität gesendet werden|
|baselineCustomChecksFilePath |Erforderlich: WAHR|Gültige Werte: **Zeichenfolge**, **null** |Standardwert: **NULL** |Vollständiger Pfad der XML-Baselinekonfiguration|
|baselineCustomChecksFileHash |Erforderlich: WAHR|Gültige Werte: **Zeichenfolge**, **null** |Standardwert: **NULL** |`sha256sum` der XML-Konfigurationsdatei. Weitere Informationen finden Sie in der [sha256sum-Referenz](https://linux.die.net/man/1/sha256sum). |

Weitere Baselinebeispiele zum Durcharbeiten finden Sie unter [Beispiel für eine benutzerdefinierte Baseline -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) und [Beispiel für eine benutzerdefinierte Baseline -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Nächste Schritte

- [Zugreifen auf Sicherheitsdaten](how-to-security-data-access.md)
- [Untersuchen eines Geräts](how-to-investigate-device.md)
- Verstehen und Erkunden von [Sicherheitsempfehlungen](concept-recommendations.md)
- Verstehen und Erkunden von [Sicherheitswarnungen](concept-security-alerts.md)
