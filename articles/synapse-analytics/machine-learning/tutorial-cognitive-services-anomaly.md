---
title: 'Tutorial: Anomalieerkennung mit Cognitive Services'
description: Informieren Sie sich darüber, wie Sie Cognitive Services zur Anomalieerkennung in Azure Synapse Analytics verwenden.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 07/01/2021
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: 505bef004acb688a1fdb1ccc87d261b1a12c6009
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318292"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services"></a>Tutorial: Anomalieerkennung mit Cognitive Services

In diesem Tutorial erfahren Sie, wie Sie Ihre Daten in Azure Synapse Analytics mit [Azure Cognitive Services](../../cognitive-services/index.yml) problemlos anreichern können. Sie verwenden die [Anomalieerkennung](../../cognitive-services/anomaly-detector/index.yml) zum Ermitteln von Anomalien. Ein Benutzer in Azure Synapse kann einfach eine Tabelle auswählen, um sie für die Erkennung von Anomalien anzureichern.

In diesem Lernprogramm wird Folgendes behandelt:

> [!div class="checklist"]
> - Schritte zum Abrufen eines Spark-Tabellendatasets mit Zeitreihendaten
> - Verwenden eines Assistenten in Azure Synapse, um Daten mithilfe der Anomalieerkennung in Cognitive Services anzureichern

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem als Standardspeicher konfigurierten Azure Data Lake Storage Gen2-Speicherkonto. Für das hier verwendete Data Lake Storage Gen2-Dateisystem müssen Sie über die Rolle *Mitwirkender an Storage-Blobdaten* verfügen.
- Spark-Pool in Ihrem Azure Synapse Analytics-Arbeitsbereich. Ausführliche Informationen finden Sie unter [Erstellen eines Spark-Pools in Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Abschluss der Vorkonfigurationsschritte im Tutorial [Konfigurieren von Cognitive Services in Azure Synapse](tutorial-configure-cognitive-services-synapse.md)

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-spark-table"></a>Erstellen einer Spark-Tabelle

Für dieses Tutorial benötigen Sie eine Spark-Tabelle.

1. Laden Sie die folgende Notebook-Datei mit Code zum Generieren einer Spark-Tabelle herunter: [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Laden Sie die Datei in ihren Azure Synapse-Arbeitsbereich hoch.

   ![Screenshot: Auswahl für das Hochladen eines Notebooks](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Öffnen Sie die Notebook-Datei, und wählen Sie **Alle ausführen** aus, um alle Zellen auszuführen.

   ![Screenshot: Auswahl für die Erstellung einer Spark-Tabelle](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. In der Spark-Standarddatenbank sollte nun eine Spark-Tabelle mit dem Namen **anomaly_detector_testing_data** angezeigt werden.

## <a name="open-the-cognitive-services-wizard"></a>Öffnen des Cognitive Services-Assistenten

1. Klicken Sie mit der rechten Maustaste auf die im vorherigen Schritt erstellte Spark-Tabelle. Wählen Sie **Machine Learning** > **Mit Modell vorhersagen** aus, um den Assistenten zu öffnen.

   ![Screenshot: Auswahl für das Öffnen des Bewertungs-Assistenten](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Ein Konfigurationsbereich wird angezeigt, und Sie werden aufgefordert, ein Cognitive Services-Modell auszuwählen. Wählen Sie **Anomalieerkennung** aus.

   ![Screenshot: Auswahl von „Anomalieerkennung“ als Modell](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="configure-anomaly-detector"></a>Konfigurieren der Anomalieerkennung

Geben Sie die folgenden Details zum Konfigurieren der Anomalieerkennung an:

- **Mit Azure Cognitive Services verknüpfter Dienst**: Als Teil der vorausgesetzten Schritte haben Sie einen mit [Cognitive Services verknüpften Dienst](tutorial-configure-cognitive-services-synapse.md) erstellt. Wählen Sie ihn hier aus.

- **Granularität**: Die Rate, mit der Stichproben Ihrer Daten entnommen werden. Wählen Sie **monatlich** aus. 

- **Spalte mit Zeitstempel**: Die Spalte, die die Uhrzeit der Reihe darstellt. Wählen Sie **timestamp (string)** (Zeitstempel (Zeichenfolge)) aus.

- **Spalte mit Zeitreihenwert**: Diese Spalte gibt den Wert der Reihe zu dem Zeitpunkt an, der unter der Spalte „Zeitstempel“ angegeben ist. Wählen Sie **value (double)** (Wert (doppelt)) aus.

- **Grouping column** (Gruppierungsspalte): Diese Spalte dient der Gruppierung der Reihe. Dies bedeutet, dass alle Zeilen mit gleichem Wert in dieser Spalte eine Zeitreihe bilden. Wählen Sie **group (string)** (Gruppe (Zeichenfolge)) aus.

Wenn Sie fertig sind, wählen Sie **Notebook öffnen** aus. Dadurch wird ein Notebook für Sie mit PySpark-Code generiert, der Azure Cognitive Services für die Anomalieerkennung verwendet.

![Screenshot: Konfigurationsdetails für die Anomalieerkennung](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-config.png)

## <a name="run-the-notebook"></a>Ausführen des Notebooks

Das soeben geöffnete Notebook verwendet die [SynapseML]-Bibliothek](https://github.com/microsoft/SynapseML), um eine Verbindung mit Cognitive Services herzustellen. Der mit Azure Cognitive Services verknüpfte Dienst, den Sie bereitgestellt haben, ermöglicht Ihnen, in dieser Umgebung sicher auf Ihren kognitiven Dienst zu verweisen, ohne Geheimnisse preiszugeben.

Sie können jetzt alle Zellen ausführen, um die Anomalieerkennung auszuführen. Wählen Sie **Alle ausführen** aus. [Hier](../../cognitive-services/anomaly-detector/index.yml) finden Sie weitere Informationen zur Anomalieerkennung in Cognitive Services.

![Screenshot: Anomalieerkennung](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-notebook.png)

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Stimmungsanalyse mit Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Assistent für die Modellbewertung in dedizierten SQL-Pools von Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)
