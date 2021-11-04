---
title: Verwenden von Batchendpunkten in Studio
titleSuffix: Azure Machine Learning
description: In diesem Artikel lernen Sie, wie Sie einen Batchendpunkt in Azure Machine Learning Studio erstellen. Batchendpunkte werden verwendet, um fortlaufend ein Batch-Bewertung großer Datenmengen zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.custom: how-to, studio, managed-batch-endpoints
ms.openlocfilehash: d9d6287903466283e0b94e246a17a75a77929c8a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560585"
---
# <a name="how-to-use-batch-endpoints-preview-in-azure-machine-learning-studio"></a>Verwenden von Batchendpunkten (Vorschauversion) in Azure Machine Learning Studio

In diesem Artikel erfahren Sie, wie Sie Batchendpunkte (Vorschau) für die Batch-Bewertung in [Azure Machine Learning Studio](https://ml.azure.com) verwenden. Weitere Informationen finden Sie unter [Was sind Azure Machine Learning-Endpunkte (Vorschau)?](concept-endpoints.md).

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Batchendpunkts für das MLflow-Modell ohne Programmieraufwand
> * Überprüfen von Batchendpunktdetails
> * Starten eines Batchbewertungsauftrags
> * Übersicht über Batchendpunktfunktionen in Azure Machine Learning Studio

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement: Sollten Sie über kein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Das Beispielrepository: Klonen Sie das [AzureML-Beispielrepository](https://github.com/Azure/azureml-examples). In diesem Artikel werden die Ressourcen in `/cli/endpoints/batch` verwendet.

* Ein Computeziel, auf dem Sie Batchbewertungsworkflows ausführen können. Weitere Informationen zum Erstellen eines Computeziels finden Sie unter [Erstellen von Computezielen in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md).

* Registrieren eines Machine Learning-Modells.

## <a name="create-a-batch-endpoint"></a>Erstellen eines Batchendpunkts

Es gibt zwei Möglichkeiten, Batch-Endpunkte in Azure Machine Learning Studio zu erstellen:

* Wählen Sie auf der Seite **Endpoints** **Batch Endpoints** und wählen Sie dann **+ Erstellen**. 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-endpoints.png" alt-text="Screenshot von Batch-Endpunkt-Erstellung/Bereitstellung auf der Seite „Endpunkte“":::

oder

* Wählen Sie auf der Seite **Modelle** das Modell aus, das Sie bereitstellen möchten, und wählen Sie dann **Bereitstellen an Batch-Endpunkt (Vorschau)** .

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/models-page-deployment.png" alt-text="Screenshot von Batch-Endpunkt-Erstellung/Bereitstellung auf der Seite „Modelle“":::

> [!TIP]
> Bei Verwendung eines MLflow-Modells können Sie die Batchendpunkterstellung ohne Code durchführen. Das bedeutet, Sie müssen kein Bewertungsskript und keine Umgebung vorbereiten. Beides kann automatisch generiert werden. Weitere Informationen finden Sie unter [Trainieren und Nachverfolgen von ML-Modellen mit MLflow und Azure Machine Learning (Vorschauversion)](how-to-use-mlflow.md).
> 
> :::image type="content" source="media/how-to-use-batch-endpoints-studio/mlflow-model-wizard.png" alt-text="Screenshot von Bereitstellung eines MLflow-Modells":::

Führen Sie alle Schritte in dem Assistenten aus, um einen Batch-Endpunkt zu erstellen und bereitzustellen.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/review-batch-wizard.png" alt-text="Screenshot des Bildschirms für die Bewertung von Batch-Endpunkten/Bereitstellung":::

## <a name="check-batch-endpoint-details"></a>Überprüfen von Batchendpunktdetails

Nachdem ein Batch-Endpunkt erstellt wurde, wählen Sie ihn auf der Seite **Endpunkte** aus, um die Details anzuzeigen.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/batch-endpoint-details.png" alt-text="Screenshot des Bildschirms zur Bewertung von Batch-Endpunkten und Bereitstellungsdetails":::

## <a name="start-a-batch-scoring-job"></a>Starten eines Batchbewertungsauftrags

Eine Batchbewertungsworkload wird als Offlineauftrag ausgeführt. Die Bewertungsausgaben der Batchbewertung werden standardmäßig in Blobspeicher gespeichert. Sie können auch den Ausgabespeicherort konfigurieren und einige der Einstellungen überschreiben, um die bestmögliche Leistung zu erzielen.

1. Klicken Sie auf **+ Auftrag erstellen**:

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-job.png" alt-text="Screenshot der Option &quot;Auftrag erstellen&quot; zum Starten der Batchbewertung":::

1. Sie können die standardmäßige Bereitstellung aktualisieren, während Sie einen Auftrag von der Dropdownliste übermitteln:

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/job-setting-batch-scoring.png" alt-text="Screenshot der Verwendung der Bereitstellung zum Übermitteln eines Batchauftrags":::

### <a name="overwrite-settings"></a>Überschreiben von Einstellungen

Einige Einstellungen können überschrieben werden, wenn Sie einen Batchbewertungsauftrag starten. So können Sie beispielsweise Einstellungen überschreiben, um die Computeressourcen besser zu nutzen oder die Leistung zu verbessern. Um Einstellungen zu überschreiben, wählen Sie __Bereitstellungseinstellungen überschreiben__ und geben Sie die Einstellungen an. Weitere Informationen finden Sie unter [Verwenden von Batch-Endpunkten](how-to-use-batch-endpoint.md#configure-the-output-location-and-overwrite-settings).

:::image type="content" source="media/how-to-use-batch-endpoints-studio/overwrite-setting.png" alt-text="Screenshot von der Überschreibungseinstellung beim Starten eines Batchauftrags":::

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>Starten eines Batchbewertungsauftrags mit verschiedenen Eingabeoptionen

Sie haben zwei Optionen, um die Dateneingaben in Azure Computer Learning Studio festzulegen:

* Verwenden eines **registrierten Datensatzes**:

    > [!NOTE]
    > Während der Vorschauphase wird nur „FileDataset“ unterstützt. 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-dataset-for-job.png" alt-text="Screenshot von der Auswahl eines registrierten Datensatzes als Eingabeoption":::

oder

* **Verwenden eines Datenspeichers**:

    Sie können einen registrierten AML-Datenspeicher angeben oder, wenn Ihre Daten öffentlich zugänglich sind, den öffentlichen Pfad angeben.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-datastore-job.png" alt-text="Screenshot der Auswahl von Datenspeicher als Eingabeoption":::

### <a name="configure-the-output-location"></a>Konfigurieren des Ausgabespeicherorts

Standardmäßig werden die Batchbewertungsergebnisse im Standardmäßigen Blobspeicher für den Arbeitsbereich gespeichert. Die Ergebnisse befinden sich in einem Ordner, der nach dem Auftragsnamen (einer vom System generierten GUID) benannt ist.

Um zu ändern, wo die Ergebnisse gespeichert werden, geben Sie einen Blobspeicher und einen Ausgabepfad an, wenn Sie den Auftrag starten.

> [!IMPORTANT]
> Der Ausgabespeicherort muss eindeutig sein. Ist die Ausgabedatei bereits vorhanden, tritt für den Batchbewertungsauftrag ein Fehler auf. 

:::image type="content" source="media/how-to-use-batch-endpoints-studio/configure-output-location.png" alt-text="Screenshot von der optionalen Konfiguration des Ausgabeorts":::

### <a name="summary-of-all-submitted-jobs"></a>Übersicht über alle übermittelten Aufträge

Um eine Zusammenfassung aller übermittelten Aufträge für einen Endpunkt anzuzeigen, wählen Sie den Endpunkt und dann die Registerkarte **Ausführungen**.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/summary-jobs.png" alt-text="Screenshot von der Zusammenfassung der an einen Batch-Endpunkt übermittelten Aufträge":::
## <a name="check-batch-scoring-results"></a>Überprüfen der Ergebnisse der Batchbewertung

Wie Sie die Ergebnisse der Bewertung anzeigen können, lernen Sie unter [Verwendung von Batch-Endpunkten](how-to-use-batch-endpoint.md#check-batch-scoring-results).

## <a name="add-a-deployment-to-an-existing-batch-endpoint"></a>Hinzufügen einer Bereitstellung zu einem vorhandenen Batch-Endpunkt

In Azure Machine Learning Studio gibt es zwei Möglichkeiten, eine Bereitstellung zu einem vorhandenen Batch-Endpunkt hinzuzufügen:

* Wählen Sie auf der Seite **Endpunkte** den Batch-Endpunkt aus, dem Sie eine neue Bereitstellung hinzufügen möchten. Wählen Sie **+ Bereitstellung hinzufügen** und schließen Sie den Assistenten zum Hinzufügen einer neuen Bereitstellung ab.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-option.png" alt-text="Screenshot der Option zum Hinzufügen einer neuen Bereitstellung":::

oder

* Wählen Sie auf der Seite **Modelle** das Modell aus, das Sie bereitstellen möchten. Wählen Sie dann aus der Dropdownliste die Option **Bereitstellung für Batch-Endpunkt (Vorschau)** aus. Wählen Sie im Assistenten auf dem Bildschirm **Endpunkt** die Option **Vorhanden**. Schließen Sie den Assistenten ab, um die neue Bereitstellung hinzuzufügen.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-models-page.png" alt-text="Screenshot von der Auswahl eines vorhandenen Batch-Endpunkts zum Hinzufügen einer neuen Bereitstellung":::

## <a name="update-the-default-deployment"></a>Aktualisieren Sie die standardmäßige Bereitstellung

Wenn ein Endpunkt mehrere Bereitstellungen hat, ist eine der Bereitstellungen die *standardmäßige*. Die standardmäßige Bereitstellung erhält 100 % des Datenverkehrs zum Endpunkt. Um die standardmäßige Bereitstellung zu ändern, führen Sie die folgenden Schritte aus:

1. Wählen Sie den Endpunkt auf der Seite **Endpunkte** aus.
1. Wählen Sie **Standardmäßige Bereitstellung aktualisieren**. Wählen Sie auf der Registerkarte **Details** die Bereitstellung aus, die Sie als standardmäßig festlegen möchten, und wählen Sie dann **Aktualisieren**.
    :::image type="content" source="media/how-to-use-batch-endpoints-studio/update-default-deployment.png" alt-text="Screenshot der Aktualisierung der standardmäßigen Bereitstellung":::

## <a name="delete-batch-endpoint-and-deployments"></a>Löschen von Batch-Endpunkten und Bereitstellungen

Um einen **Endpunkt** zu löschen, wählen Sie den Endpunkt auf der Seite **Endpunkte** aus und wählen Sie dann „Löschen“.

> [!WARNING]
> Mit dem Löschen eines Endpunkts werden auch alle Bereitstellungen für diesen Endpunkt gelöscht.

Um eine **Bereitstellung** zu löschen, wählen Sie den Endpunkt auf der Seite  **Endpunkte** aus, wählen Sie die Bereitstellung aus, und wählen Sie dann „Löschen“.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Batch-Endpunkte erstellen und aufrufen können. Weitere Informationen zu Azure Machine Learning finden Sie in den folgenden Artikeln:

* [Problembehandlung für Batchendpunkte](how-to-troubleshoot-batch-endpoints.md)
* [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
