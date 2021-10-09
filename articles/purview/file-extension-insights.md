---
title: Berichterstellung zu Dateierweiterungen für Ihre Daten in Azure Purview mit Purview-Erkenntnissen
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Berichterstellung zu Dateierweiterungen für Ihre Daten in Purview anzeigen und verwenden können.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 706c27603233369836368239645cf28222da8eaa
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208539"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Dateierweiterungserkenntnisse zu Ihren Daten aus Azure Purview 

In dieser Schrittanleitung wird beschrieben, wie Sie auf die in Ihren Daten gefundenen Dateierweiterungen bzw. Dateitypen zugreifen, sie anzeigen und filtern können.

> [!IMPORTANT]
> Azure Purview-Erkenntnisse befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Unterstützte Datenquellen sind unter anderem: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Amazon S3 Buckets

In dieser Schrittanleitung lernen Sie Folgendes:
> [!div class="checklist"]
> * Starten Ihres Purview-Kontos über Azure 
> - Anzeigen von Dateierweiterungserkenntnissen zu Ihren Daten
> - Ausführen eines Drilldowns zum Abrufen weiterer Dateierweiterungsdetails zu Ihren Daten

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Beginn mit Purview-Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

- Einrichten Ihrer Azure-Ressourcen und Füllen der entsprechenden Konten mit Testdaten

- Einrichten und Abschließen einer Überprüfung der Testdaten in den einzelnen Datenquellen. Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview](manage-data-sources.md) und [Erstellen eines Scan-Regelsatzes](create-a-scan-rule-set.md).

- Anmeldung bei Purview mit einem Konto mit der Rolle [„Datenleser“ oder „Datenkurator“](catalog-permissions.md#roles)


Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Verwenden von Purview-Erkenntnisse zu Dateierweiterungen

Beim Überprüfen Ihrer Ressourcen ist Azure Purview in der Lage, die in Ihrem Datenbestand gefundenen Dateitypen zu erkennen und Ihnen weitere Details zu jedem Dateityp bereitzustellen. Die Details umfassen, über wie viele Dateien der einzelnen Typen Sie verfügen, wo sich diese Dateien befinden und ob sie auf vertrauliche Daten durchsucht werden können.

> [!NOTE]
> Nachdem Sie Ihre Quelltypen überprüft haben, geben Sie den Erkenntnissen zur **Dateierweiterung** ein paar Stunden Zeit, um die neuen Ressourcen zu berücksichtigen.

**So zeigen Sie Erkenntnisse zu Dateierweiterungen an**

1. Wechseln Sie zum **Azure Purview**-[Instanzbildschirm im Azure-Portal](https://aka.ms/purviewportal), und wählen Sie Ihr Purview-Konto aus.

1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** die Kontokachel **Purview starten** aus.

1. Wählen Sie in Purview das Menüelement **Erkenntnisse** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: auf der linken Seite aus, um auf Ihren **Erkenntnisse**-Bereich zuzugreifen.
    
1. Wählen Sie in **Erkenntnisse** die Registerkarte **Dateierweiterungen** aus.

    Der Bericht zeigt eine Zusammenfassung an, wie viele eindeutige Dateierweiterungen gefunden wurden, sowie einen Graphen der 10 am häufigsten gefundenen Erweiterungen über den ausgewählten Zeitraum (Standard: 30 Tage).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Dateierweiterungsbericht: Übersicht" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Gehen Sie folgendermaßen vor, um weitere Informationen zu erhalten:

    - Wählen Sie den Selektor **Zeit** am oberen Rand des Berichts aus, um die Zeitspanne zu ändern, für die die Dateierweiterungen gefunden wurden.
    
    - Wählen Sie **Mehr anzeigen** unterhalb des Graphen aus, um eine vollständige Liste der gefundenen Dateierweiterungen anzuzeigen. Weitere Informationen finden Sie unter [Drilldown zu den Dateierweiterungserkenntnissen](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Drilldown zu den Dateierweiterungserkenntnissen

Nachdem Sie die allgemeinen Informationen zu den Dateitypen in Ihrem Datenbestand angezeigt haben, können Sie weitere Details dazu anzeigen, wo sich die Dateien befinden und ob sie auf vertrauliche Daten überprüft werden können.

Beispiel:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Dateierweiterungsbericht: Drilldown" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

Das Raster zeigt Details für jede gefundene Dateierweiterung, einschließlich:

- **Dateianzahl** Die Anzahl der Dateien mit der angegebenen Erweiterung.
- **Inhaltsüberprüfung** Gibt an, ob die Dateierweiterung für das Überprüfen auf vertrauliche Daten unterstützt wird.
- **Abonnements** Die Anzahl der Abonnements, bei denen die angegebene Erweiterung gefunden wurde.
- **Quellen** Die Anzahl der gefundenen Quellen mit der angegebenen Dateierweiterung.



Verwenden Sie die Filter oberhalb des Rasters, um die angezeigten Daten zu filtern:

|Option  |BESCHREIBUNG  |
|---------|---------|
|**Nach Schlüsselwort filtern**     |    Geben Sie Text in das Feld **Nach Schlüsselwort filtern** ein, um Ihre Dateitypen nach Namen zu filtern. Geben Sie z. B. `PDF` ein, um nur PDF-Dateien anzuzeigen.     |
|**Time**        | Wählen Sie diese Option aus, um nach einer bestimmten Zeitspanne zu filtern, in der Ihre Daten erstellt wurden. <br>**Standardwert:** 30 Tage  |
|**Dateierweiterung**     |Wählen Sie diese Option aus, um das Raster nach einem oder mehreren Dateitypen zu filtern.        |
|**Sources**    |Wählen Sie diese Option aus, um das Raster nach bestimmten Datenquellen zu filtern. |
|**Inhaltsüberprüfung**     |Wählen Sie **Unterstützt** oder **Nicht unterstützt** aus, um nur Dateitypen anzuzeigen, die weiter auf vertrauliche Daten überprüft werden können, oder Daten, die nicht überprüft werden können, wie **CERT**- oder **JPG**-Dateien. |
| | |

Wählen Sie oberhalb der Filter die Option **Spalten bearbeiten** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: aus, um mehr oder weniger Spalten in Ihrem Raster anzuzeigen oder die Reihenfolge zu ändern. 

Wählen Sie einen Spaltenheader aus, um das Raster nach dieser Spalte zu sortieren.
## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure Purview-Erkenntnisberichte
> [!div class="nextstepaction"]
> [Glossarerkenntnisse](glossary-insights.md)

> [!div class="nextstepaction"]
> [Überprüfungserkenntnisse](scan-insights.md)

> [!div class="nextstepaction"]
> [Klassifizierungs-Erkenntnisse](./classification-insights.md)

> [!div class="nextstepaction"]
> [Vertraulichkeitsbezeichnungs-Erkenntnisse](sensitivity-insights.md)
