---
title: Anzeigen und Aktivieren von Diagnoseeinstellungen im FHIR-Dienst – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie Diagnoseeinstellungen im FHIR-Dienst aktivieren und einige Beispielabfragen für Überwachungsprotokolle überprüfen.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: how-to
ms.date: 10/12/2021
ms.author: zxue
ms.openlocfilehash: 7574d4abf20e07019016796e937cf80c7d915128
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130187571"
---
# <a name="view-and-enable-diagnostic-settings-in-the-fhir-service"></a>Anzeigen und Aktivieren von Diagnoseeinstellungen im FHIR-Dienst

Der Zugriff auf Diagnoseprotokolle ist für jeden Gesundheitsdienst von entscheidender Bedeutung. Die Einhaltung gesetzlicher Anforderungen wie Health Insurance Portability and Accountability Act (HIPAA) ist ein Muss. In diesem Artikel erfahren Sie, wie Sie Einstellungen für Diagnoseprotokolle im FHIR-Dienst in Azure Healthcare-APIs auswählen. Außerdem überprüfen Sie einige Beispielabfragen für diese Protokolle.

> [!IMPORTANT]
> Der Azure Healthcare-APIs-Dienst befindet sich derzeit in der Vorschau. Die [zusätzlichen Nutzungsbedingungen für Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bestimmungen, die für Azure-Features gelten, die sich in der Betaphase, in der Vorschauphase oder anderweitig noch nicht in der allgemeinen Verfügbarkeit befinden.

## <a name="steps-to-enable-diagnostic-logs"></a>Schritte zum Aktivieren von Diagnoseprotokollen

1. Wählen Sie im Azure-Portal Ihren FHIR-Dienst aus.

2. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.

3. Wählen Sie **+ Diagnoseeinstellungen hinzufügen** aus.

   [![Screenshot der Seite "Diagnoseeinstellungen" im Azure-Portal ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png) ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png#lightbox)

4. Geben Sie den Namen der **Diagnoseeinstellung** ein.
 
   [![Screenshot der Zieldetails und des Kontrollkästchens zum Aktivieren oder Deaktivieren von Überwachungsprotokollen ](media/diagnostic-logs/fhir-diagnostic-settings-add.png) ](media/diagnostic-logs/fhir-diagnostic-settings-add.png#lightbox)

5. Wählen Sie die Methode aus, die Sie verwenden möchten, um auf Ihre Protokolle zuzugreifen:

   - **An Log Analytics-Arbeitsbereich senden** dient zum Senden von Protokollen und Metriken an einen Log Analytics-Arbeitsbereich in Azure Monitor. Sie müssen Ihren Log Analytics-Arbeitsbereich erstellen, bevor Sie diese Option auswählen können.
   
   - **Das Archivieren in einem Speicherkonto** dient zur Überwachung oder manuellen Überprüfung. Das Speicherkonto, das Sie verwenden möchten, muss bereits erstellt sein. Die Aufbewahrungsoption gilt nur für ein Speicherkonto. Die Aufbewahrungsrichtlinie reicht von 1 bis 365 Tagen. Wenn Sie keine Aufbewahrungsrichtlinie anwenden und Daten dauerhaft beibehalten möchten, legen Sie die Aufbewahrungsdauer (Tage) auf 0 fest.

   - **Streamen an einen Event Hub** dient zur Erfassung durch einen Drittanbieterdienst oder eine benutzerdefinierte Analyselösung. Sie müssen einen Event Hub-Namespace und eine Event Hub-Richtlinie erstellen, bevor Sie diese Option konfigurieren können.
   
   - **An Partnerlösung senden** sollte ausgewählt werden, wenn Sie eine Von Azure unterstützte Partnerlösung aktiviert haben. Weitere Informationen finden Sie unter [Erweitern von Azure mit Lösungen von Partnern.](../../partner-solutions/overview.md)

6. Wählen Sie **AuditLogs aus.**

7. Wählen Sie **Speichern** aus.

> [!NOTE]
> Es kann bis zu 15 Minuten dauern, bis die ersten Protokolle im Log Analytics-Arbeitsbereich angezeigt werden, wenn Sie diese Methode ausgewählt haben. Wenn der FHIR-Dienst von einer Ressourcengruppe oder einem Abonnement in eine andere verschoben wird, aktualisieren Sie die Einstellungen nach Abschluss der Verschiebung.


## <a name="diagnostic-log-details"></a>Diagnoseprotokolldetails

Zu diesem Zeitpunkt gibt der FHIR-Dienst die folgenden Felder in einem Diagnoseprotokoll zurück:

|Feldname|Typ|Hinweise|
|----------|----|-----|
|`CallerIdentity` |Dynamisch|Ein generischer Eigenschaftenbehälter, der Identitätsinformationen enthält.|
|`CallerIdentityIssuer` | String| Der Aussteller.|
|`CallerIdentityObjectId` | String| Die Objekt-ID.|
|`CallerIPAddress` | String| Die IP-Adresse des Aufrufers.|
|`CorrelationId` | String| Die Korrelations-ID.|
|`FhirResourceType` | String| Der Ressourcentyp, für den der Vorgang ausgeführt wurde.|
|`LogCategory` | String| Die Protokollkategorie. (In diesem Artikel wird `AuditLogs` zurückgegeben.)|
|`Location` | String| Der Speicherort des Servers, der die Anforderung verarbeitet hat. Beispiel: `South Central US`.|
|`OperationDuration` | Int| Die Zeit, die zum Abschließen dieser Anforderung in Sekunden gedauert hat.|
|`OperationName` | String| Der Typ des Vorgangs. Beispiel: `update` oder `search-type`.|
|`RequestUri` | String| Der Anforderungs-URI.|
|`ResultType` | String| Der Status des Protokolls. Verfügbare Werte sind `Started` , `Succeeded` oder `Failed` .|
|`StatusCode` | Int| Der HTTP-Statuscode. Beispiel: `200`.|
|`TimeGenerated` | Datetime| Das Datum und die Uhrzeit des Ereignisses.|
|`Properties` | String| Die Eigenschaften von `FhirResourceType` .|
|`SourceSystem` | String| Das Quellsystem, das in diesem Fall immer `Azure` ist.|
|`TenantId` | String | Die Mandanten-ID.|
|`Type` | String| Der Typ des Protokolls, der in diesem Fall immer `MicrosoftHealthcareApisAuditLog` ist.|
|`_ResourceId` | String| Details zur Ressource.|       
        
## <a name="sample-queries"></a>Beispielabfragen

Sie können diese grundlegenden Application Insights-Abfragen verwenden, um Ihre Protokolldaten zu untersuchen:

- Führen Sie die folgende Abfrage aus, um die *100 neuesten* Protokolle anzuzeigen:

  `Insights
  MicrosoftHealthcareApisAuditLogs
  | limit 100`

- Führen Sie die folgende Abfrage aus, um Vorgänge nach *FHIR-Ressourcentyp* zu gruppieren:

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | summarize count() by FhirResourceType`

- Führen Sie die folgende Abfrage aus, um alle *fehlerhaften Ergebnisse* abzurufen:

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | where ResultType == "Failed"`   

## <a name="conclusion"></a>Zusammenfassung

Der Zugriff auf Diagnoseprotokolle ist für die Überwachung eines Diensts und die Bereitstellung von Berichten zur Compliance unerlässlich. In diesem Artikel haben Sie erfahren, wie Sie diese Protokolle für den FHIR-Dienst aktivieren. 

> [!NOTE]
> Metriken werden hinzugefügt, wenn der Azure Healthcare-APIs-Dienst allgemein verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über den FHIR-Dienst finden Sie unter:

>[!div class="nextstepaction"]
>[Was ist der FHIR-Dienst?](overview.md)   

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.
