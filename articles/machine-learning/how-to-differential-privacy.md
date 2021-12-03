---
title: 'Anleitung zu differenziellem Datenschutz: SmartNoise (Vorschau)'
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mithilfe der Open-Source-basierten SmartNoise-Bibliotheken verschiedene bewährte Datenschutzmethoden auf Azure Machine Learning-Modelle anwenden können.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.custom: responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 10/21/2021
ms.openlocfilehash: 98790b7e4e78d98936f80dd46f1ba83b7a4e945a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561535"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Verwenden von differenziellem Datenschutz in Azure Machine Learning (Vorschau)

Hier erfahren Sie, wie Sie mithilfe der Open-Source-basierten SmartNoise-Python-Bibliotheken verschiedene bewährte Datenschutzmethoden auf Azure Machine Learning-Modelle anwenden können.

Der differenzielle Datenschutz ist die Premiumdefinition des Datenschutzes. Systeme, die sich an diese Definition des Datenschutzes halten, bieten umfassende Schutzmaßnahmen gegen ein breites Spektrum von Angriffen durch Datenwiederherstellung und erneute Identifizierung, einschließlich Angriffen von Angreifern, die über zusätzliche Informationen verfügen. Weitere Informationen zur Funktionsweise des differenziellen Datenschutzes finden Sie [hier](./concept-differential-privacy.md).


## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.
- [Python 3](https://www.python.org/downloads/)

## <a name="install-smartnoise-python-libraries"></a>Installieren der SmartNoise-Python-Bibliotheken

### <a name="standalone-installation"></a>Eigenständige Installation

Die Bibliotheken sind für die Arbeit mit verteilten Spark-Clustern ausgelegt und können wie jedes andere Paket installiert werden.

Die folgenden Anweisungen gehen davon aus, dass Ihre `python`- und `pip`-Befehle `python3` und `pip3` zugeordnet sind.

Verwenden Sie pip, um die [SmartNoise-Python-Pakete](https://pypi.org/project/opendp-smartnoise/) zu installieren.

`pip install opendp-smartnoise`

Starten Sie eine Python-Eingabeaufforderung und geben Sie Folgendes ein, um zu überprüfen, ob die Pakete installiert sind:

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

Wenn die Importe erfolgreich durchgeführt wurden, sind die Bibliotheken installiert und einsatzbereit.

### <a name="docker-image-installation"></a>Docker-Imageinstallation

Sie können die SmartNoise-Pakete auch mit Docker verwenden.

Pullen Sie das `opendp/smartnoise`-Image, um die Bibliotheken innerhalb eines Docker-Containers zu verwenden, der Spark-, Jupyter- und Beispielcode enthält.

[!INCLUDE [pull-image-include](../../includes/pull-image-include.md)]

```sh
docker pull opendp/smartnoise:privacy
```

Nachdem Sie das Image gepullt haben, starten Sie den Jupyter-Server:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

Dadurch wird ein Jupyter-Server an Port `8989` auf Ihrem `localhost` mit dem Kennwort `pass@word99` gestartet. Unter der Annahme, dass Sie die obige Befehlszeile verwendet haben, um den Container mit dem Namen `smartnoise-privacy` zu starten, können Sie ein Bash-Terminal im Jupyter-Server öffnen, indem Sie Folgendes ausführen:

```sh
docker exec -it smartnoise-run bash
```

Die Docker-Instanz löscht beim Herunterfahren alle Zustände, sodass Sie alle Notebooks verlieren, die Sie in der aktiven Instanz erstellen. Um das Problem zu beheben, können Sie einen lokalen Ordner an den Container binden, wenn Sie ihn starten:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

Alle Notebooks, die Sie unter dem Ordner *my-notebooks* erstellen, werden in Ihrem lokalen Dateisystem gespeichert.

## <a name="perform-data-analysis"></a>Durchführen einer Datenanalyse

Um eine differenzielle private Freigabe vorzubereiten, müssen Sie eine Datenquelle, eine Statistik und einige Datenschutzparameter auswählen, die den Grad des Datenschutzes angeben.  

Dieses Beispiel bezieht sich auf die California Public Use Microdata (PUMS), die anonymisierte Aufzeichnungen der demografischen Daten von Bürgern darstellen:

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

In diesem Beispiel berechnen wir den Mittelwert und die Varianz des Alters.  Wir verwenden ein gesamtes `epsilon` von 1,0 (Epsilon ist unser Parameter für den Datenschutz, der unser Datenschutzbudget auf die beiden Größen verteilt, die wir berechnen möchten. Weitere Informationen zu [Datenschutzmetriken](concept-differential-privacy.md#differential-privacy-metrics).

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Die Ergebnisse sehen in etwa so aus wie die folgenden:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Zu diesem Beispiel gibt es einige wichtige Dinge zu beachten.  Zunächst stellt das `Analysis`-Objekt einen Datenverarbeitungsgraphen dar.  In diesem Beispiel werden der Mittelwert und die Varianz über denselben Quellknoten berechnet.  Sie können jedoch komplexere Ausdrücke einbeziehen, die Eingaben auf beliebige Weise mit Ausgaben kombinieren.

Die Analysegrafik enthält `data_upper`- und `data_lower`-Metadaten, die die untere und obere Grenze für das Alter angeben.  Mit diesen Werten werden die Stördaten präzise kalibriert, um den differenziellen Datenschutz sicherzustellen.  Diese Werte werden auch bei der Behandlung von Ausreißern oder fehlenden Werten verwendet.

Schließlich wird im Analysegraphen das gesamte ausgegebene Datenschutzbudget verfolgt.

Sie können die Bibliothek verwenden, um komplexere Analysegraphen mit mehreren Mechanismen, Statistiken und Hilfsfunktionen zu erstellen:

| Statistik    | Mechanismen | Versorgungsunternehmen  |
| ------------- |------------|------------|
| Anzahl         | Gaußscher   | Umwandeln       |
| Histogramm     | Geometrischer  | Clamping   |
| Mittelwert          | Laplace    | Digitalisieren   |
| Quantile     |            | Filtern     |
| SUM           |            | Anrechnen |
| Varianz/Kovarianz |      | Transformieren  |

Weitere Informationen finden Sie im [Notebook für die Datenanalyse](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb).

## <a name="approximate-utility-of-differentially-private-releases"></a>Ungefährer Nutzen unterschiedlich privater Versionen

Da der differenzielle Datenschutz durch die Kalibrierung von Stördaten funktioniert, kann der Nutzen von Versionen je nach Datenschutzrisiko variieren.  Im Allgemeinen werden die Stördaten, die zum Schutz der einzelnen Personen erforderlich sind, vernachlässigbar, wenn die Stichprobengrößen groß werden, aber bei Versionen, die auf eine einzelne Person abzielen, das Ergebnis überfordern.  Analysten können die Genauigkeitsinformationen für eine Version überprüfen, um festzustellen, wie nützlich die Version ist:

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

Das Ergebnis dieses Vorgangs sollte dem untenstehenden ähneln:

```text
Age accuracy is: 0.2995732273553991
```

In diesem Beispiel wird der Mittelwert wie oben berechnet und die `get_accuracy`-Funktion verwendet, um eine Genauigkeit von 0,05 bei `alpha` anzufordern. Ein `alpha` von 0,05 entspricht einem 95 %-Intervall, da dieser freigegebene Wert in etwa 95 % der Zeit innerhalb der angegebenen Genauigkeitsgrenzen liegt.  In diesem Beispiel beträgt die berichtete Genauigkeit 0,3. Dies bedeutet, dass der freigegebene Wert innerhalb eines Intervalls der Breite 0,6 liegt, also in etwa 95 % der Zeit.  Es ist nicht richtig, sich diesen Wert als Fehlerindikator vorzustellen, da der freigegebene Wert mit der durch `alpha` angegebenen Geschwindigkeit außerhalb des angegebenen Genauigkeitsbereichs liegt und die außerhalb des Bereichs liegenden Werte in beide Richtungen außerhalb des Bereichs liegen können.

Analysten können `get_accuracy` nach verschiedenen Werte von `alpha` abfragen, um enger oder weiter gefasste Konfidenzintervalle zu erhalten, ohne dass zusätzliche Kosten für den Datenschutz anfallen.

## <a name="generate-a-histogram"></a>Generieren eines Histogramms

Die integrierte `dp_histogram`-Funktion erstellt differenzielle private Histogramme über einen der folgenden Datentypen:

- Eine kontinuierliche Variable, bei der die Menge der Zahlen in Klassen unterteilt werden muss
- Eine boolesche oder dichotome Variable, die nur zwei Werte annehmen kann
- Eine kategorische Variable, bei der es verschiedene Kategorien gibt, die als Zeichenfolgen aufgezählt werden

Hier folgt ein Beispiel für eine `Analysis`, bei der die Klassen für ein Histogramm mit kontinuierlichen Variablen angegeben werden:

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Da die Einzelpersonen unzusammenhängend auf Histogrammklassen aufgeteilt sind, fallen die Kosten für den Datenschutz nur einmal pro Histogramm an, auch wenn das Histogramm viele Klassen umfasst.

Weitere Informationen zu Histogrammen finden Sie im [Notebook zu Histogrammen](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb).

## <a name="generate-a-covariance-matrix"></a>Generieren einer Kovarianzmatrix

SmartNoise bietet mit seiner `dp_covariance`-Funktion drei verschiedene Funktionen:

- Kovarianz zwischen zwei Vektoren
- Kovarianzmatrix einer Matrix
- Kovarianzübergreifende Matrix eines Matrizenpaars

Hier folgt ein Beispiel für die Berechnung einer skalaren Kovarianz:

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Weitere Informationen finden Sie im [Notebook zur Kovarianz](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb).

## <a name="next-steps"></a>Nächste Schritte

- Erkunden Sie die [SmartNoise-Beispielnotebooks](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis).
