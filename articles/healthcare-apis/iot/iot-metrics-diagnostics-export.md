---
title: Exportieren von IoT-Connectormetriken über Diagnoseeinstellungen – Azure Healthcare-APIs
description: In diesem Artikel wird erläutert, wie Sie IoT-Connectormetriken über Diagnoseeinstellungen exportieren.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 09/30/2021
ms.author: jasteppe
ms.openlocfilehash: 23908c8ab910324cda4cd2802158447a1c97db4e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354590"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>Exportieren von IoT-Connectormetriken über Diagnoseeinstellungen

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie Metrikprotokolle des IoT-Connectors exportieren. Die Metrikprotokollierung wird über die Funktion [**Diagnoseeinstellungen**](../../azure-monitor/essentials/diagnostic-settings.md) im Azure-Portal ermöglicht. 

## <a name="enable-metrics-logging-for-iot-connector"></a>Aktivieren der Metrikprotokollierung für den IoT-Connector
1. Um die Metrikprotokollierung für den IoT-Connector zu aktivieren, wählen Sie im Azure-Portal Ihren dienst Fast Healthcare Interoperability Resources (FHIR&#174;) aus. 

2. Navigieren zu den **Diagnoseeinstellungen** 

3. Wählen Sie **+ Diagnoseeinstellung hinzufügen**  aus.

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Geben Sie einen Namen in das Dialogfeld **Name der Diagnoseeinstellung** ein.

5. Wählen Sie die Methode aus, die Sie für den Zugriff auf Ihre Diagnoseprotokolle verwenden möchten:

    1. **Archivieren Sie Protokolle zur (manuellen) Überprüfung in einem Speicherkonto**. Das Speicherkonto, das Sie verwenden möchten, muss bereits erstellt worden sein.
    2. **Streamen Sie die Protokolle zu Event Hub**, damit sie von einem Dienst eines Drittanbieters oder einer benutzerdefinierten Analyselösung erfasst werden können. Sie müssen einen Event Hub-Namespace und eine Event Hub-Richtlinie erstellen, ehe Sie diesen Schritt konfigurieren können.
    3. **Streamen Sie die Protokolle zum Log Analytics-Arbeitsbereich** in Azure Monitor. Sie müssen Ihren Log Analytics-Arbeitsbereich erstellen, bevor Sie diese Option auswählen können.

6. Wählen Sie **Fehler, Datenverkehr und Latenz** für IoT-Connector aus.  Wählen Sie zusätzliche Metrikkategorien aus, die Sie für den FHIR-Dienst erfassen möchten.

7. Wählen Sie **Speichern** aus.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT-Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Es kann bis zu 15 Minuten dauern, bis die ersten Metrikprotokolle im Repository Ihrer Wahl angezeigt werden.  
 
Weitere Informationen zum Arbeiten mit Diagnoseprotokollen finden Sie in der [Dokumentation zum Azure-Ressourcenprotokoll](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="conclusion"></a>Zusammenfassung 
Zugriff auf Metrikprotokolle zu haben, ist für die Überwachung und Problembehandlung von entscheidender Bedeutung.  Mit dem IoT-Connector können Sie diese Aktionen über Metrikprotokolle ausführen. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die häufig gestellten Fragen zum IoT-Connector an.

>[!div class="nextstepaction"]
>[Häufig gestellte Fragen zum IoT-Connector](../fhir/fhir-faq.md)

(FHIR&#174;) ist eine registrierte Marke von HL7 und wird mit der Berechtigung von HL7 verwendet.