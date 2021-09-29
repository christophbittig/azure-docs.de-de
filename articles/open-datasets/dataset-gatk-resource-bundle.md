---
title: GATK-Ressourcenpaket
titleSuffix: Azure Open Datasets
description: Hier erfahren Sie, wie Sie das GATK-Ressourcenpaketdataset in Azure Open Datasets verwenden.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1b775d6ca0dd00a2e6eacf292c2f3397a0de303b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563018"
---
# <a name="gatk-resource-bundle"></a>GATK-Ressourcenpaket

Beim [GATK-Ressourcenpaket](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle) handelt es sich um eine Sammlung von Standarddateien für die Arbeit mit menschlichen Resequenzierungsdaten mit dem GATK.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Datenquelle

Bei diesem Dataset handelt es sich um eine Spiegelung des Datenspeichers unter https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle.

## <a name="data-volumes-and-update-frequency"></a>Datenvolumes und Aktualisierungshäufigkeit

1. datasetgatkbestpractices: 542 GB
1. datasetgatklegacybundles: 61 GB
1. datasetgatktestdata: 2 TB
1. datasetpublicbroadref: 477 GB
1. datasetbroadpublic: 3 TB

Datasets werden monatlich während der ersten Woche jedes Monats aktualisiert.

## <a name="storage-location"></a>Speicherort

Dieses Dataset wird in den Azure-Regionen „USA, Westen 2“ und „USA, Westen-Mitte“ gespeichert. Aus Gründen der Affinität wird die Zuweisung von Computeressourcen in die Regionen „USA, Westen 2“ oder „USA, Westen-Mitte“ empfohlen.

## <a name="data-access"></a>Datenzugriff

1. datasetgatkbestpractices

    USA, Westen 2: https://datasetgatkbestpractices.blob.core.windows.net/dataset
    
    USA, Westen-Mitte: https://datasetgatkbestpractices-secondary.blob.core.windows.net/dataset
    
    [SAS-Token](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=6SaDfKtXAIfdpO%2BkvNA%2FsTNmNij%2Byh%2F%2F%2Bf98WAUqs7I%3D

2. datasetgatklegacybundles

    USA, Westen 2: https://datasetgatklegacybundles.blob.core.windows.net/dataset
    
    USA, Westen-Mitte: https://datasetgatklegacybundles-secondary.blob.core.windows.net/dataset
    
    [SAS-Token](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=xBfxOPBqHKUCszzwbNCBYF0k9osTQjKnZbEjXCW7gU0%3D

3. datasetgatktestdata

    USA, Westen 2: https://datasetgatktestdata.blob.core.windows.net/dataset
    
    USA, Westen-Mitte: https://datasetgatktestdata-secondary.blob.core.windows.net/dataset
    
    [SAS-Token](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=fzLts1Q2vKjuvR7g50vE4HteEHBxTcJbNvf%2FZCeDMO4%3D

4. datasetpublicbroadref
    
    USA, Westen 2: https://datasetpublicbroadref.blob.core.windows.net/dataset
    
    USA, Westen-Mitte: https://datasetpublicbroadref-secondary.blob.core.windows.net/dataset
    
    [SAS-Token](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=DQxmjB4D1lAfOW9AxIWbXwZx6ksbwjlNkixw597JnvQ%3D

5. datasetbroadpublic

    USA, Westen 2: https://datasetbroadpublic.blob.core.windows.net/dataset
    
    USA, Westen-Mitte: https://datasetbroadpublic-secondary.blob.core.windows.net/dataset
    
    [SAS-Token](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=u%2Bg2Ab7WKZEGiAkwlj6nKiEeZ5wdoJb10Az7uUwis%2Fg%3D

## <a name="use-terms"></a>Nutzungsbedingungen

Besuchen Sie die [offizielle Website zum GATK-Ressourcenpaket](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle).

## <a name="contact"></a>Contact

Besuchen Sie die [offizielle Website zum GATK-Ressourcenpaket](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle).

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit den restlichen Datasets im [Open Datasets-Katalog](dataset-catalog.md) vertraut.
