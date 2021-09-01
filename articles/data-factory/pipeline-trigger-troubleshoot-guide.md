---
title: Problembehandlung bei der Pipelineorchestrierung und Pipelinetriggern in Azure Data Factory
description: Wenden Sie verschiedene Methoden an, um Probleme mit Pipelinetriggern in Azure Data Factory zu beheben.
author: ssabat
ms.service: data-factory
ms.date: 08/17/2021
ms.subservice: troubleshooting
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: aed6df814ddfb240093aad8ff981e9b89a6f2f52
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867570"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Problembehandlung bei der Pipelineorchestrierung und Pipelinetriggern in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Eine Pipelineausführung in Azure Data Factory definiert eine Instanz einer Pipelineausführung. Beispiel: Angenommen, Sie verfügen über eine Pipeline, die um 8:00, 9:00 und 10:00 Uhr ausgeführt wird. In diesem Fall erfolgen drei separate Pipelineausführungen. Jede Pipelineausführung besitzt eine eindeutige Pipelineausführungs-ID. Eine Ausführungs-ID ist eine GUID (Globally Unique Identifier), die die jeweilige Pipelineausführung eindeutig definiert.

Zur Instanziierung von Pipelineausführungen werden in der Regel Argumente an in der Pipeline definierte Parameter übergeben. Sie können eine Pipeline entweder manuell oder mithilfe eines Triggers ausführen. Weitere Informationen finden Sie unter [Pipelineausführung und -trigger in Azure Data Factory](concepts-pipeline-execution-triggers.md).

## <a name="common-issues-causes-and-solutions"></a>Bekannte Probleme, Ursachen und Lösungen

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Eine Pipeline für eine Azure Functions-App löst einen Fehler bei privaten Endpunktverbindungen aus
 
Sie führen Data Factory und eine Funktions-App auf einem privaten Endpunkt in Azure aus. Sie versuchen, eine Pipeline auszuführen, die mit der Funktions-App interagiert. Sie haben drei verschiedene Methoden ausprobiert, aber eine gibt den Fehler „Ungültige Anforderung“ zurück, und die anderen beiden Methoden geben „Fehler 103: Unzulässig“ zurück.

**Ursache**

Data Factory unterstützt für Funktions-Apps derzeit keinen Connector für private Endpunkte. Azure Functions lehnt Aufrufe ab, weil gemäß Konfiguration nur Verbindungen über eine private Verbindung zugelassen werden.

**Auflösung**

Erstellen Sie einen **PrivateLinkService**-Endpunkt, und geben Sie den DNS der Funktions-App an.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Eine Pipelineausführung wurde zwar abgebrochen, aber der Status wird weiterhin in der Überwachung angezeigt

**Ursache**

Wenn Sie eine Pipelineausführung abbrechen, wird der Status häufig weiterhin in der Pipelineüberwachung aufgeführt. Dies geschieht aufgrund eines Browsercacheproblems. Außerdem verwenden Sie möglicherweise nicht die richtigen Überwachungsfilter.

**Auflösung**

Aktualisieren Sie den Browser, und wenden Sie die richtigen Überwachungsfilter an.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Beim Kopieren einer Pipeline wird der Fehler „DelimitedTextMoreColumnsThanDefined" angezeigt
 
 **Ursache**
 
Wenn ein Ordner, den Sie kopieren, Dateien mit unterschiedlichen Schemas (z. B. eine variable Anzahl von Spalten, unterschiedliche Trennzeichen oder abweichende Anführungszeichen) oder Datenproblemen enthält, löst die Data Factory-Pipeline möglicherweise diesen Fehler aus:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Auflösung**

Wählen Sie beim Erstellen der Copy-Aktivität die Option **Binärkopie** aus. Auf diese Weise werden die Dateien bei Massenkopien oder bei der Migration Ihrer Daten von einem Data Lake zu einem anderen nicht von Data Factory geöffnet, um das Schema zu lesen. Stattdessen behandelt Data Factory jede Datei als Binärdatei und kopiert sie in den anderen Speicherort.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>Eine Pipelineausführung führt zu einem Fehler, wenn Sie die Kapazitätsgrenze der Integration Runtime für Datenfluss erreichen

**Problem**

Fehlermeldung:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Ursache**

Sie haben die Kapazitätsgrenze der Integration Runtime erreicht. Möglicherweise führen Sie große Mengen an Datenflüssen gleichzeitig mit derselben Integration Runtime aus. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2).

**Auflösung**
 
- Führen Sie Ihre Pipelines zu verschiedenen Triggerzeiten aus.
- Erstellen Sie eine neue Integration Runtime, und teilen Sie Ihre Pipelines auf mehrere Integration Runtimes auf.

### <a name="a-pipeline-run-error-while-invoking-rest-api-in-a-web-activity"></a>Fehler der Pipelineausführung beim Aufrufen der REST-API in einer Webaktivität

**Problem**

Fehlermeldung:

`
Operation on target Cancel failed: {“error”:{“code”:”AuthorizationFailed”,”message”:”The client ‘<client>’ with object id ‘<object>’ does not have authorization to perform action ‘Microsoft.DataFactory/factories/pipelineruns/cancel/action’ over scope ‘/subscriptions/<subscription>/resourceGroups/<resource group>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelineruns/<pipeline run id>’ or the scope is invalid. If access was recently granted, please refresh your credentials.”}}
`

**Ursache**

Pipelines können die Webaktivität nur dann zum Aufrufen der REST-API-Methoden von ADF verwenden, wenn dem Azure Data Factory-Mitglied die Rolle „Mitwirkender“ zugewiesen ist. Sie müssen zuerst die verwaltete Azure Data Factory-Identität konfigurieren und der Sicherheitsrolle „Mitwirkender“ hinzufügen. 

**Auflösung**

Bevor Sie die REST-API von Azure Data Factory auf der Registerkarte „Einstellungen“ einer Webaktivität verwenden, muss die Sicherheit konfiguriert werden. Azure Data Factory-Pipelines können die Webaktivität nur dann zum Aufrufen der REST-API-Methoden von ADF verwenden, wenn der verwalteten Azure Data Factory-Identität die Rolle *Mitwirkender* zugewiesen ist. Öffnen Sie zunächst das Azure-Portal, und klicken Sie im linken Menü auf den Link **Alle Ressourcen**. Wählen Sie **Azure Data Factory** aus, um die verwaltete ADF-Identität mit der Rolle „Mitwirkender“ hinzuzufügen. Klicken Sie dazu im Feld *Rollenzuweisung hinzufügen* auf die Schaltfläche **Hinzufügen**.


### <a name="how-to-check-and-branch-on-activity-level-success-and-failure-in-pipelines"></a>Überprüfen und Verzweigen nach Erfolg und Fehler in Pipelines auf Aktivitätsebene

**Ursache**

Die Azure Data Factory-Orchestrierung erlaubt bedingte Logik und ermöglicht es, basierend auf dem Ergebnis einer vorherigen Aktivität verschiedene Pfade zu nutzen. Vier bedingte Pfade sind zulässig: **Bei Erfolg** (Standardpfad), **Bei Fehlern**, **Nach Abschluss** und **Bei Überspringen**. 

Azure Data Factory wertet das Ergebnis aller Aktivitäten auf Blattebene aus. Die Pipelineergebnisse gelten nur dann als erfolgreich, wenn alle Blätter erfolgreich waren. Wenn eine Blattaktivität übersprungen wurde, wird stattdessen die übergeordnete Aktivität ausgewertet. 

**Lösung**

* Implementieren Sie Überprüfungen auf Aktivitätsebene. Lesen Sie dazu [Informationen zu Pipelinefehlern und zur Fehlerbehandlung](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
* Verwenden Sie Azure Logic Apps, um Pipelines in regelmäßigen Abständen wie unter [Abfragen nach Factory](/rest/api/datafactory/pipelineruns/querybyfactory) beschrieben zu überwachen.
* [Visuelles Überwachen der Pipeline](monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Überwachen von Pipelinefehlern in regelmäßigen Abständen

**Ursache**

Es ist häufig erforderlich, fehlerhafte Data Factory-Pipelines in Intervallen zu überwachen, z. B. alle 5 Minuten. Sie können die Pipelineausführungen einer Data Factory mithilfe des Endpunkts abfragen und filtern. 

**Auflösung**
* Sie können eine Azure-Logik-App einrichten, um alle fehlerhaften Pipelines alle 5 Minuten abzufragen. Dieser Vorgang wird unter [Abfragen nach Factory](/rest/api/datafactory/pipelineruns/querybyfactory) beschrieben. Anschließend können Sie Incidents in Ihrem Ticketsystem melden.
* Sie können Pipelines und Aktivitäten wie [hier](monitor-visually.md#rerun-pipelines-and-activities) beschrieben erneut ausführen.
* Wenn eine Aktivität fehlschlägt oder abgebrochen wird, können Sie sie erneut ausführen wie unter [Erneutes Ausführen fehlgeschlagener Aktivitäten](monitor-visually.md#rerun-from-failed-activity) beschrieben.
* [Visuelles Überwachen der Pipeline](monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>Ein höherer Grad an Parallelität führt nicht zu einem höheren Durchsatz.

**Ursache** 

Der Grad an Parallelität in *ForEach* ist tatsächlich der maximale Grad an Parallelität. Es kann nicht garantiert werden, dass eine bestimmte Anzahl von Ausführungen gleichzeitig erfolgt, mit diesem Parameter wird jedoch sichergestellt, dass der festgelegte Wert nie überschritten wird. Sie sollten dies als Limit betrachten, das Sie beim Steuern des gleichzeitigen Zugriffs auf Ihre Quellen und Senken nutzen.

Bekannte Fakten zu *ForEach*
 * ForEach verfügt über eine Eigenschaft mit dem Namen „batch count(n)“ mit dem Standardwert 20 und dem Höchstwert 50.
 * Die Batchanzahl n wird zum Erstellen von n Warteschlangen verwendet. 
 * Jede Warteschlange wird sequenziell ausgeführt, Sie können jedoch mehrere Warteschlangen parallel ausführen.
 * Die Warteschlangen werden vorab erstellt. Dies bedeutet, dass die Warteschlangen während der Laufzeit nicht neu verteilt werden.
 * Zu jedem Zeitpunkt gibt es höchstens ein Element, das pro Warteschlange verarbeitet wird. Dies bedeutet, dass zu einem bestimmten Zeitpunkt höchstens n Elemente verarbeitet werden.
 * Die Gesamtverarbeitungszeit für ForEach ist gleich der Verarbeitungszeit der längsten Warteschlange. Dies bedeutet, dass die ForEach-Aktivität davon abhängt, wie die Warteschlangen erstellt werden.
 
**Auflösung**

 * Sie sollten keine *SetVariable*-Aktivität in *ForEach* verwenden, die parallel ausgeführt wird.
 * Wenn Sie die Art und Weise berücksichtigen, in der die Warteschlangen erstellt werden, können Kunden die Leistung von ForEach verbessern, indem sie mehrere *ForEach-Vorgänge* festlegen, wobei jedes *ForEach-Element* Elemente mit ähnlicher Verarbeitungszeit enthält. 
 * Dadurch wird sichergestellt, dass lange Ausführungen parallel und nicht sequenziell verarbeitet werden.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>Eine Pipeline befindet sich für längere Zeit in einer Warteschlange oder ist für längere Zeit hängen geblieben
 
 **Ursache**
 
 Dies kann verschiedene Ursachen haben, wie z. B. das Erreichen von Parallelitätsgrenzen, Dienstausfälle, Netzwerkfehler usw.
 
 **Auflösung**
 
* **Parallelitätsgrenze:** Wenn Ihre Pipeline über eine Parallelitätsrichtlinie verfügt, stellen Sie sicher, dass keine alten aktiven Pipelineausführungen vorhanden sind. 
* **Überwachen von Grenzwerten**: Navigieren Sie zum ADF-Erstellungsbereich, wählen Sie Ihre Pipeline aus, und ermitteln Sie, ob ihr eine Parallelitätseigenschaft zugewiesen ist. Falls ja, navigieren Sie zur Überwachungsansicht. Vergewissern Sie sich, dass für die letzten 45 Tage keine aktiven Vorgänge vorhanden sind. Falls aktive Vorgänge vorhanden sind, können Sie sie stornieren. Daraufhin sollte die neue Pipelineausführung gestartet werden.

* **Vorübergehende Probleme:** Es ist möglich, dass Ihre Ausführung durch ein vorübergehendes Netzwerkproblem, fehlerhafte Anmeldeinformationen, Dienstausfälle usw. beeinträchtigt wurde. Für solche Fälle verfügt Azure Data Factory über einen internen Wiederherstellungsprozess, der alle Ausführungen überwacht und sie startet, wenn ein Fehler aufgetreten ist. Sie können Pipelines und Aktivitäten wie [hier](monitor-visually.md#rerun-pipelines-and-activities) beschrieben erneut ausführen. Wenn eine Aktivität fehlschlägt oder abgebrochen wird, können Sie sie erneut ausführen wie unter [Erneutes Ausführen fehlgeschlagener Aktivitäten](monitor-visually.md#rerun-from-failed-activity) beschrieben. Dieser Prozess wird stündlich ausgeführt. Sollte Ihre Ausführung länger als eine Stunde hängen bleiben, erstellen Sie eine entsprechende Supportanfrage.


 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>Längere Startzeiten für Aktivitäten ihm Rahmen von ADF-Kopiervorgängen und des Datenflusses

**Ursache**

Dies kann vorkommen, wenn Sie keine Gültigkeitsdauer für den Datenfluss oder eine optimierte SHIR implementiert haben.

**Auflösung**

* Wenn das Starten jeder Kopieraktivität bis zu zwei Minuten dauert und das Problem hauptsächlich bei einem VNET-Beitritt auftritt (nicht in der Azure IR), handelt es sich ggf. um ein Problem mit der Kopierleistung. Informationen zu den Problembehandlungsschritten finden Sie unter [Kopierleistungsverbesserung.](copy-activity-performance-troubleshooting.md)
* Sie können die Gültigkeitsdauer (TTL, Time to Live) verwenden, um die Startzeit des Clusters für Datenflussaktivitäten zu verkürzen. Weitere Informationen finden Sie unter [Datenfluss-Integration Runtime.](control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Kapazitätsprobleme in SHIR (selbstgehostete Integration Runtime)
 
 **Ursache**
 
Dies kann vorkommen, wenn Sie die SHIR nicht gemäß Ihrer Arbeitsauslastung hochskaliert haben.

**Auflösung**

* Wenn für die SHIR ein Kapazitätsproblem auftritt, sollten Sie für die VM ein Upgrade durchführen, um den Knoten zu vergrößern und so eine bessere Verteilung der Aktivitäten zu ermöglichen. Falls Sie eine Fehlermeldung im Zusammenhang mit einem allgemeinen Fehler, einem Upgrade oder einem Konnektivitätsproblem für eine selbstgehostete IR erhalten, was eine lange Warteschlange zur Folge haben kann, helfen Ihnen die Informationen zur [Problembehandlung für die selbstgehostete Integration Runtime](self-hosted-integration-runtime-troubleshoot-guide.md) weiter.

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>Fehlermeldungen aufgrund von langen Warteschlangen für ADF-Kopieraktivitäten und den Datenfluss

**Ursache**

Fehlermeldungen im Zusammenhang mit langen Warteschlangen können aus verschiedenen Gründen auftreten. 

**Auflösung**
* Falls Sie über Connectors eine Fehlermeldung von einer beliebigen Quelle oder einem beliebigen Ziel erhalten, was eine lange Warteschlange zur Folge haben kann, lesen Sie den [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md).
* Falls Sie eine Fehlermeldung im Zusammenhang mit dem Zuordnungsdatenfluss erhalten, was eine lange Warteschlange zur Folge haben kann, lesen Sie den [Leitfaden zur Problembehandlung für Datenflüsse](data-flow-troubleshoot-guide.md).
* Falls Sie eine Fehlermeldung im Zusammenhang mit anderen Aktivitäten (z. B. Databricks, benutzerdefinierten Aktivitäten oder HDI) erhalten, was eine lange Warteschlange zur Folge haben kann, helfen Ihnen die Informationen im [Leitfaden zur Problembehandlung für Aktivitäten](data-factory-troubleshoot-guide.md) weiter.
* Falls Sie eine Fehlermeldung im Zusammenhang mit der Ausführung von SSIS-Paketen erhalten, was eine lange Warteschlange zur Folge haben kann, finden Sie weitere Informationen unter [Behandeln von Problemen bei der Paketausführung in der SSIS Integration Runtime](ssis-integration-runtime-ssis-activity-faq.yml) und [Problembehandlung bei der SSIS Integration Runtime-Verwaltung in Azure Data Factory](ssis-integration-runtime-management-troubleshoot.md).

### <a name="error-message---codebadrequest-messagenull"></a>Fehlermeldung: "code":"BadRequest", "message":"null"

**Ursache**

Dies ist ein Benutzerfehler, weil die JSON-Payload, die bei „management.azure.com“ ankommt, beschädigt ist. Es werden keine Protokolle gespeichert, weil der Benutzeraufruf die ADF-Dienstebene nicht erreicht hat.

**Auflösung**

Führen Sie die Netzwerkablaufverfolgung Ihres API-Aufrufs über das ADF-Portal im Edge-/Chrome-Browser mithilfe der **Entwicklertools** aus. Es wird die beanstandete JSON-Nutzlast angezeigt, die möglicherweise wegen eines Sonderzeichens (z. B. $), Leerzeichen und andere Arten von Benutzereingaben ungültig ist. Nachdem Sie den Zeichenfolgenausdruck korrigiert haben, können Sie im Browser mit den restlichen ADF-Nutzungsaufrufen fortfahren.

### <a name="foreach-activities-do-not-run-in-parallel-mode"></a>ForEach-Aktivitäten werden nicht im parallelen Modus ausgeführt

**Ursache**

Sie führen ADF im Debugmodus aus.

**Auflösung**

Führen Sie die Pipeline im Triggermodus aus.

### <a name="cannot-publish-because-account-is-locked"></a>Kann nicht veröffentlicht werden, weil das Konto gesperrt ist

**Ursache**

Sie haben Änderungen im Kollaborationsbranch vorgenommen, um einen Speicherereignistrigger zu entfernen. Sie versuchen, zu veröffentlichen, und stoßen auf die Nachricht `Trigger deactivation error`.

**Auflösung**

Der Grund: Das für den Ereignistrigger verwendete Speicherkonto ist gesperrt. Entsperren Sie das Konto.

### <a name="expression-builder-fails-to-load"></a>Fehler beim Laden des Ausdrucks-Generators

**Ursache**

Der Ausdrucks-Generator kann aufgrund von Netzwerk- oder Cacheproblemen mit dem Webbrowser nicht geladen werden.  

**Auflösung**


Verwenden Sie einen unterstützten, auf die neueste Version aktualisierten Webbrowser, löschen Sie Cookies für die Website und aktualisieren Sie die Seite.

### <a name="codebadrequestmessageerrorcodeflowrunsizelimitexceeded"></a>"Code":"BadRequest","message":"ErrorCode=FlowRunSizeLimitExceeded

**Ursache**

Sie haben viele Aktivitäten verkettet.

**Auflösung**

Sie können Ihre Pipelines in Unterpipelines aufteilen und sie mit der **ExecutePipeline**-Aktivität zusammensetzen. 

###  <a name="how-to-optimize-pipeline-with-mapping-data-flows-to-avoid-internal-server-errors-concurrency-errors-etc-during-execution"></a>Optimieren der Pipeline mit Zuordnungsdatenflüssen, um interne Serverfehler, Parallelitätsfehler usw. während der Ausführung zu vermeiden

**Ursache**

Sie haben den Zuordnungsdatenfluss nicht optimiert.

**Auflösung**

* Verwenden Sie arbeitsspeicheroptimierte Computefunktionen, wenn Sie große Datenmengen und Transformationen verarbeiten.
* Reduzieren Sie die Batchgröße im Fall einer für jede Aktivität.
* Skalieren Sie Ihre Datenbanken und Warehouses entsprechend der Leistung Ihrer ADF hoch. 
* Verwenden Sie eine separate IR (Integration Runtime) für Aktivitäten, die parallel ausgeführt werden.
* Passen Sie die Partitionen an der Quelle und Senke entsprechend an. 
* Sehen Sie unter [Datenflussoptimierungen](concepts-data-flow-performance.md) nach.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-data-factory.html)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
