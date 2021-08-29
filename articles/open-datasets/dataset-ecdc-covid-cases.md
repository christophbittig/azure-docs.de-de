---
title: COVID-19-Fälle des Europäischen Zentrums für die Prävention und die Kontrolle von Krankheiten l (ECDC)
titleSuffix: Azure Open Datasets
description: Erfahren Sie, wie Sie das Dataset der COVID-19-Fälle des Europäischen Zentrums für die Prävention und die Kontrolle von Krankheiten (ECDC) in Azure Open Datasets verwenden.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 31868ee27e531e70df4c89944f0baf888527a190
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447598"
---
# <a name="european-centre-for-disease-prevention-and-control-ecdc-covid-19-cases"></a>COVID-19-Fälle des Europäischen Zentrums für die Prävention und die Kontrolle von Krankheiten l (ECDC)

Hier finden Sie die [aktuell verfügbaren öffentlichen Daten](https://www.ecdc.europa.eu/en/publications-data/download-todays-data-geographic-distribution-covid-19-cases-worldwide) zur geografischen Verbreitung der COVID-19-Fälle weltweit, die vom Europäischen Zentrum für die Prävention und die Kontrolle von Krankheiten (European Centre for Disease Prevention and Control, ECDC) stammen. Jede Zeile bzw. jeder Eintrag enthält die Anzahl der neu gemeldeten Fälle pro Tag und Land oder Region.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>Datasets

Geänderte Versionen des Datasets sind im CSV-, JSON-, JSONL- und Parquet-Format verfügbar. Sie werden täglich aktualisiert:
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

Allen geänderten Datasets wurden iso_country_region-Codes und Ladezeiten hinzugefügt. Außerdem wurden die Spaltennamen in Kleinbuchstaben umformatiert und mit Unterstrichen versehen.

Rohdaten: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/latest/ECDCCases.csv

Vorgängerversionen der geänderten Daten und Rohdaten: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/

## <a name="data-volume"></a>Datenvolumen
Nach Stand des 28. Mai 2020 enthalten diese 19.876 Zeilen (CSV: 1,5 MB, JSON: 4,9 MB, JSONL: 4,9 MB, Parquet: 54,1 KB).

## <a name="data-source"></a>Datenquellen-

Die Rohdaten werden täglich aus der [ECDC-CSV-Datei](https://opendata.ecdc.europa.eu/covid19/casedistribution/csv) übernommen. Weitere Informationen zu diesem Dataset, einschließlich seiner Ursprünge, finden Sie auf der Seite der[ECDC-Datensammlungen](https://www.ecdc.europa.eu/en/covid-19/data-collection).

## <a name="data-quality"></a>Datenqualität
Das ECDC übernimmt keine Garantie für die Genauigkeit oder Aktualität der Daten. [Lesen Sie den Haftungsausschluss](https://www.ecdc.europa.eu/en/covid-19/data-collection).

## <a name="license-and-use-rights-attribution"></a>Anerkennung der Lizenzbedingungen und Nutzungsrechte

Diese Daten wurden unter Einhaltung der hier einsehbaren ECDC-Urheberrechtrichtlinie veröffentlicht und dürfen nur in Übereinstimmung mit dieser verwendet werden. Wenn das Urheberrecht an einem Dokument bei einem Dritten liegt, müssen Sie die Erlaubnis zur Vervielfältigung beim Inhaber des Urheberrechts einholen.

Das ECDC muss immer als ursprüngliche Quelle dieser Daten angegeben werden. Diese Bestätigung muss in jeder Kopie dieser Materialien vorhanden sein.

## <a name="contact"></a>Contact

Wenden Sie sich bei Fragen oder Feedback zu diesem Dataset oder anderen Datasets im Data Lake zu COVID-19 an askcovid19dl@microsoft.com.

## <a name="columns"></a>Spalten

| Name                      | Datentyp | Eindeutig | Beispielwerte            | BESCHREIBUNG                            |
|---------------------------|-----------|--------|----------------------------|----------------------------------------|
| cases                     | SMALLINT  | 5,515  | 1 2                        | Anzahl gemeldeter Fälle               |
| continent_exp             | Zeichenfolge    | 6      | Europa, Afrika              | Name des Kontinents                         |
| countries_and_territories | Zeichenfolge    | 214    | Kanada, Belgien             | Name des Landes oder Gebiets              |
| country_territory_code    | Zeichenfolge    | 213    | KOR, ISL                    | Aus drei Buchstaben bestehender Länder- oder Gebietscode |
| date_rep                  | date      | 350    | 2020-12-11 2020-11-22      | Datum der Meldung                     |
| day                       | SMALLINT  | 31     | 14 13                      | Tag des Monats                           |
| Todesfälle                    | SMALLINT  | 1,049  | 1 2                        | Anzahl gemeldeter Todesfälle              |
| geo_id                    | Zeichenfolge    | 214    | CA SE                      | Geografischer Bezeichner                         |
| iso_country               | Zeichenfolge    | 214    | SE US                      | Landes- oder Regionscode gemäß ISO 3166        |
| load_date                 | timestamp | 1      | 2021-04-26 00:06:22.123000 | Uploaddatum der Daten in Azure      |
| month                     | SMALLINT  | 12     | 10 8                       | Monatsnummer                           |
| year                      | SMALLINT  | 2      | 2020 2019                  | Jahr                                   |

## <a name="preview"></a>Vorschau

| date_rep   | day | month | year | cases | Todesfälle | countries_and_territories | geo_id | country_territory_code | continent_exp | load_date             | iso_country |
|------------|-----|-------|------|-------|--------|---------------------------|--------|------------------------|---------------|-----------------------|-------------|
| 2020-12-14 | 14  | 12    | 2020 | 746   | 6      | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-13 | 13  | 12    | 2020 | 298   | 9      | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-12 | 12  | 12    | 2020 | 113   | 11     | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-11 | 11  | 12    | 2020 | 63    | 10     | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-10 | 10  | 12    | 2020 | 202   | 16     | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-09 | 9   | 12    | 2020 | 135   | 13     | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-08 | 8   | 12    | 2020 | 200   | 6      | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-07 | 7   | 12    | 2020 | 210   | 26     | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-06 | 6   | 12    | 2020 | 234   | 10     | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |
| 2020-12-05 | 5   | 12    | 2020 | 235   | 18     | Afghanistan               | AF     | AFG                    | Asien          | 4/26/2021 00:06:22 Uhr | AF          |

## <a name="data-access"></a>Datenzugriff

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Dieses Notebook dokumentiert die URLs und den Beispielcode für den Zugriff auf die Dataset-URLs des Europäischen Zentrums für die Prävention und die Kontrolle von Krankheiten (ECDC) zu COVID-19-Fällen verschiedener Dataset-Dateiformate, die in Azure Blob Storage gehostet werden: CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

Laden Sie die Dataset-Datei mithilfe des integrierten Funktionsdownloads in Pandas anhand der HTTP-URL herunter. Pandas verfügt über Reader für verschiedene Dateiformate:

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet")
df.head(10)

df.dtypes

df.groupby('countries_and_territories').first().filter(['continent_exp','cases', 'deaths','date_rep'])

df.groupby('continent_exp').agg({'countries_and_territories': 'count','cases': 'count','deaths': 'count'})

import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countries_and_territories', 'cases', 'deaths']].groupby(['countries_and_territories'
         ]).max().sort_values(by='cases',ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')

df_Worldwide=df[df['countries_and_territories']=='United_States_of_America']

df.plot(kind='line',x='date_rep',y="cases",grid=True)
df.plot(kind='line',x='date_rep',y="deaths",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="confirmed_change",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="deaths_change",grid=True)

```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

Für diese Kombination aus Plattform und Paket ist kein Beispiel verfügbar.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Für diese Kombination aus Plattform und Paket ist kein Beispiel verfügbar.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Für diese Kombination aus Plattform und Paket ist kein Beispiel verfügbar.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

## <a name="examples"></a>Beispiele

Sehen Sie sich Beispiele für die Verwendung dieses Datasets an:

- [Analysieren von COVID-Daten mit einem serverlosen Endpunkt in Synapse SQL](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/how-azure-synapse-analytics-helps-you-analyze-covid-data-with/ba-p/1457836)
- [Lineare Regressionsanalyse für COVID-Daten mithilfe des SQL Endpunkts in Azure Synapse Analytics](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/linear-regression-analysis-on-covid-data-using-sql-endpoint-in/ba-p/1468697)

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit den restlichen Datasets im [Open Datasets-Katalog](dataset-catalog.md) vertraut.
