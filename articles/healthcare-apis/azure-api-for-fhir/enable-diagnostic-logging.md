---
title: Aktivieren der Diagnoseprotokollierung in Azure API for FHIR
description: In diesem Artikel wird das Aktivieren der Diagnoseprotokollierung in Azure API for FHIR® erläutert.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: a884dac90273e98868fed6bfe1cbed23b939d286
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346702"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Aktivieren der Diagnoseprotokollierung in Azure API for FHIR

In diesem Artikel erfahren Sie, wie Sie die Diagnoseprotokollierung in Azure API for FHIR aktivieren und einige Beispiele von Abfragen dieser Protokolle einsehen können. Der Zugriff auf Diagnoseprotokolle ist für jeden Dienst im Gesundheitswesen unerlässlich, bei dem die Einhaltung gesetzlicher Vorschriften (wie z. B. HIPAA) zwingend erforderlich ist. Eine Option in Azure API for FHIR, die Diagnoseprotokolle aktiviert, ist [**Diagnoseeinstellungen**](../../azure-monitor/essentials/diagnostic-settings.md) im Azure-Portal. 

## <a name="view-and-download-fhir-metrics-data"></a>Anzeigen und Herunterladen von FHIR-Metrikdaten

Sie können die Metriken unter „Überwachung > Metriken“ vom Portal aus anzeigen. Die Metriken umfassen die Anzahl von Anforderungen, die durchschnittliche Latenz, die Anzahl von Fehlern, die Datengröße, die verwendeten RUs, die Anzahl der Anforderungen, die die Kapazität überschritten haben, und die Verfügbarkeit (in %). Der folgende Screenshot zeigt RUs, die für eine Beispielumgebung mit sehr wenigen Aktivitäten in den letzten 7 Tagen verwendet wurden. Die Daten können Sie im JSON-Format herunterladen.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="Anzeigen der „Azure API for FHIR“-Metriken über das Portal" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Aktivieren von Überwachungsprotokollen
1. Um die Diagnoseprotokollierung in der Azure API for FHIR zu aktivieren, wählen Sie Ihren Dienst „Azure API for FHIR“ im Azure-Portal aus. 
2. Navigieren zu den **Diagnoseeinstellungen** 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Hinzufügen der Azure-FHIR-Diagnoseeinstellungen." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Wählen Sie **+ Diagnoseeinstellung hinzufügen**  aus.

4. Geben Sie einen Namen für die Einstellung ein.

5. Wählen Sie die Methode aus, die Sie für den Zugriff auf Ihre Diagnoseprotokolle verwenden möchten:

    1. **Archivieren Sie Protokolle zur (manuellen) Überprüfung in einem Speicherkonto**. Das Speicherkonto, das Sie verwenden möchten, muss bereits erstellt worden sein.
    2. **Streamen Sie die Protokolle zu Event Hub**, damit sie von einem Dienst eines Drittanbieters oder einer benutzerdefinierten Analyselösung erfasst werden können. Sie müssen einen Event Hub-Namespace und eine Event Hub-Richtlinie erstellen, ehe Sie diesen Schritt konfigurieren können.
    3. **Streamen Sie die Protokolle zum Log Analytics-Arbeitsbereich** in Azure Monitor. Sie müssen zuvor Ihren Log Analytics-Arbeitsbereich erstellen, bevor Sie diese Option auswählen können.

6. Wählen Sie **AuditLogs** und/oder **AllMetrics** aus. Die Metriken umfassen Dienstname, Verfügbarkeit, Datengröße, Gesamtwartezeit, Gesamtanzahl der Anforderungen, Gesamtanzahl der Fehler und Zeitstempel. Weitere Informationen zu [unterstützten Metriken](../../azure-monitor/essentials/metrics-supported.md#microsofthealthcareapisservices) finden Sie hier. 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Azure FHIR-Diagnoseeinstellungen. Auswählen von „AuditLogs“ und/oder „AllMetrics“" lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Wählen Sie **Speichern** aus.


> [!Note] 
> Es kann bis zu 15 Minuten dauern, bis die ersten Protokolle in Log Analytics angezeigt werden. Wenn Azure API for FHIR aus einer Ressourcengruppe in eine andere oder einem Abonnement in ein anderes verschoben wird, aktualisieren Sie die Einstellung, sobald die Verschiebung beendet ist. 
 
Weitere Informationen zum Arbeiten mit Diagnoseprotokollen finden Sie in der Dokumentation zu [Azure-Ressourcenprotokollen](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="audit-log-details"></a>Überwachungsprotokolldetails
Derzeit gibt der Dienst „Azure API for FHIR“ im Überwachungsprotokoll die folgenden Felder zurück: 

|Feldname  |type  |Notizen  |
|---------|---------|---------|
|CallerIdentity|Dynamisch|Ein generischer Eigenschaftenbehälter mit Identitätsinformationen
|CallerIdentityIssuer|String|Issuer (Aussteller) 
|CallerIdentityObjectId|String|Object_Id 
|CallerIPAddress|String|Die IP-Adresse des Aufrufers 
|CorrelationId|String| Korrelations-ID
|FhirResourceType|String|Der Ressourcentyp, für den der Vorgang ausgeführt wurde
|LogCategory|String|Die Protokollkategorie (wir geben derzeit AuditLogs als LogCategory zurück)
|Standort|String|Der Standort des Servers, der die Anforderung verarbeitet hat (z. B. USA, Süden-Mitte)
|OperationDuration|Int|Der Zeitaufwand für die Bearbeitung dieser Anforderung in Sekunden
|Vorgangsname|String| Beschreibt den Typ des Vorgangs (z. b. Aktualisierung, Suchtyp)
|RequestUri|String|Der Anforderungs-URI 
|ResultType|String|Die derzeit verfügbaren Werte sind **Started**, **Succeeded** oder **Failed**.
|StatusCode|Int|Der HTTP-Statuscode. (Beispiel: 200) 
|TimeGenerated|Datetime|Datum und Uhrzeit des Ereignisses|
|Eigenschaften|String| Beschreibt die Eigenschaften von fhirResourceType
|SourceSystem|String| Quellsystem (in diesem Fall immer Azure)
|TenantId|String|Mandanten-ID
|type|String|Typ des Protokolls (in diesem Fall immer MicrosoftHealthcareApisAuditLog)
|_ResourceId|String|Details zur Ressource

## <a name="sample-queries"></a>Beispielabfragen

Es folgen einige einfache Application Insights-Abfragen, mit denen Sie Ihre Protokolldaten erkunden können.

Führen Sie diese Abfrage aus, um die **letzten 100 Protokolle** anzuzeigen:

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Führen Sie diese Abfrage aus, um die Vorgänge nach **FHIR-Ressourcentyp** zu gruppieren:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Führen Sie diese Abfrage aus, um alle **fehlgeschlagenen Ergebnisse** anzuzeigen.

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Zusammenfassung 
Der Zugriff auf Diagnoseprotokolle ist für die Überwachung eines Diensts und die Bereitstellung von Berichten zur Compliance unerlässlich. Azure API for FHIR ermöglicht Ihnen diese Aufgaben mithilfe von Diagnoseprotokollen. 
 
FHIR ist ein eingetragenes Markenzeichen von HL7 und wird mit Erlaubnis von HL7 verwendet.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Überwachungsprotokolle für Azure API for FHIR aktiviert werden können. Im nächsten Artikel lernen Sie weitere Einstellungen kennen, die Sie in Azure API for FHIR konfigurieren können.
 
>[!div class="nextstepaction"]
>[Zusätzliche Einstellungen](azure-api-for-fhir-additional-settings.md)