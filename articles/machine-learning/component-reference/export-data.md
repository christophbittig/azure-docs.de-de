---
title: '„Export Data“: Komponentenreferenz'
titleSuffix: Azure Machine Learning
description: Verwenden Sie die Komponente „Export Data“ (Daten exportieren) im Azure Machine Learning-Designer, um Ergebnisse und Zwischendaten außerhalb von Azure Machine Learning zu speichern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 60821daf863e90ecc4f4f14db2197632d965740c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566634"
---
# <a name="export-data-component"></a>„Export Data“-Komponente

In diesem Artikel wird eine Komponente im Azure Machine Learning-Designer beschrieben.

Verwenden Sie diese Komponente, um Ergebnisse, Zwischendaten und Arbeitsdaten aus Ihren Pipelines in Cloudspeicherzielen zu speichern. 

Diese Komponente unterstützt das Exportieren von Daten in die folgenden Clouddatendienste:

- Azure-Blobcontainer
- Azure-Dateifreigabe
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL-Datenbank

Bevor Sie Ihre Daten exportieren können, müssen Sie einen Datenspeicher in Ihrem Azure Machine Learning-Arbeitsbereich registrieren. Weitere Informationen finden Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Gewusst wie: Konfigurieren von „Export Data“

1. Fügen Sie Ihrer Pipeline im Designer die **Export Data**-Komponente hinzu. Sie finden diese Komponente in der Kategorie **Eingabe und Ausgabe**.

1. Verbinden Sie die **Export Data**-Komponente mit der Komponente, die die zu exportierenden Daten enthält.

1. Wählen Sie **Export Data** aus, um den Bereich **Properties** (Eigenschaften) zu öffnen.

1. Wählen Sie für **Datastore** (Datenspeicher) in der Dropdownliste einen vorhandenen Datenspeicher aus. Sie können auch einen neuen Datenspeicher erstellen. Wie dies funktioniert, erfahren Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](../how-to-access-data.md).

    > [!NOTE]
    > Das Exportieren von Daten eines bestimmten Datentyps in eine SQL-Datenbankspalte, für die ein anderer Datentyp angegeben ist, wird nicht unterstützt. Die Zieltabelle muss nicht vorab existieren.

1. Das Kontrollkästchen **Regenerate output** (Ausgabe neu generieren) entscheidet, ob die Komponente ausgeführt werden soll, um die Ausgabe zur Laufzeit neu zu generieren. 

    Die Auswahl ist standardmäßig deaktiviert. Das bedeutet, dass das System die Ausgabe der letzten Ausführung wiederverwendet, um die Laufzeit zu verkürzen, wenn die Komponente zuvor mit denselben Parameter ausgeführt wurde. 

    Wenn diese Option ausgewählt wird, führt das System die Komponente erneut aus, um die Ausgabe erneut zu generieren.

1. Geben Sie den Pfad im Datenspeicher an, an dem sich die Daten befinden. Bei dem Pfad handelt es sich um einen relativen Pfad. Am Beispiel `data/testoutput` wird ersichtlich, dass die Eingabedaten von **Export Data** in `data/testoutput` in den Datenspeicher exportiert werden, den Sie bei den **Ausgabeeinstellungen** der Komponente festlegen.

    > [!NOTE]
    > Leere Pfade oder **URL-Pfade** sind nicht zulässig.


1. Wählen Sie für **File format** (Dateiformat) das Format aus, in dem die Daten gespeichert werden sollen.
 
1. Übermitteln Sie die Pipeline.

## <a name="limitations"></a>Einschränkungen

Wenn Ihre Rückschlusspipeline die **Export Data**-Komponente enthält, wird sie aufgrund der Zugriffsbeschränkung des Datenspeichers automatisch entfernt, wenn sie auf einem Echtzeitendpunkt bereitgestellt wird.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie die für Azure Machine Learning [verfügbaren Komponenten](component-reference.md). 
