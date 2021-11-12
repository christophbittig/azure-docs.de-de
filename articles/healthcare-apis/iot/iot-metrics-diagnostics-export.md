---
title: Exportieren von IoT-Connectormetriken über Diagnoseeinstellungen – Azure Healthcare-APIs
description: In diesem Artikel wird erläutert, wie Sie IoT-Connectormetriken über Diagnoseeinstellungen exportieren.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: eda593fcbb1f7116d7f6a7a1c29af375ca354d6b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337223"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>Exportieren von IoT-Connectormetriken über Diagnoseeinstellungen

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie IoT-Connector-Metrikprotokolle exportieren. Die Metrikprotokollierung wird über die Funktion [**Diagnoseeinstellungen**](../../azure-monitor/essentials/diagnostic-settings.md) im Azure-Portal ermöglicht. 

## <a name="enable-metrics-logging-for-iot-connector"></a>Aktivieren der Metrikprotokollierung für den IoT-Connector
1. Um die Metrikprotokollierung für den IoT-Connector zu aktivieren, wählen Sie ihren Fast Healthcare Interoperability Resources-Dienst (FHIR&#174;) im Azure-Portal. 

2. Navigieren zu den **Diagnoseeinstellungen** 

3. Wählen Sie **+ Diagnoseeinstellung hinzufügen**  aus.

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Geben Sie einen Namen in das Dialogfeld **Name der Diagnoseeinstellung** ein.

5. Wählen Sie die Methode aus, die Sie für den Zugriff auf Ihre Diagnoseprotokolle verwenden möchten:

    1. **Archivieren Sie Protokolle zur (manuellen) Überprüfung in einem Speicherkonto**. Das Speicherkonto, das Sie verwenden möchten, muss bereits erstellt worden sein.
    2. **Streamen sie zur Erfassung** durch einen Drittanbieterdienst oder eine benutzerdefinierte Analyselösung an Event Hub. Sie müssen einen Event Hub-Namespace und eine Event Hub-Richtlinie erstellen, bevor Sie diesen Schritt konfigurieren können.
    3. **Streamen Sie die Protokolle zum Log Analytics-Arbeitsbereich** in Azure Monitor. Sie müssen Ihren Log Analytics-Arbeitsbereich erstellen, bevor Sie diese Option auswählen können.

6. Wählen **Sie Fehler, Datenverkehr und Latenz für** IoT-Connector aus.  Wählen Sie alle zusätzlichen Metrikkategorien aus, die Sie für den FHIR-Dienst erfassen möchten.

7. Wählen Sie **Speichern** aus.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT-Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Es kann bis zu 15 Minuten dauern, bis die ersten Metrikprotokolle im Repository Ihrer Wahl angezeigt werden.  
 
Weitere Informationen zum Arbeiten mit Diagnoseprotokollen finden Sie in der [Dokumentation zum Azure-Ressourcenprotokoll](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="conclusion"></a>Zusammenfassung 
Zugriff auf Metrikprotokolle zu haben, ist für die Überwachung und Problembehandlung von entscheidender Bedeutung.  Mit dem IoT-Connector können Sie diese Aktionen über Metrikprotokolle ausführen. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich häufig gestellte Fragen zum IoT-Connector an.

>[!div class="nextstepaction"]
>[Häufig gestellte Fragen zum IoT-Connector](iot-connector-faqs.md)

(FHIR&#174;) ist eine registrierte Marke von HL7 und wird mit der Berechtigung von HL7 verwendet.
