---
title: Importieren von Daten in den Designer
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Daten in den Azure Machine Learning Designer importieren können, indem Sie Azure Machine Learning-Datensätze und die Komponente Daten importieren verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
author: likebupt
ms.author: keli19
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: c434319e48bb2bf1f7d0321a8200e9d42532e659
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289735"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Importieren von Daten in den Azure Machine Learning-Designer

In diesem Artikel erfahren Sie, wie Sie Ihre eigenen Daten in den Designer importieren, um benutzerdefinierte Lösungen zu erstellen. Es gibt zwei Möglichkeiten, wie Sie Daten in den Designer importieren können: 

* **Azure Machine Learning-Datasets** – Registrieren Sie [Datasets](concept-data.md#datasets) in Azure Machine Learning, um erweiterte Features zu aktivieren, die Sie bei der Verwaltung Ihrer Daten unterstützen.
* **Datenimport-Komponente** - Verwenden Sie die Komponente [Datenimport](algorithm-module-reference/import-data.md), um direkt auf Daten aus Online-Datenquellen zuzugreifen.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Verwenden von Azure Machine Learning-Datasets

Es wird empfohlen, dass Sie [Datasets](concept-data.md#datasets) verwenden, um Daten in den Designer zu importieren. Wenn Sie ein Dataset registrieren, können Sie die erweiterten Datenfeatures wie [Versionsverwaltung und Nachverfolgung](how-to-version-track-datasets.md) und [Datenüberwachung](how-to-monitor-datasets.md) in vollem Umfang nutzen.

### <a name="register-a-dataset"></a>Registrieren eines Datasets

Sie können vorhandene Datasets [programmgesteuert mit dem SDK](how-to-create-register-datasets.md#datasets-sdk) oder [visuell im Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) registrieren.

Sie können auch die Ausgabe für jede Designerkomponente als Datensatz registrieren.

1. Wählen Sie die Komponente aus, die die Daten ausgibt, die Sie registrieren möchten.

1. Wählen Sie im Bereich „Eigenschaften“ die Option **Ausgaben + Protokolle** > **Dataset registrieren** aus.

    ![Screenshot: Navigation zur Option „Dataset registrieren“](media/how-to-designer-import-data/register-dataset-designer.png)

Wenn die Ausgabedaten der Komponente in einem Tabellenformat vorliegen, müssen Sie wählen, ob die Ausgabe als **Datensatz in einer Datei** oder als **Tabellendatensatz** registriert werden soll.

 - **Dateidatensatz** registriert den Ausgabeordner der Komponente als Dateidatensatz. Der Ausgabeordner enthält eine Datendatei und Metadateien, die vom Designer intern verwendet werden. Wählen Sie diese Option aus, wenn Sie das registrierte Dataset weiterhin im Designer verwenden möchten. 

 - Mit **Tabellendatensatz** wird nur die Ausgabedatei der Komponente als Tabellendatensatz registriert. Dieses Format kann problemlos von anderen Tools verarbeitet werden, z. B. durch automatisiertes maschinelles Lernen oder im Python-SDK. Wählen Sie diese Option aus, wenn Sie das registrierte Dataset außerhalb des Designers verwenden möchten.  
 

### <a name="use-a-dataset"></a>Verwenden eines Datasets

Ihre registrierten Datensätze finden Sie in der Komponentenpalette unter **Datensätze**. Um ein Dataset zu verwenden, ziehen Sie es per Drag & Drop auf die Pipelinecanvas. Verbinden Sie dann den Ausgangsanschluss des Datensatzes mit anderen Komponenten im Canvas. 

Wenn Sie ein Dateidataset registrieren, ist der Ausgabeporttyp des Datasets **AnyDirectory**. Wenn Sie ein Tabellendataset registrieren, ist der Ausgabeporttyp des Datasets **DataFrameDirectory**. Wenn Sie den Ausgangsanschluss des Datensatzes mit anderen Komponenten im Designer verbinden, müssen die Anschlussarten von Datensätzen und Komponenten aufeinander abgestimmt werden.

![Screenshot: Speicherort gespeicherter Datasets in der Designerpalette](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Der Designer unterstützt die [Datasetversionierung](how-to-version-track-datasets.md). Geben Sie die Datensatzversion im Eigenschaftsfenster der Datensatzkomponente an.

### <a name="limitations"></a>Einschränkungen 

- Derzeit können nur tabellarische Datasets im Designer visualisiert werden. Wenn Sie ein Dateidataset außerhalb des Designers registrieren, können Sie es nicht im Designer-Zeichenbereich visualisieren.
- Derzeit unterstützt der Designer nur Vorschauausgaben, die in **Azure Blob Storage** gespeichert sind. Sie können Ihren Ausgabedatenspeicher in den **Ausgabeeinstellungen** unter der Registerkarte **Parameter** im rechten Bereich der Komponente überprüfen und ändern.
- Wenn Ihre Daten in einem virtuellen Netzwerk (VNet) gespeichert sind und Sie eine Vorschau anzeigen möchten, müssen Sie die vom Arbeitsbereich verwaltete Identität des Datenspeichers aktivieren.
    1. Navigieren Sie zum entsprechenden Datenspeicher und klicken Sie auf **Authentifizierung aktualisieren**
    :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="Anmeldeinformationen aktualisieren":::
    1. Wählen Sie **Ja** aus, um die vom Arbeitsbereich verwaltete Identität zu aktivieren.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Aktivieren der vom Arbeitsbereich verwalteten Identität":::

## <a name="import-data-using-the-import-data-component"></a>Daten mit der Komponente Daten importieren importieren

Obwohl wir empfehlen, Datasets für den Datenimport zu verwenden, können Sie auch die Komponente [Datenimport](algorithm-module-reference/import-data.md) verwenden. Die Komponente Daten importieren überspringt die Registrierung Ihres Datensatzes in Azure Machine Learning und importiert Daten direkt von einem [Datenspeicher](concept-data.md#datastores) oder einer HTTP-URL.

Ausführliche Informationen zur Verwendung der Komponente Datenimport finden Sie auf der Referenzseite [Datenimport](algorithm-module-reference/import-data.md).

> [!NOTE]
> Wenn das Dataset zu viele Spalten enthält, kann der folgende Fehler auftreten: „Fehler bei der Überprüfung aufgrund von Größenbeschränkung“. Um dies zu vermeiden, [registrieren Sie das Dataset in der Dataset-Schnittstelle](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Unterstützte Quellen

Dieser Abschnitt listet die vom Designer unterstützten Datenquellen auf. Die Daten kommen entweder aus einem Datenspeicher oder aus einem [Tabellendataset](how-to-create-register-datasets.md#dataset-types) in den Designer.

### <a name="datastore-sources"></a>Datenspeicherquellen
Eine Liste der unterstützten Datenspeicherquellen finden Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Tabellendatasetquellen

Der Designer unterstützt Tabellendatasets, die aus den folgenden Quellen erstellt wurden:
 * Durch Trennzeichen getrennte Dateien
 * JSON-Dateien
 * Parquet-Dateien
 * SQL-Abfragen

## <a name="data-types"></a>Datentypen

Der Designer erkennt intern die folgenden Datentypen:

* String
* Integer
* Decimal
* Boolean
* Date

Der Designer verwendet einen internen Datentyp, um Daten zwischen Komponenten zu übergeben. Sie können Ihre Daten explizit in das Datentabellenformat konvertieren, indem Sie die Komponente [Konvertieren in Datensatz](algorithm-module-reference/convert-to-dataset.md) verwenden. Jede Komponente, die andere Formate als das interne Format akzeptiert, konvertiert die Daten stillschweigend, bevor sie sie an die nächste Komponente weitergibt.

## <a name="data-constraints"></a>Dateneinschränkungen

Die Module im Designer sind durch die Größe des Computeziels begrenzt. Für größere Datasets sollten Sie eine größere Azure Machine Learning-Computeressource verwenden. Weitere Informationen zum Machine Learning-Computing finden Sie unter [Was sind Computeziele in Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed).

## <a name="access-data-in-a-virtual-network"></a>Zugreifen auf Daten in einem virtuellen Netzwerk

Wenn sich Ihr Arbeitsbereich in einem virtuellen Netzwerk befindet, müssen Sie zusätzliche Konfigurationsschritte ausführen, um die Daten im Designer visuell darzustellen. Weitere Informationen zur Verwendung von Datenspeichern und Datasets in virtuellen Netzwerken finden Sie unter [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den Grundlagen des Designers finden Sie im [Tutorial: Prognostizieren von Automobilpreisen mit dem Designer](tutorial-designer-automobile-price-train-score.md).
