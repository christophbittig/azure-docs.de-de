---
title: Optimierung der Leistung von Azure Data Lake Storage Gen1 – PowerShell
description: Tipps zur Verbesserung der Leistung, wenn Azure PowerShell mit Azure Data Lake Storage Gen1 verwendet wird.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: normesta
ms.openlocfilehash: 76b5b414b46d133528987702120ebe507d58910e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642223"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Leitfaden zur Leistungsoptimierung für die Verwendung von PowerShell mit Azure Data Lake Storage Gen1

In diesem Artikel werden die Eigenschaften beschrieben, die Sie optimieren können, um eine bessere Leistung zu erzielen, wenn Sie PowerShell für die Arbeit mit Data Lake Storage Gen1 verwenden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Leistungsbezogene Eigenschaften

| Eigenschaft            | Standard | BESCHREIBUNG |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Mit diesem Parameter können Sie die Anzahl von parallelen Threads zum Hochladen oder Herunterladen der einzelnen Dateien auswählen. Diese Zahl steht für die maximalen Threads, die pro Datei zugeordnet werden können, aber Sie erhalten je nach Szenario ggf. weniger Threads. (Wenn Sie beispielsweise eine Datei mit 1 KB hochladen, erhalten Sie auch dann nur einen Thread, wenn Sie 20 Threads anfordern.)  |
| ConcurrentFileCount | 10      | Dieser Parameter gilt speziell für das Hochladen oder Herunterladen von Ordnern. Mit diesem Parameter wird die Anzahl von gleichzeitigen Dateien bestimmt, die hoch- oder heruntergeladen werden können. Diese Zahl steht für die maximale Anzahl von Dateien, die gleichzeitig hoch- oder heruntergeladen werden können, aber je nach Szenario erhalten Sie ggf. eine geringere Parallelität. (Wenn Sie beispielsweise zwei Dateien hochladen, erhalten Sie auch dann nur zwei gleichzeitige Dateiuploads, wenn Sie 15 anfordern.) |

**Beispiel:**

Mit diesem Befehl werden Dateien aus Azure Data Lake Storage Gen1 auf den lokalen Datenträger des Benutzers heruntergeladen, indem 20 Threads pro Datei und 100 gleichzeitige Dateien verwendet werden.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Ermitteln von Eigenschaftswerten

Als Nächstes möchten Sie vielleicht wissen, wie Sie ermitteln, welcher Wert für die leistungsbezogenen Eigenschaften angegeben werden soll. Hier sind einige hilfreiche Informationen zur Vorgehensweise angegeben.

* **Schritt 1: Ermitteln der Gesamtanzahl von Threads**: Beginnen Sie, indem Sie die Gesamtanzahl der Threads berechnen, die verwendet werden sollen. Als Faustregel gilt, dass Sie sechs Threads für jeden physischen Kern verwenden sollten.

    `Total thread count = total physical cores * 6`

    **Beispiel:**

    Annahme: Sie führen die PowerShell-Befehle über eine D14-VM mit 16 Kernen aus.

    `Total thread count = 16 cores * 6 = 96 threads`

* **Schritt 2: Berechnen von PerFileThreadCount**: Wir berechnen den Wert für PerFileThreadCount basierend auf der Größe der Dateien. Für Dateien, die kleiner als 2,5 GB sind, muss dieser Parameter nicht geändert werden, da die Standardeinstellung 10 ausreicht. Für Dateien, die größer als 2,5 GB sind, sollten Sie zehn Threads als Basis für die ersten 2,5 GB nutzen und für jede weitere Steigerung der Dateigröße um 256 MB einen Thread hinzufügen. Wenn Sie einen Ordner mit Dateien mit stark variierender Größe haben, können Sie erwägen, die Dateien nach Größe zu gruppieren. Wenn die Dateigrößen sehr unterschiedlich sind, kann dies die Leistung beeinträchtigen. Falls das Gruppieren nach ähnlichen Dateigrößen nicht möglich sein sollte, ist es ratsam, den Wert für PerFileThreadCount basierend auf der größten Datei festzulegen.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Beispiel:**

    Angenommen, Sie haben 100 Dateien im Größenbereich von 1 GB bis 10 GB. Wir verwenden für die Gleichung dann 10 GB als größte Dateigröße und erhalten Folgendes:

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Schritt 3: Berechnen von „ConcurrentFilecount“:** Verwenden Sie die Gesamtzahl der Threads und den Wert von „PerFileThreadCount“, um „ConcurrentFileCount“ basierend auf der folgenden Gleichung zu berechnen:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Beispiel:**

    Basierend auf den verwendeten Beispielwerten:

    `96 = 40 * ConcurrentFileCount`

    **ConcurrentFileCount** ist also **2,4**, und dieser Wert kann auf **2** abgerundet werden.

## <a name="further-tuning"></a>Weitere Optimierung

Unter Umständen ist eine weitere Optimierung erforderlich, da viele verschiedene Dateigrößen verwendet werden. Die obige Berechnung funktioniert gut, wenn die meisten oder alle Dateien größer sind und eher im Bereich von 10 GB liegen. Falls dagegen viele unterschiedliche Dateigrößen und viele kleinere Dateien vorhanden sind, können Sie den Wert für PerFileThreadCount verringern. Wenn wir PerFileThreadCount reduzieren, kann der Wert für ConcurrentFileCount erhöht werden. Nehmen wir also an, dass die meisten Dateien kleiner sind und sich im Bereich von 5 GB bewegen. Wir können die Berechnung dann entsprechend ändern:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

**ConcurrentFileCount** beträgt jetzt also 96/20, was 4,8 ergibt und auf **4** abgerundet wird.

Sie können diese Einstellungen weiter optimieren, indem Sie den Wert für **PerFileThreadCount** je nach Verteilung der Dateigrößen nach oben oder unten anpassen.

### <a name="limitation"></a>Einschränkung

* **Anzahl von Dateien ist geringer als ConcurrentFileCount**: Wenn die Anzahl von hochgeladenen Dateien kleiner als der von Ihnen berechnete Wert von **ConcurrentFileCount** ist, sollten Sie **ConcurrentFileCount** reduzieren und gemäß der Anzahl von Dateien festlegen. Sie können alle verbleibenden Threads verwenden, um **PerFileThreadCount** zu erhöhen.

* **Zu viele Threads**: Wenn Sie die Anzahl von Threads zu stark erhöhen, ohne die Clustergröße zu erhöhen, können sich Leistungseinbußen ergeben. Es kann zu Problemen aufgrund von Konflikten kommen, die beim Kontextwechsel in der CPU entstehen.

* **Unzureichende Parallelität**: Falls die Parallelität nicht ausreicht, ist Ihr Cluster ggf. zu klein. Sie können die Anzahl von Knoten in Ihrem Cluster erhöhen, um mehr Parallelität zu erzielen.

* **Drosselungsfehler**: Es können Drosselungsfehler auftreten, wenn die Parallelität zu hoch ist. Falls es zu Drosselungsfehlern kommt, sollten Sie entweder die Parallelität reduzieren oder mit uns Kontakt aufnehmen.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Azure Data Lake Storage Gen1 für Big Data-Anforderungen](data-lake-store-data-scenarios.md) 
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Erstellen von HDInsight-Clustern mithilfe von Azure Data Lake Storage Gen1 im Azure-Portal](data-lake-store-hdinsight-hadoop-use-portal.md)

