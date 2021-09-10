---
title: Genome Aggregation Database (gnomAD)
titleSuffix: Azure Open Datasets
description: Hier erfahren Sie, wie Sie das Dataset für Genome Aggregation Database (gnomAD) in Azure Open Datasets verwenden.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 779f39afa7ac9b9890013db546223274cdf37ad6
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867663"
---
# <a name="genome-aggregation-database-gnomad"></a>Genome Aggregation Database (gnomAD)

[Genome Aggregation Database (gnomAD)](https://gnomad.broadinstitute.org/) ist eine von einem internationalen Forscherzusammenschluss entwickelte Ressource. Sie dient dem Zweck, Exom- und Genomsequenzierungsdaten aus zahlreichen großen Sequenzierungsprojekten zu aggregieren, zu vereinheitlichen und der Wissenschaft in zusammengefasster Form zur Verfügung zu stellen.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Datenquelle

Dieses Dataset wird in Zusammenarbeit mit dem Broad Institute gehostet. Den vollständigen gnomAD-Datenkatalog finden Sie unter https://gnomad.broadinstitute.org/downloads.

## <a name="data-volumes-and-update-frequency"></a>Datenvolumes und Aktualisierungshäufigkeit

Dieses Dataset enthält etwa 30 TB Daten und wird mit jedem gnomAD-Release aktualisiert.

## <a name="storage-location"></a>Speicherort

Das Speicherkonto für dieses Dataset befindet sich in der Azure-Region „USA, Osten“. Aus Gründen der Affinität wird die Zuweisung von Computeressourcen in der Region „USA, Osten“ empfohlen.

## <a name="data-access"></a>Datenzugriff

Speicherkonto: https://datasetgnomad.blob.core.windows.net/dataset/

Diese Daten sind uneingeschränkt öffentlich verfügbar, und für Massenvorgänge wird das Tool „AzCopy“ empfohlen. So zeigen Sie beispielsweise die VCF-Dateien im Release 3.0 von gnomAD an:

```powershell
$ azcopy ls https://datasetgnomad.blob.core.windows.net/dataset/release/3.0/vcf/genomes
```

So laden Sie alle VCF-Dateien rekursiv herunter:

```powershell
$ azcopy cp --recursive=true https://datasetgnomad.blob.core.windows.net/dataset/release/3.0/vcf/genomes .
```

**NEU: Parquet-Format von gnomAD v2.1.1-VCF-Dateien (Exome und Genome)**

So zeigen Sie die Parquet-Dateien an

```powershell
$ azcopy ls https://datasetgnomadparquet.blob.core.windows.net/dataset
```

So laden Sie alle Parquet-Dateien rekursiv herunter

```powershell
$ cp --recursive=true https://datasetgnomadparquet.blob.core.windows.net/dataset
```

Der [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) ist zum Durchsuchen der Dateiliste im gnomAD-Release ebenfalls nützlich.

## <a name="use-terms"></a>Nutzungsbedingungen

Die Daten stehen uneingeschränkt zur Verfügung. Weitere Informationen und Zitatdetails finden Sie auf der Infoseite zu [gnomAD](https://gnomad.broadinstitute.org/about).

## <a name="contact"></a>Contact

Bei Fragen oder Feedback zu diesem Dataset wenden Sie sich an das [gnomAD-Team](https://gnomad.broadinstitute.org/contact).

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit den restlichen Datasets im [Open Datasets-Katalog](dataset-catalog.md) vertraut.
