---
title: Aktivieren der Diagnoseprotokollierung im DICOM-Dienst – Azure Healthcare-APIs
description: In diesem Artikel wird erläutert, wie Sie die Diagnoseprotokollierung im DICOM-Dienst aktivieren.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 51a162ccbad5813a559cf27c26adcd964adeaf07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355386"
---
# <a name="enable-diagnostic-logging-in-the-dicom-service"></a>Aktivieren der Diagnoseprotokollierung im DICOM-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie die Diagnoseprotokollierung im DICOM-Dienst aktivieren und einige Beispielabfragen für diese Protokolle überprüfen können. Der Zugriff auf Diagnoseprotokolle ist für jeden Gesundheitsdienst unerlässlich, bei dem die Einhaltung gesetzlicher Anforderungen erforderlich ist. Das Feature im DICOM-Dienst ermöglicht Diagnoseprotokolle, die [Diagnoseeinstellungen](../../azure-monitor/essentials/diagnostic-settings.md) im Azure-Portal. 

## <a name="enable-audit-logs"></a>Aktivieren von Überwachungsprotokollen

1. Um den DICOM-Dienst für die Diagnoseprotokollierung zu aktivieren, wählen Sie ihren DICOM-Dienst im Azure-Portal.
2. Wählen Sie das **Blatt Aktivitätsprotokoll** und dann **Diagnoseeinstellungen aus.**

   [![Azure-Aktivitätsprotokoll. ](media/dicom-activity-log.png) ](media/dicom-activity-log.png#lightbox)

3. Wählen Sie **+Diagnoseeinstellung hinzufügen**  aus.

   [![Fügen Sie Diagnoseeinstellungen hinzu. ](media/add-diagnostic-settings.png) ](media/add-diagnostic-settings.png#lightbox)

4. Geben Sie den **Namen der Diagnoseeinstellungen ein.**

   [![Konfigurieren Sie Diagnoseeinstellungen. ](media/configure-diagnostic-settings.png) ](media/configure-diagnostic-settings.png#lightbox)

5. Wählen Sie **die Kategorie-** **und Zieldetails** für den Zugriff auf die Diagnoseprotokolle aus.

   * **Senden sie an den Log Analytics-Arbeitsbereich** im Azure Monitor. Sie müssen Ihren Log Analytics-Arbeitsbereich erstellen, bevor Sie diese Option auswählen können. Weitere Informationen zu den Plattformprotokollen finden Sie unter [Übersicht über Azure-Plattformprotokolle.](../../azure-monitor/essentials/platform-logs-overview.md)
   * **Archivieren Sie Protokolle zur (manuellen) Überprüfung in einem Speicherkonto**. Das Speicherkonto, das Sie verwenden möchten, muss bereits erstellt worden sein.
   * **Streamen sie zur Erfassung** durch einen Drittanbieterdienst oder eine benutzerdefinierte Analyselösung an einen Event Hub. Sie müssen einen Event Hub-Namespace und eine Event Hub-Richtlinie erstellen, bevor Sie diesen Schritt konfigurieren können.
   * **Senden Sie an die Partnerlösung,** mit der Sie als Partnerorganisation in Azure arbeiten. Informationen zu potenziellen Partnerintegrationen finden Sie in der [Dokumentation zu Azure-Partnerlösungen.](../../partner-solutions/overview.md)

     Informationen zu unterstützten Metriken finden Sie unter [Unterstützte Metriken mit Azure Monitor](.././../azure-monitor/essentials/metrics-supported.md).

6. Wählen Sie **Speichern** aus.


   > [!Note] 
   > Es kann bis zu 15 Minuten dauern, bis die ersten Protokolle in Log Analytics angezeigt werden. Wenn der DICOM-Dienst von einer Ressourcengruppe oder einem Abonnement in eine andere verschoben wird, aktualisieren Sie die Einstellungen, sobald die Verlegung abgeschlossen ist. 
 
   Informationen zum Arbeiten mit Diagnoseprotokollen finden Sie in der [Dokumentation zum Azure-Ressourcenprotokoll.](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>Überwachungsprotokolldetails

Der DICOM-Dienst gibt die folgenden Felder im Überwachungsprotokoll zurück: 

|Feldname  |type  |Hinweise  |
|---------|---------|---------|
|correlationId|String|Korrelations-ID
|category|String|Protokollkategorie (derzeit "AuditLogs") 
|operationName|String|Beschreibt den Typ des Vorgangs (z.B. Abrufen, Store, Abfrage usw.). 
|time|Datetime|Datum und Uhrzeit des Ereignisses. 
|resourceId|String| Azure-Pfad zur Ressource.
|identity|Dynamisch|Eine generische Eigenschaftentüte, die Identitätsinformationen enthält (gilt derzeit nicht für DICOM).
|callerIpAddress|String|Die IP-Adresse des Aufrufers.
|Standort|String|Der Speicherort des Servers, der die Anforderung verarbeitet hat.
|uri|String|Der Anforderungs-URI.
|resultType|String| Die derzeit verfügbaren Werte sind Gestartet, Erfolgreich oder Fehler.
|resultSignature|Int|Der HTTP-Statuscode (z.B. 200)
|properties|String|Beschreibt die Eigenschaften, einschließlich Ressourcentyp, Ressourcenname, Abonnement-ID, Überwachungsaktion usw.
|type|String|Protokolltyp (in diesem Fall immer MicrosoftHealthcareApisAuditLog).
|Ebene|String|Protokollebene (Information, Fehler).
|operationVersion|String| Derzeit leer. Wird zum Anzeigen der API-Version verwendet.


## <a name="sample-queries"></a>Beispielabfragen

Im Folgenden finden Sie einige grundlegende Anwendungsabfragen Insights die Sie verwenden können, um Ihre Protokolldaten zu untersuchen.

Führen Sie die folgende Abfrage aus, um **die 100 neuesten Protokolle** zu sehen:

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Führen Sie die folgende Abfrage aus, um Vorgänge nach **DICOM-Ressourcentyp zu gruppen:**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by DICOMResourceType
```

Führen Sie die folgende Abfrage aus, um alle fehlerhaften **Ergebnisse zu erhalten.**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Zusammenfassung

Der Zugriff auf Diagnoseprotokolle ist für die Überwachung eines Diensts und die Bereitstellung von Berichten zur Compliance unerlässlich. Mit dem DICOM-Dienst können Sie diese Aktionen über Diagnoseprotokolle ausführen. 

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie Überwachungsprotokolle für den DICOM-Dienst aktivieren. Informationen zum Azure-Aktivitätsprotokoll finden Sie unter
 
>[!div class="nextstepaction"]
>[Ereignisschema des Azure-Aktivitätsprotokolls](.././../azure-monitor/essentials/activity-log-schema.md)
