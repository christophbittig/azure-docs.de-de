---
title: Bringen Sie Ihre eigene ML in Microsoft Sentinel | Microsoft Docs
description: In diesem Artikel wird erklärt, wie Sie Ihre eigenen Algorithmen für maschinelles Lernen zur Datenanalyse in Microsoft Sentinel erstellen und verwenden können.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 815b1b640fad26587d354a306d4bfb4188cef053
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524643"
---
# <a name="bring-your-own-machine-learning-ml-into-microsoft-sentinel"></a>Bringen Sie Ihr eigenes maschinelles Lernen (ML) in Microsoft Sentinel ein

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Maschinelles Lernen (ML) ist eine der wichtigsten Grundlagen von Microsoft Sentinel und eines der Hauptmerkmale, die es von anderen abheben. Microsoft Sentinel bietet ML in mehreren Versionen: integriert in die [Fusion](fusion.md)-Korrelations-Engine und Jupyter-Notebooks sowie die neu verfügbare Build-Your-Own ML (BYO ML)-Plattform. 

ML-Erkennungsmodelle können sich an einzelne Umgebungen und Änderungen im Benutzerverhalten anpassen, um [falsch positive Ergebnisse](false-positives.md) zu verringern und Bedrohungen zu identifizieren, die mit einem herkömmlichen Ansatz nicht gefunden würden. Viele Sicherheitsorganisationen verstehen den Wert von ML für die Sicherheit, obgleich nicht viele von Ihnen über den Luxus von Fachleuten verfügen, die Fachkenntnisse sowohl im Bereich Sicherheit als auch des ML besitzen. Wir haben das hier vorgestellte Framework für Sicherheitsorganisationen und Fachkräfte entwickelt, damit sie gemeinsam mit uns auf ihrer ML Journey wachsen können. Organisationen, die mit ML noch nicht vertraut sind oder nicht über das erforderliche Fachwissen verfügen, können aus den integrierten ML-Funktionen von Microsoft Sentinel einen erheblichen Schutzwert ziehen.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="Machine Learning-Framework":::


## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Was ist die Bring Your Own Machine Learning-Plattform (BYO-ML)?

Organisationen, die über ML-Ressourcen verfügen und angepasste ML-Modelle für Ihre individuellen geschäftlichen Anforderungen erstellen möchten, bieten wir die **BYO-ML-Plattform**. Die Plattform nutzt die [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)/[Apache Spark](http://spark.apache.org/)-Umgebung sowie Jupyter-Notebooks, um die ML-Umgebung zu erzeugen. Sie bietet die folgenden Komponenten:

- Ein BYO-ML-Paket, das Bibliotheken enthält, mit denen Sie auf Daten zugreifen und die Ergebnisse zurück an Log Analytics (LA) übertragen können, sodass Sie die Ergebnisse in Ihre Erkennung, Untersuchung und Suche integrieren können. 

- ML-Algorithmusvorlagen, die Sie so anpassen können, dass sie für bestimmte Sicherheitsprobleme in Ihrer Organisation passen. 

- Beispielnotebooks zum Trainieren des Modells und zum Planen der Modellbewertung. 

Darüber hinaus können Sie Ihre eigenen ML-Modelle und/oder Ihre eigene Spark-Umgebung mitbringen, um sie in Microsoft Sentinel zu integrieren.

Mit der BYO-ML-Plattform können Sie schnell loslegen und Ihre eigenen ML-Modelle entwickeln: 

- Das Notebook mit Beispieldaten hilft Ihnen dabei, praktische End-to-End-Erfahrungen zu sammeln, ohne sich Gedanken über die Verarbeitung von Produktionsdaten machen zu müssen.

- Das in die Spark-Umgebung integrierte Paket verringert die Herausforderungen und Probleme bei der Verwaltung der Infrastruktur.

- Die Bibliotheken unterstützen Datenverschiebungen. Trainings- und Bewertungsnotebooks veranschaulichen die End-to-End-Erfahrung und dienen als Vorlage, die Sie an Ihre Umgebung anpassen können.

### <a name="use-cases"></a>Anwendungsfälle
 
Die BYO-ML-Plattform und das BYO-ML-Paket reduzieren den Zeit- und Arbeitsaufwand für die Erstellung eigener ML-Erkennungen erheblich und bieten die Möglichkeit, spezifische Sicherheitsprobleme in Microsoft Sentinel zu lösen. Die Plattform unterstützt die folgenden Anwendungsfälle:

**Trainieren eines ML-Algorithmus zur Erzeugung eines angepassten Modells:** Sie können einen vorhandenen ML-Algorithmus (von Microsoft oder von der Benutzercommunity geteilt) verwenden und diesen problemlos mit Ihren eigenen Daten trainieren, um ein angepasstes ML-Modell zu erhalten, das besser für Ihre Daten und Ihre Umgebung geeignet ist.

**Ändern einer ML-Algorithmusvorlage zur Erzeugung eines angepassten Modells:** Sie können eine ML-Algorithmusvorlage (von Microsoft oder von der Benutzercommunity geteilt) ändern und den geänderten Algorithmus mit Ihren eigenen Daten trainieren, um ein angepasstes Modell abzuleiten, das an Ihr spezifisches Problem angepasst ist.

**Erstellen Sie Ihr eigenes Modell:** Erstellen Sie Ihr eigenes Modell von Grund auf mit der BYO-ML-Plattform und den Dienstprogrammen von Microsoft Sentinel.

**Integrieren Sie Ihre Databricks/Spark-Umgebung:** Integrieren Sie Ihre bestehende Databricks/Spark-Umgebung in Microsoft Sentinel und verwenden Sie BYO-ML-Bibliotheken und -Vorlagen, um ML-Modelle für ihre speziellen Situationen zu erstellen.

**Importieren Sie Ihr eigenes ML-Modell:** Sie können Ihre eigenen ML-Modelle importieren und die BYO-ML-Plattform und Dienstprogramme verwenden, um sie in Microsoft Sentinel zu integrieren.

**Teilen eines ML-Algorithmus:** Teilen Sie einen ML-Algorithmus, den die Community übernehmen und anpassen kann.

**Verwenden von ML zur Unterstützung von SecOPs:** Verwenden Sie Ihr eigenes benutzerdefiniertes ML-Modell und dessen Ergebnisse für die Suche, Erkennungen, Untersuchungen und Antworten.

Dieser Artikel zeigt Ihnen die Komponenten der BYO-ML-Plattform und wie Sie die Plattform und den Algorithmus für den Zugriff auf anomale Ressourcen nutzen können, um eine benutzerdefinierte ML-Erkennung mit Microsoft Sentinel bereitzustellen.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark-Umgebung

[Apache Spark](http://spark.apache.org/) hat bei der Vereinfachung von Big Data einen großen Schritt nach vorne gemacht, indem es ein einheitliches Framework für den Aufbau von Datenpipelines bereitstellt. Azure Databricks geht noch darüber hinaus, indem es eine um Spark herum entwickelte, verwaltungsfreie Cloudplattform bereitstellt. Wir empfehlen, dass Sie Databricks für Ihre BYO-ML-Plattform verwenden, damit Sie sich auf das Finden von Antworten konzentrieren können, die unmittelbare Auswirkungen auf Ihr Unternehmen haben, anstatt Probleme mit Datenpipelines und Plattformen zu lösen.

Wenn Sie bereits über Databricks oder eine andere Spark-Umgebung verfügen und lieber die vorhandene Einrichtung verwenden möchten, funktioniert das BYO-ML-Paket auch damit ebenfalls problemlos. 

## <a name="byo-ml-package"></a>BYO-ML-Paket

Das BYO-ML-Paket integriert bewährte Methoden und Forschung von Microsoft in das ML-Front-End für Sicherheit. In diesem Paket stellen wir die folgende Liste von Hilfsprogrammen, Notebooks und Algorithmusvorlagen für Sicherheitsprobleme bereit.

| Dateiname | BESCHREIBUNG |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Enthält Hilfsprogramme zum Lesen von Blobs aus Azure und zum Schreiben in Log Analytics. |
| AnomalousRASampleData | Notebook demonstriert die Verwendung des Modells Anomalous Resource Access in Microsoft Sentinel mit generierten Trainings- und Test-Beispieldaten. |
| AnomalousRATraining.ipynb | Ein Notebook zum Trainieren des Algorithmus, zum Erstellen und Speichern der Modelle. |
| AnomalousRAScoring.ipynb | Notebook, um die Ausführung des Modells zu planen, das Ergebnis zu visualisieren und die Ergebnisse an Microsoft Sentinel zurückzuschreiben. |
|

Die erste ML-Algorithmusvorlage, die wir anbieten, ist für die [Erkennung anomalen Ressourcenzugriffs](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML). Sie basiert auf einem kollaborativen Filteralgorithmus und wird mit Protokollen des Zugriffs auf Windows-Dateifreigaben trainiert (Sicherheitsereignisse mit Ereignis-ID 5140). Die Schlüsselinformationen, die Sie für dieses Modell im Protokoll benötigen, ist die Kopplung von Benutzern mit Ressourcen, auf die zugegriffen wird. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Exemplarische Vorgehensweise: Erkennung anomaler Zugriffe auf Dateifreigaben 

Nachdem Sie nun mit den wichtigsten Komponenten der BYO-ML-Plattform vertraut sind, finden Sie hier ein Beispiel, das zeigt, wie Sie die Plattform und die Komponenten verwenden, um eine angepasste ML-Erkennung bereitzustellen.

### <a name="setup-the-databricksspark-environment"></a>Einrichten der Databricks/Spark-Umgebung

Sie müssen Ihre eigene Databricks-Umgebung einrichten, wenn Sie noch keine haben. Anweisungen hierzu finden Sie in dem Dokument [Databrick-Schnellstart](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal).

### <a name="auto-export-instruction"></a>Anweisung zum automatischen Export

Um benutzerdefinierte ML-Modelle auf der Grundlage Ihrer eigenen Daten in Microsoft Sentinel zu erstellen, müssen Sie Ihre Daten aus Log Analytics in einen Blob-Speicher oder eine Event-Hub-Ressource exportieren, damit das ML-Modell von Databricks aus darauf zugreifen kann. Erfahren Sie, wie man [Daten in Microsoft Sentinel](connect-data-sources.md) einspeist.

In diesem Beispiel müssen sich Ihre Trainingsdaten für das Dateifreigabe-Zugriffsprotokoll im Azure Blob-Speicher befinden. Das Format der Daten ist im Notebook und in den Bibliotheken dokumentiert.

Sie können Ihre Daten mithilfe der [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/monitor/log-analytics) automatisch aus Log Analytics exportieren. 

Ihnen muss die Rolle **Mitwirkender** in Ihrem Log Analytics-Arbeitsbereich, Ihrem Speicherkonto und Ihrer EventHub-Ressource zugewiesen sein, um die Befehle ausführen zu können. 

Im Folgenden sehen Sie ein Beispielset von Befehlen zum Einrichten des automatischen Exports:

```azurecli

az –version

# Login with Azure CLI
az login

# List all Log Analytics clusters
az monitor log-analytics cluster list

# Set to specific subscription
az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Exportieren benutzerdefinierter Daten

Für benutzerdefinierte Daten, die vom automatischen Export von Log Analytics nicht unterstützt werden, können Sie Logic Apps oder andere Lösungen verwenden, um Ihre Daten zu verschieben. Weitere Informationen finden Sie im Blog und Skript zu [Exportieren von Log Analytics-Daten in Blob-Speicher](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530).

### <a name="correlate-with-data-outside-of-microsoft-sentinel"></a>Korrelieren Sie mit Daten außerhalb von Microsoft Sentinel

Sie können auch Daten von außerhalb von Microsoft Sentinel in den Blob-Speicher oder den Event Hub bringen und sie mit den Microsoft Sentinel-Daten korrelieren, um Ihre ML-Modelle zu erstellen.

### <a name="copy-and-install-the-related-packages"></a>Kopieren und Installieren der relevanten Pakete

Kopieren Sie das BYO-ML-Paket aus dem bereits erwähnten Microsoft Sentinel GitHub-Repository in Ihre Databricks-Umgebung. Öffnen Sie dann die Notebooks, und befolgen Sie die Anweisungen im jeweiligen Notebook, um die erforderlichen Bibliotheken in Ihren Clustern zu installieren.

### <a name="model-training-and-scoring"></a>Training und Bewertung von Modellen

Befolgen Sie die Anweisungen in den beiden Notebooks, um die Konfigurationen gemäß Ihrer Umgebung und Ihrer Ressourcen zu ändern, führen Sie die Schritte zum Trainieren und Erstellen Ihres Modells aus, und planen Sie dann das Modell, um eingehende Dateifreigabe-Zugriffsprotokolle zu bewerten.

### <a name="write-results-to-log-analytics"></a>Schreiben von Ergebnissen in Log Analytics

Sobald Sie die Auswertung geplant haben, können Sie das Modul im Auswertungsnotizbuch verwenden, um die Auswertungsergebnisse in den mit Ihrer Microsoft Sentinel-Instanz verbundenen Log Analytics-Arbeitsbereich zu schreiben.

### <a name="check-results-in-microsoft-sentinel"></a>Ergebnisse in Microsoft Sentinel überprüfen

Um Ihre Ergebnisse zusammen mit den zugehörigen Protokolldetails zu sehen, gehen Sie zurück zu Ihrem Microsoft Sentinel Portal. In „**Protokolle** > Benutzerdefinierte Protokolle“ werden die Ergebnisse in der Tabelle **AnomalousResourceAccessResult_CL** (oder dem Namen Ihrer eigenen benutzerdefinierten Tabelle) angezeigt. Sie können diese Ergebnisse verwenden, um Ihre Untersuchungs- und Sucherfahrungen zu verbessern.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="Protokolle über anomale Ressourcenzugriffe":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Erstellen benutzerdefinierter Analyseregeln mit ML-Ergebnissen

Nachdem Sie bestätigt haben, dass sich die ML-Ergebnisse in der Tabelle „Benutzerdefinierte Protokolle“ befinden und Sie mit der Genauigkeit der Bewertungen zufrieden sind, können Sie auf Grundlage der Ergebnisse eine Erkennung erstellen. Gehen Sie im Microsoft Sentinel-Portal zu **Analytics** und [erstellen Sie eine neue Erkennungsregel](detect-threats-custom.md). Im Folgenden finden Sie ein Beispiel für die Abfrage, die zum Erstellen der Erkennung verwendet wird.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="Erstellen einer benutzerdefinierten Analyseregel für B Y O M L-Erkennungen":::

### <a name="view-and-respond-to-incidents"></a>Anzeigen von und Reagieren auf Vorfälle
Wenn Sie die Analyseregel auf der Grundlage der ML-Ergebnisse eingerichtet haben und die Ergebnisse über dem von Ihnen in der Abfrage festgelegten Schwellenwert liegen, wird ein Vorfall generiert und auf der Seite **Vorfälle** in Microsoft Sentinel angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie mit der BYO-ML-Plattform von Microsoft Sentinel Ihre eigenen Algorithmen für maschinelles Lernen erstellen oder importieren können, um Daten zu analysieren und Bedrohungen zu erkennen.

- Beiträge über maschinelles Lernen und viele andere relevante Themen finden Sie im [Microsoft Sentinel Blog](https://aka.ms/azuresentinelblog).
