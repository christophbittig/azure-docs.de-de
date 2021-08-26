---
title: Bing Daten zu COVID-19
titleSuffix: Azure Open Datasets
description: Hier erfahren Sie, wie das COVID-19-Dataset in Azure Open Datasets verwendet wird.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 7d797e169a0f1fbeeceaf377e731a051112c68e3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452297"
---
# <a name="bing-covid-19"></a>Bing Daten zu COVID-19

Diese Bing-Daten zu COVID-19 enthalten bestätigte Fälle, Fälle mit tödlichem Verlauf und genesene Patienten aus allen Regionen. Sie werden täglich aktualisiert.
Diese Daten werden im [Bing-Tracker zu COVID-19](https://bing.com/covid) verwendet.

Bing erfasst Daten aus mehreren vertrauenswürdigen und zuverlässigen Quellen. Dazu zählen die [Weltgesundheitsorganisation (World Health Organization, WHO)](https://www.who.int/emergencies/diseases/novel-coronavirus-2019), die [Centers for Disease Control and Prevention (CDC)](https://www.cdc.gov/coronavirus/2019-ncov/index.html), öffentliche Gesundheitsbehörden auf nationaler und bundesstaatlicher Ebene, [BNO News](https://bnonews.com/index.php/2020/04/the-latest-coronavirus-cases/), [24/7 Wall St.](https://247wallst.com/) und [Wikipedia](https://en.wikipedia.org/wiki/2019%E2%80%9320_coronavirus_pandemic).

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Datasets
Die geänderten Datasets sind im CSV-, JSON-, JSON-Lines und Parquet-Format verfügbar.

- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

Allen geänderten Datasets wurden ISO 3166-Teilgebietscodes und Ladezeiten hinzugefügt. Außerdem wurden die Spaltennamen in Kleinbuchstaben umformatiert und mit Unterstrichen versehen.

Rohdaten: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/bing_covid-19_data/latest/Bing-COVID19-Data.csv

Vorgängerversionen der geänderten Daten und Rohdaten: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/

## <a name="data-volume"></a>Datenvolumen
Alle Datasets werden täglich aktualisiert. Seit dem 11. Mai 2020 enthalten diese 125.576 Zeilen (CSV: 16,1 MB, JSON: 40,0 MB, JSONL: 39,6 MB, Parquet: 1,1 MB).

## <a name="license-and-use-rights-attribution"></a>Vergabe von Lizenz- und Nutzungsrechten
Diese Daten stehen ausschließlich für Bildungszwecke und akademische Zwecke zur Verfügung, z. B. für die medizinische Forschung, für Regierungsbehörden und akademische Einrichtungen, und zwar unter diesen [Geschäftsbedingungen](https://github.com/microsoft/Bing-COVID-19-Data/blob/master/LICENSE.txt).

In Publikationen verwendete oder zitierte Daten müssen mit einem Zuordnungsverweis zum „Bing-Tracker zu COVID-19“ und einem Link zu www.bing.com/covid gekennzeichnet werden.

## <a name="contact"></a>Contact
Wenden Sie sich bei Fragen oder Feedback zu diesem Dataset oder anderen Datasets im Data Lake zu COVID-19 an askcovid19dl@microsoft.com.

## <a name="columns"></a>Spalten

| Name             | Datentyp | Eindeutig    | Beispielwerte                    | BESCHREIBUNG                                                          |
|------------------|-----------|-----------|------------------------------------|----------------------------------------------------------------------|
| admin_region_1   | Zeichenfolge    | 864       | Texas Georgia                      | Region in country_region                                         |
| admin_region_2   | Zeichenfolge    | 3.143     | Washington County Jefferson County | Region in admin_region_1                                         |
| bestätigt        | INT       | 120,692   | 1 2                                | Bestätigte Todesfälle in der Region                                  |
| confirmed_change | INT       | 12,120    | 1 2                                | Änderungen an der Anzahl der bestätigten Fälle im Vergleich zum Vortag                 |
| country_region   | Zeichenfolge    | 237       | Vereinigte Staaten – Indien                | Land/Region                                                       |
| Todesfälle           | INT       | 20,616    | 1 2                                | Anzahl der Todesfälle in der Region                                      |
| deaths_change    | SMALLINT  | 1,981     | 1 2                                | Änderungen an der Anzahl der Todesfälle im Vergleich zum Vortag                          |
| id               | INT       | 1,783,534 | 742546 69019298                    | Eindeutiger Bezeichner                                                    |
| iso_subdivision  | Zeichenfolge    | 484       | US-TX US-GA                        | Zweiteiliger ISO-Teilgebietscode                                        |
| iso2             | Zeichenfolge    | 226       | US IN                              | Ländercode (zwei Buchstaben)                                     |
| iso3             | Zeichenfolge    | 226       | USA IND                            | Ländercode (drei Buchstaben)                                     |
| latitude         | double    | 5,675     | 42.28708 19.59852                  | Breitengrad des Schwerpunkts der Region                               |
| load_time        | timestamp | 1         | 2021-04-26 00:06:34.719000         | Datum und Uhrzeit des Dateiuploads von der Bing-Quelle auf GitHub |
| longitude        | double    | 5,693     | -2.5396 -155.5186                  | Längengrad des Schwerpunkts der Region                              |
| genesen        | INT       | 73,287    | 1 2                                | Anzahl genesener Patienten in der Region                                       |
| recovered_change | INT       | 10,441    | 1 2                                | Änderungen an der Anzahl genesener Patienten im Vergleich zum Vortag                 |
| aktualisiert          | date      | 457       | 2021-04-23 2021-04-22              | Datumsangabe für aktuellen Stand des Datensatzes                                        |

## <a name="preview"></a>Vorschau

| id     | aktualisiert    | bestätigt | Todesfälle | iso2 | iso3 | country_region | admin_region_1 | iso_subdivision | admin_region_2 | load_time             | confirmed_change | deaths_change |
|--------|------------|-----------|--------|------|------|----------------|----------------|-----------------|----------------|-----------------------|------------------|---------------|
| 338995 | 2020-01-21 | 262       | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr |                  |               |
| 338996 | 2020-01-22 | 313       | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 51               | 0             |
| 338997 | 23.01.2020 | 578       | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 265              | 0             |
| 338998 | 2020-01-24 | 841       | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 263              | 0             |
| 338999 | 2020-01-25 | 1320      | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 479              | 0             |
| 339000 | 2020-01-26 | 2014      | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 694              | 0             |
| 339001 | 2020-01-27 | 2798      | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 784              | 0             |
| 339002 | 2020-01-28 | 4593      | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 1795             | 0             |
| 339003 | 2020-01-29 | 6065      | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 1472             | 0             |
| 339004 | 30.01.2020 | 7818      | 0      | NULL | NULL | Weltweit      | NULL           | NULL            | NULL           | 4/26/2021 00:06:34 Uhr | 1753             | 0             |

## <a name="data-access"></a>Datenzugriff

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data -->

> [!TIP]
> **[Laden Sie stattdessen das Notebook herunter](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data)** .

#### <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-bing-covid-19-dataset"></a>In diesem Notebook werden die URLs und der Beispielcode für den Zugriff auf das [Bing COVID-19-Dataset](https://github.com/microsoft/Bing-COVID-19-Data) dokumentiert

Verwenden Sie die folgenden URLs, um bestimmte Dateiformate zu erhalten, die auf Azure Blob Storage gehostet sind:

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

Laden Sie die Dataset-Datei mithilfe des integrierten Funktionsdownloads in Pandas anhand der HTTP-URL herunter. Pandas verfügt über Reader für verschiedene Dateiformate:

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet")
df.head(10)
```

Überprüfen Sie die Datentypen der verschiedenen Felder, und ob die aktualisierte Spalte das datetime-Format aufweist.

```python
df.dtypes
```

Wir sehen uns nun die weltweiten Daten an und zeichnen einige einfache Diagramme, um die Daten zu visualisieren.

```python
df_Worldwide=df[df['country_region']=='Worldwide']
```

```python
df_Worldwide_pivot=df_Worldwide.pivot_table(df_Worldwide, index=['country_region','updated'])

df_Worldwide_pivot
```

```python
df_Worldwide.plot(kind='line',x='updated',y="confirmed",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="confirmed_change",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths_change",grid=True)
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Für diese Kombination aus Plattform und Paket ist kein Beispiel verfügbar.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Für diese Kombination aus Plattform und Paket ist kein Beispiel verfügbar.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[Laden Sie stattdessen das Notebook herunter](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Für diese Kombination aus Plattform und Paket ist kein Beispiel verfügbar.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[Laden Sie stattdessen das Notebook herunter](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit den restlichen Datasets im [Open Datasets-Katalog](dataset-catalog.md) vertraut.
