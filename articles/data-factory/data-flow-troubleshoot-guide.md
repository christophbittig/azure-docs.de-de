---
title: Problembehandlung bei Zuordnungsdatenflüssen
description: Hier erfahren Sie, wie Sie in Azure Data Factory Datenflussprobleme beheben können.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: f41d53207eb00e4044e523e481de444a615a5d88
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389397"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Problembehandlung bei Zuordnungsdatenflüssen in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden die gängigen Problembehandlungsmethoden für Zuordnungsdatenflüsse in Azure Data Factory beschrieben.  Informationen zu bestimmten Fehlermeldungen und den damit verbundenen Ursachen sowie Empfehlungen finden Sie unter [Häufig auftretende Datenflussfehlercodes und -meldungen](data-flow-troubleshoot-errors.md).

## <a name="miscellaneous-troubleshooting-tips"></a>Verschiedene Tipps zur Problembehandlung
- **Problem**: Unerwartete Ausnahme und Fehler bei der Ausführung.
    - **Meldung**: Während der Ausführung der Datenflussaktivität: Unerwartete Ausnahme, Fehler bei der Ausführung.
    - **Ursache:** Dies ist ein Fehler beim Back-End-Dienst. Wiederholen Sie den Vorgang, und starten Sie Ihre Debugsitzung neu.
    - **Empfehlung**: Wenn das Problem durch Wiederholung und Neustart nicht behoben werden kann, wenden Sie sich an den Kundensupport.

-  **Problem**: Während der Debugdatenvorschau gibt es keine Ausgabedaten für den Join.
    - **Meldung**: There are a high number of null values or missing values which may be caused by having too few rows sampled. Try updating the debug row limit and refreshing the data. (Es ist eine hohe Anzahl von NULL-Werten oder fehlenden Werten vorhanden. Möglicherweise wurden von zu wenigen Zeilen Stichproben genommen. Aktualisieren Sie das Debugzeilenlimit, und aktualisieren Sie die Daten.)
    - **Ursache**: Die Joinbedingung stimmte entweder mit irgendwelchen Zeilen nicht überein oder führte während der Datenvorschau zu einer großen Anzahl von NULL-Werten.
    - **Empfehlung**: Erhöhen Sie in **Debugeinstellungen** die maximale Anzahl der Quellzeilen. Sie müssen eine Azure IR mit einem Datenflusscluster auswählen, der groß genug ist, um mehr Daten verarbeiten zu können.
    
- **Problem:** Überprüfungsfehler auf Quelle mit mehrzeiligen CSV-Dateien. 
    - **Meldung**: Möglicherweise wird eine der folgenden Fehlermeldungen angezeigt:
        - Die letzte Spalte ist NULL oder fehlt.
        - Fehler bei der Schemaüberprüfung auf der Quelle.
        - Der Schemaimport kann auf der Benutzeroberfläche nicht richtig angezeigt werden, und der Name der letzten Spalte enthält ein Neue-Zeile-Zeichen.
    - **Ursache**: Im Zuordnungsdatenfluss funktionieren mehrzeilige CSV-Quelldateien zurzeit nicht, wenn „\r\n“ als Zeilentrennzeichen verwendet wird. Manchmal können zusätzliche Zeilen bei Wagenrückläufen Fehler verursachen. 
    - **Empfehlung**: Generieren Sie die Datei auf der Quelle mit „\n“ (statt „\r\n“) als Zeilentrennzeichen. Oder konvertieren Sie die CSV-Datei mithilfe der Copy-Aktivität so, dass „\n“ als Zeilentrennzeichen verwendet wird.

## <a name="general-troubleshooting-guidance"></a>Allgemeine Anleitungen zur Problembehandlung
1. Überprüfen Sie den Status der Datasetverbindungen. Wechseln Sie in jeder einzelnen Quell-und Senkentransformation zum verknüpften Dienst für jedes verwendete Dataset, und testen Sie die Verbindungen.
2. Überprüfen Sie im Datenfluss-Designer den Status Ihrer Datei- und Tabellenverbindungen. Wählen Sie im Debugmodus in Ihren Quelltransformationen **Datenvorschau** aus, um sicherzustellen, dass Sie auf Ihre Daten zugreifen können.
3. Wenn in der Datenvorschau keine Probleme erkennbar sind, wechseln Sie zum Pipeline-Designer, und fügen Sie Ihren Datenfluss in eine Pipelineaktivität ein. Debuggen Sie die Pipeline in einem End-to-End-Test.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>Verbesserung im CSV/CDM-Format im Datenfluss 

Wenn Sie die Formatierung **Delimited Text oder CDM für das Mapping des Datenflusses in Azure Data Factory V2** verwenden, kann es aufgrund der Verbesserung für Delimited Text/CDM im Datenfluss ab dem **1. Mai 2021** zu Verhaltensänderungen in Ihren bestehenden Pipelines kommen. 

Die folgenden Probleme können vor der Verbesserung auftreten, die Probleme wurden jedoch nach der Verbesserung behoben. Lesen Sie den folgenden Inhalt, um zu bestimmen, ob sich diese Verbesserung auf Sie auswirkt. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>Szenario 1: Problem mit dem unerwarteten Zeilentrennzeichen

 Sie sind betroffen, wenn Sie sich in den folgenden Umständen befinden:
 - Verwenden Sie den durch Trennzeichen getrennten Text, bei dem die mehrzeilige Einstellung auf True oder CDM als Quelle eingestellt ist.
 - Die erste Zeile enthält über 128 Zeichen. 
 - Das Zeilentrennzeichen in Datendateien ist nicht `\n`.

 Vor der Verbesserung kann das Standardzeilen Trennzeichen `\n` unerwartet verwendet werden, um durch Trennzeichen getrennte Textdateien zu analysieren, denn wenn die mehrzeilige Einstellung auf True festgelegt ist, wird die Zeilentrennzeichen-Einstellung ungültig, und das Zeilentrennzeichen wird automatisch basierend auf den ersten 128 Zeichen erkannt. Wenn Sie das tatsächliche Zeilentrennzeichen nicht ermitteln können, greift es auf `\n` zurück.  

 Nach der Verbesserung sollte eines der drei Zeilentrennzeichen `\r`, `\n`, `\r\n` funktioniert haben.
 
 Das folgende Beispiel zeigt Ihnen eine Pipeline-Verhaltensänderung nach der Verbesserung an:

 **Beispiel**:<br/>
   Für die folgende Spalte:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   Vor der Verbesserung wird `\r` im Spaltenwert beibehalten. Das Ergebnis der geparsten Spalte ist:<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   Nach der Verbesserung sollte das Ergebnis der geparsten Spalte sein:<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>Szenario 2: Auftreten des Problems des fehlerhaften Lesens von Spaltenwerten mit '\r\n'

 Sie sind betroffen, wenn Sie sich in den folgenden Umständen befinden:
 - Verwendung von Begrenztem Text mit der Mehrzeilen-Einstellung auf True oder CDM als Quelle. 
 - Das Zeilentrennzeichen ist `\r\n`.

 Vor der Verbesserung wurde beim Lesen des Spaltenwerts das darin enthaltene `\r\n` möglicherweise fälschlicherweise durch `\n`ersetzt. 

 Nach der Verbesserung, wird das `\r\n` im Spaltenwert nicht durch `\n`ersetzt.

 Das folgende Beispiel zeigt Ihnen eine Pipeline-Verhaltensänderung nach der Verbesserung an:
 
 **Beispiel**:<br/>
  
 Für die folgende Spalte：<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 Vor der Verbesserung ist das Ergebnis der geparsten Spalte:<br/>
  **`A\n`**` B C`<br/>

 Nach der Verbesserung sollte das Ergebnis der geparsten Spalte sein:<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>Szenario 3: Auftreten des Problems des fehlerhaften Lesens von Spaltenwerten mit '\r\n'

 Sie sind betroffen, wenn Sie sich in den folgenden Umständen befinden:
 - Verwenden des durch Trennzeichen getrennten Texts als Senke.
 - Der Spaltenwert enthält `\n`.
 - Das Zeilentrennzeichen ist auf `\r\n` festgelegt.
 
 Vor der Verbesserung wurde beim Lesen des Spaltenwerts das darin enthaltene `\n` möglicherweise fälschlicherweise durch `\r\n`ersetzt. 

 Nach der Verbesserung, wird das `\n` im Spaltenwert nicht durch `\r\n`ersetzt.
 
 Das folgende Beispiel zeigt Ihnen eine Pipeline-Verhaltensänderung nach der Verbesserung an:

 **Beispiel**:<br/>

 Für die folgende Spalte:<br/>
 **`A\n`**` B C`<br/>

 Vor der Verbesserung ist die CSV-Senke wie folgt:<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 Nach der Verbesserung sollte die CSV-Senke wie folgt sein:<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>Szenario 4: Problem beim nicht ordnungsgemäßem Lesen einer leeren Zeichenfolge als NULL
 
 Sie sind betroffen, wenn Sie sich in den folgenden Umständen befinden:
 - Verwenden des durch Trennzeichen getrennten Texts als Senke. 
 - Der NULL-Wert ist auf einen nicht leeren Wert festgelegt. 
 - Der Spaltenwert ist eine leere Zeichenfolge und ist nicht in Anführungszeichen eingeschlossen. 
 
 Vor der Verbesserung wird der Spaltenwert der leeren Zeichenfolge ohne Anführungszeichen als Null gelesen. 

 Nach der Verbesserung wird eine leere Zeichenfolge nicht als NULL-Wert analysiert. 
 
 Das folgende Beispiel zeigt Ihnen eine Pipeline-Verhaltensänderung nach der Verbesserung an:

 **Beispiel**:<br/>

 Für die folgende Spalte:<br/>
  `A, ,B, `<br/>

 Vor der Verbesserung ist das Ergebnis der geparsten Spalte:<br/>
  `A null B null`<br/>

 Nach der Verbesserung sollte das Ergebnis der geparsten Spalte sein:<br/>
  `A "" (empty string) B "" (empty string)`<br/>

###  <a name="internal-server-errors"></a>Interne Serverfehler

Im Folgenden finden Sie spezifische Szenarien, die interne Serverfehler verursachen können.

#### <a name="scenario-1-not-choosing-the-appropriate-compute-sizetype-and-other-factors"></a>Szenario 1: Es wurde eine ungeeignete Auswahl bzgl. Computegröße/-typ und anderer Faktoren getroffen

  Die erfolgreiche Ausführung eines Datenflusses hängt von vielen Faktoren ab. Hierzu zählen unter anderem die/der Computegröße/-typ, die Anzahl der zu verarbeitenden Quellen/Senken, die Partitionsspezifikation, die erforderlichen Transformationen,die Größe von Datasets sowie die Datenschiefe.<br/>
  
  Weitere Anleitungen finden Sie unter [Leistung der Integration Runtime](concepts-integration-runtime-performance.md).

#### <a name="scenario-2-using-debug-sessions-with-parallel-activities"></a>Szenario 2: Verwenden von Debugsitzungen mit parallelen Aktivitäten

  Wenn eine Ausführung mithilfe einer Datenflussdebugsitzung mit Konstrukten wie „ForEach“ in der Pipeline ausgelöst wird, können mehrere parallele Ausführungen an den gleichen Cluster übermittelt werden. Diese Situation kann zu Clusterfehlern führen, z. B. weil nicht genügend Arbeitsspeicher zur Verfügung steht oder ein anderes Ressourcenproblemen vorliegt.<br/>
  
  Um eine Ausführung mit der entsprechenden in der Pipeline-Aktivität definierten Integration Runtime-Konfiguration zu übermitteln, wählen Sie nach der Veröffentlichung der Änderungen **Jetzt auslösen** oder **Debuggen** >  **Aktivitätslaufzeit verwenden**.

#### <a name="scenario-3-transient-issues"></a>Szenario 3: Vorübergehende Probleme

  Vorübergehende Probleme mit an der Ausführung beteiligten Microservices können dazu führen, dass die Ausführung nicht erfolgreich ist.<br/>
  
  Schwierigkeiten infolge von vorübergehenden Problemen lassen sich ggf. durch das Konfigurieren von Wiederholungen in der Pipelineaktivität beheben. Sie finden weitere Anleitungen in der Dokumentation zur [Aktivitätsrichtlinie](concepts-pipelines-activities.md#activity-json).

## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in diesen Ressourcen:

- [Häufig auftretende Datenflussfehlercodes und -meldungen](data-flow-troubleshoot-errors.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
