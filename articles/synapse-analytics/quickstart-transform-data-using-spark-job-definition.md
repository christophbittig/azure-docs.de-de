---
title: 'Schnellstart: Transformieren von Daten mithilfe einer Apache Spark-Auftragsdefinition'
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie Azure Synapse Analytics verwenden, um Daten mit einer Apache Spark-Auftragsdefinition zu transformieren.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2021
ms.openlocfilehash: 2b7392af7a00ec68ad263b7c930842714c94fe1d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967713"
---
# <a name="quickstart-transform-data-using-apache-spark-job-definition"></a>Schnellstart: Transformieren von Daten mithilfe einer Apache Spark-Auftragsdefinition

In dieser Schnellstartanleitung verwenden Sie Azure Synapse Analytics, um eine Pipeline mithilfe einer Apache Spark-Auftragsdefinition zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Synapse-Arbeitsbereich**: Befolgen Sie zum Erstellen eines Synapse-Arbeitsbereichs mithilfe des Azure-Portals die Anweisungen unter [Schnellstart: Erstellen eines Synapse-Arbeitsbereichs](quickstart-create-workspace.md).
* **Apache Spark-Auftragsdefinition:** Erstellen Sie eine Apache Spark-Auftragsdefinition im Synapse-Arbeitsbereich nach den Anweisungen unter [Tutorial: Erstellen einer Apache Spark-Auftragsdefinition in Synapse Studio](spark/apache-spark-job-definitions.md).


### <a name="navigate-to-the-synapse-studio"></a>Navigieren zu Synapse Studio

Nachdem Ihr Azure Synapse-Arbeitsbereich erstellt wurde, haben Sie zwei Möglichkeiten zum Öffnen von Synapse Studio:

* Öffnen Sie Ihren Synapse-Arbeitsbereich im [Azure-Portal](https://ms.portal.azure.com/#home). Wählen Sie unter „Erste Schritte“ auf der Karte „Synapse Studio öffnen“ die Option **Öffnen** aus.
* Öffnen Sie [Azure Synapse Analytics](https://web.azuresynapse.net/), und melden Sie sich bei Ihrem Arbeitsbereich an.

In dieser Schnellstartanleitung wird der Arbeitsbereich „sampletest“ als Beispiel verwendet. Sie werden automatisch zur Startseite von Synapse Studio weitergeleitet.

![Synapse Studio-Startseite](media/quickstart-transform-data-using-spark-job-definition/synapse-studio-home.png)

## <a name="create-a-pipeline-with-an-apache-spark-job-definition"></a>Erstellen einer Pipeline mit einer Apache Spark-Auftragsdefinition

Eine Pipeline enthält den logischen Ablauf für die Ausführung einer Aktivitätenmenge. In diesem Abschnitt erstellen Sie eine Pipeline, die eine Apache Spark-Auftragsdefinitionsaktivität enthält.

1. Wechseln Sie zur Registerkarte **Integrieren**. Wählen Sie neben dem Header „Pipelines“ das Pluszeichen und anschließend **Pipeline** aus.

     ![Erstellen einer neuen Pipeline](media/doc-common-process/new-pipeline.png)

2. Geben Sie auf der Einstellungsseite **Eigenschaften** der Pipeline unter **Name** die Zeichenfolge **demo** ein.

3. Ziehen Sie unter **Synapse** im Bereich **Aktivitäten** die **Spark-Auftragsdefinition** auf den Pipelinecanvas.

     ![Ziehen der Spark-Auftragsdefinition](media/quickstart-transform-data-using-spark-job-definition/drag-spark-job-definition.png)


## <a name="set-apache-spark-job-definition-canvas"></a>Einrichten des Apache Spark-Auftragsdefinitionscanvas

Nachdem Sie Ihre Apache Spark-Auftragsdefinition erstellt haben, gelangen Sie automatisch zum Spark-Auftragsdefinitionscanvas.

### <a name="general-settings"></a>Allgemeine Einstellungen

1. Wählen Sie auf dem Canvas das Modul „Spark-Auftragsdefinition“ aus.

2. Geben Sie auf der Registerkarte **Allgemein** für **Name** **sample** ein.

3. Optional können Sie auch eine Beschreibung angeben.

4. Timeout: Der maximale Zeitraum für das Ausführen einer Aktivität. Als Standardwert ist der maximal zulässige Zeitrum von sieben Tagen eingestellt. Das Format ist „D.HH:MM:SS“.

5. Wiederholung: Die maximale Anzahl der Wiederholungsversuche.

6. Wiederholungsintervall: Die Anzahl von Sekunden zwischen den einzelnen Wiederholungsversuchen.

7. Sichere Ausgabe: Wenn diese Option aktiviert ist, werden Ausgaben der Aktivität nicht bei der Protokollierung erfasst.

8. Sichere Eingabe: Wenn diese Option aktiviert ist, werden Eingaben der Aktivität nicht bei der Protokollierung erfasst.

     ![Allgemeines zur Spark-Auftragsdefinition](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-general.png)

### <a name="settings-tab"></a>Registerkarte "Einstellungen" 

In diesem Panel können Sie auf die auszuführende Spark-Auftragsdefinition verweisen.

* Erweitern Sie die Liste der Spark-Auftragsdefinition. Sie können eine vorhandene Apache Spark-Auftragsdefinition auswählen. Sie können auch eine neue Apache Spark-Auftragsdefinition erstellen, indem Sie die Schaltfläche **Neu** auswählen, um auf die auszuführende Spark-Auftragsdefinition zu verweisen.

* Sie können Befehlszeilenargumente hinzufügen, indem Sie auf die Schaltfläche **Neu** klicken. Beachten Sie, dass durch das Hinzufügen von Befehlszeilenargumenten die Befehlszeilenargumente außer Kraft gesetzt werden, die durch die Spark-Auftragsdefinition definiert sind. <br> *Beispiel: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result`* <br>

     ![Pipelineeinstellungen für Spark-Auftragsdefinitionen](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-pipline-settings.png)

* Sie können dynamische Inhalte hinzufügen, indem Sie auf die Schaltfläche **Dynamischen Inhalt hinzufügen** klicken oder die Tastenkombination <kbd>Alt</kbd>+<kbd>Umschalt</kbd>+<kbd>D</kbd> drücken. Auf der **Dynamischen Inhalt hinzufügen**-Seite können Sie eine beliebige Kombination aus Ausdrücken, Funktionen und Systemvariablen verwenden, um dynamische Inhalte hinzuzufügen.

     ![Dynamischen Inhalt hinzufügen](media/quickstart-transform-data-using-spark-job-definition/add-dynamic-content.png)

### <a name="user-properties-tab"></a>Benutzereigenschaften-Tab

In diesem Bereich können Sie Eigenschaften für Apache Spark-Auftragsdefinitionsaktivitäten hinzufügen.

![Benutzereigenschaften](media/quickstart-transform-data-using-spark-job-definition/user-properties.png)

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie Informationen zur Unterstützung von Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Pipelines und Aktivitäten in Azure Data Factory](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [Zuordnungsdatenflüsse in Azure Data Factory](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [Datentransformationsausdrücke in Mapping Data Flow](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
