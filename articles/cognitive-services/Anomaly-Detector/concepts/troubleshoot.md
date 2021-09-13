---
title: Problembehandlung bei der multivariaten Anomalieerkennungs-API
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie häufige Fehlercodes beheben, wenn Sie die Anomalieerkennungs-API verwenden.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: Anomalieerkennung, maschinelles Lernen, Algorithmen
ms.openlocfilehash: 46b67e3f465bfb481389049e4c45e5c2edbefbfc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292694"
---
# <a name="troubleshooting-the-multivariate-api"></a>Problembehandlung bei der multivariaten API

Dieser Artikel enthält Anleitungen zur Problembehandlung und Behebung gängiger Fehlermeldungen bei Verwendung der multivariaten API.

## <a name="multivariate-error-codes"></a>Multivariate Fehlercodes

### <a name="common-errors"></a>Häufige Fehler

| Fehlercode                 | HTTP-Fehlercode | Fehlermeldung                                  | Kommentar                                                      |
| -------------------------- | --------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| `SubscriptionNotInHeaders` | 400             | apim-subscription-id is not found in headers (APIM-Abonnement-ID wurde in Headern nicht gefunden) | Fügen Sie Ihre APIM-Abonnement-ID im Header hinzu. Beispielheader: `{"apim-subscription-id": <Your Subscription ID>}` |
| `FileNotExist`             | 400             | File \<source> does not exist. (Die Datei \<source> ist nicht vorhanden.)                  | Überprüfen Sie die Gültigkeit Ihrer Blob-SAS (Shared Access Signature). Vergewissern Sie sich, dass sie nicht abgelaufen ist. |
| `InvalidBlobURL`           | 400             |                                                | Ihre Blob-SAS (Shared Access Signature) ist keine gültige SAS.                            |
| `StorageWriteError`        | 403             |                                                | Dieser Fehler wird möglicherweise durch Berechtigungsprobleme verursacht. Unser Dienst darf die Daten nicht in das Blob schreiben, das mit einem kundenseitig verwalteten Schlüssel (Customer Managed Key, CMK) verschlüsselt ist. Entweder entfernen Sie den CMK, oder Sie gewähren wieder den Zugriff auf unseren Dienst. Weitere Informationen finden Sie auf [dieser Seite](../../encryption/cognitive-services-encryption-keys-portal.md). |
| `StorageReadError`         | 403             |                                                | Wie in `StorageWriteError`.                                 |
| `UnexpectedError`          | 500             |                                                | Wenden Sie sich mit detaillierten Fehlerinformationen an uns. Sie können die Supportoptionen in [diesem Dokument](../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fanomaly-detector%2fcontext%2fcontext) nutzen oder uns unter [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com) eine E-Mail senden.           |


### <a name="train-a-multivariate-anomaly-detection-model"></a>Trainieren eines multivariaten Anomalieerkennungsmodells

| Fehlercode               | HTTP-Fehlercode | Fehlermeldung                                                | Kommentar                                                      |
| ------------------------ | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `TooManyModels`          | 400             | This subscription has reached the maximum number of models. (Für dieses Abonnement wurde die maximale Anzahl von Modellen erreicht.)  | Jede APIM-Abonnement-ID darf 300 aktive Modelle aufweisen. Löschen Sie nicht verwendete Modelle, bevor Sie ein neues Modell trainieren. |
| `TooManyRunningModels`   | 400             | This subscription has reached the maximum number of running models. (Für dieses Abonnement wurde die maximale Anzahl ausgeführter Modelle erreicht.) | Jede APIM-Abonnement-ID darf fünf Modelle gleichzeitig trainieren. Trainieren Sie ein neues Modell, nachdem vorherige Modelle ihren Trainingsprozess abgeschlossen haben. |
| `InvalidJsonFormat`      | 400             | Invalid json format. (Ungültiges JSON-Format.)                                         | Die Trainingsanforderung ist kein gültiger JSON-Code.                        |
| `InvalidAlignMode`       | 400             | The `'alignMode'` field must be one of the following: `'Inner'` or `'Outer'`. (Das Feld `'alignMode'` muss einen der folgenden Werte aufweisen: `'Inner'` oder `'Outer'`.) | Überprüfen Sie den Wert von `'alignMode'`, der entweder `'Inner'` oder `'Outer'` lauten sollte (Groß- und Kleinschreibung beachten). |
| `InvalidFillNAMethod`    | 400             | The `'fillNAMethod'` field must be one of the following:  `'Previous'`, `'Subsequent'`, `'Linear'`, `'Zero'`, `'Fixed'`, `'NotFill'` and it cannot be `'NotFill'` when `'alignMode'` is `'Outer'`. (Das Feld `'fillNAMethod'` muss einen der folgenden Werte enthalten: `'Previous'`, `'Subsequent'`, `'Linear'`, `'Zero'`, `'Fixed'`, `'NotFill'`, und es kann nicht `'NotFill'` sein, wenn `'alignMode'` den Wert `'Outer'` hat.) | Überprüfen Sie den Wert von `'fillNAMethod'`. Weitere Informationen finden Sie in [diesem Abschnitt](./best-practices-multivariate.md#optional-parameters-for-training-api). |
| `RequiredPaddingValue`   | 400             | The `'paddingValue'` field is required in the request when `'fillNAMethod'` is `'Fixed'`. (Das Feld `'paddingValue'` ist in der Anforderung erforderlich, wenn `'fillNAMethod'` den Wert `'Fixed'` hat.) | Sie müssen einen gültigen Abstandswert angeben, wenn `'fillNAMethod'` den Wert `'Fixed'` hat. Weitere Informationen finden Sie in [diesem Abschnitt](./best-practices-multivariate.md#optional-parameters-for-training-api). |
| `RequiredSource`         | 400             | The `'source'` field is required in the request. (Das Feld `'source'` ist in der Anforderung erforderlich.)             | In Ihrer Trainingsanforderung ist kein Wert für das Feld `'source'` angegeben. Beispiel: `{"source": <Your Blob SAS>}`. |
| `RequiredStartTime`      | 400             | The `'startTime'` field is required in the request. (Das Feld `'startTime'` ist in der Anforderung erforderlich.)          | In Ihrer Trainingsanforderung ist kein Wert für das Feld `'startTime'` angegeben. Beispiel: `{"startTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidTimestampFormat` | 400             | Invalid Timestamp format. `<timestamp>` is not a valid format. (Ungültiges Zeitstempelformat. `<timestamp>` ist kein gültiges Format.) | Das Format des Zeitstempels im Anforderungstext ist nicht korrekt. Mit `import pandas as pd; pd.to_datetime(timestamp)` können Sie dies überprüfen. |
| `RequiredEndTime`        | 400             | The `'endTime'` field is required in the request. (Das Feld `'endTime'` ist in der Anforderung erforderlich.)            | In Ihrer Trainingsanforderung ist kein Wert für das Feld `'startTime'` angegeben. Beispiel: `{"endTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidSlidingWindow`   | 400             | The `'slidingWindow'` field must be an integer between 28 and 2880. (Das Feld `'slidingWindow'` muss eine ganze Zahl zwischen 28 und 2880 sein.) | `'slidingWindow'` muss eine ganze Zahl zwischen 28 und 2880 (einschließlich) sein. |

### <a name="get-multivariate-model-with-model-id"></a>Abrufen eines multivariaten Modells mit Modell-ID

| Fehlercode      | HTTP-Fehlercode | Fehlermeldung             | Kommentar                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
| `ModelNotExist` | 404             | The model does not exist. (Das Modell ist nicht vorhanden.) | Das Modell mit der entsprechenden Modell-ID ist nicht vorhanden. Überprüfen Sie die Modell-ID in der Anforderungs-URL. |

### <a name="list-multivariate-models"></a>Auflisten multivariater Modelle

| Fehlercode      | HTTP-Fehlercode | Fehlermeldung             | Kommentar                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
|`InvalidRequestParameterError`| 400             | Invalid values for $skip or $top … (Ungültige Werte für $skip oder $top ...) | Überprüfen Sie, ob die Werte für die beiden Parameter numerisch sind. $skip und $top werden verwendet, um die Modelle mit Paginierung aufzulisten. Da die API nur zehn zuletzt aktualisierte Modelle zurückgibt, können Sie mit $skip und $top Modelle früher aktualisieren lassen. | 

### <a name="anomaly-detection-with-a-trained-model"></a>Anomalieerkennung mit einem trainierten Modell

| Fehlercode        | HTTP-Fehlercode | Fehlermeldung                                                | Kommentar                                                      |
| ----------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `ModelNotExist`   | 404             | The model does not exist. (Das Modell ist nicht vorhanden.)                                    | Das für Rückschlüsse verwendete Modell ist nicht vorhanden. Überprüfen Sie die Modell-ID in der Anforderungs-URL. |
| `ModelFailed`     | 400             | Model failed to be trained. (Das Modell konnte nicht trainiert werden.)                                  | Das Modell wurde nicht erfolgreich trainiert. Ausführliche Informationen erhalten Sie, indem Sie das Modell mit der Modell-ID abrufen. |
| `ModelNotReady`   | 400             | Das Modell ist noch nicht bereit.                                  | Das Modell ist noch nicht bereit. Warten Sie eine Weile, bis der Trainingsprozess abgeschlossen ist. |
| `InvalidFileSize` | 413             | File \<file> exceeds the file size limit (\<size limit> bytes). (Die Datei \<file> überschreitet das Dateigrößenlimit (\<size limit> Bytes).) | Die Größe der Rückschlussdaten übersteigt die Obergrenze (derzeit 2 GB). Verwenden Sie weniger Daten für Rückschlüsse. |

### <a name="get-detection-results"></a>Abrufen von Erkennungsergebnissen

| Fehlercode       | HTTP-Fehlercode | Fehlermeldung              | Kommentar                                                      |
| ---------------- | --------------- | -------------------------- | ------------------------------------------------------------ |
| `ResultNotExist` | 404             | The result does not exist. (Das Ergebnis ist nicht vorhanden.) | Das Ergebnis pro Anforderung ist nicht vorhanden. Entweder wurden die Rückschlüsse nicht abgeschlossen, oder das Ergebnis ist abgelaufen (sieben Tage). |

### <a name="data-processing-errors"></a>Datenverarbeitungsfehler

Den folgenden Fehlercodes sind keine HTTP-Fehlercodes zugeordnet.

| Fehlercode             | Fehlermeldung                                                | Kommentar                                                      |
| ---------------------  | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `NoVariablesFound`     | No variables found. Please check that your files are organized as per instruction. (Keine Variablen gefunden. Überprüfen Sie, ob Ihre Dateien gemäß Anweisung organisiert sind.) | In der Datenquelle konnten keine CSV-Dateien gefunden werden. Dies wird in der Regel durch eine falsche Organisation von Dateien verursacht. Die gewünschte Struktur finden Sie in den Beispieldaten. |
| `DuplicatedVariables`  | There are multiple variables with the same name. (Es sind mehrere Variablen mit demselben Namen vorhanden.)             | Es sind doppelte Variablennamen vorhanden.                         |
| `FileNotExist`         | File \<filename> does not exist. (Die Datei \<filename> ist nicht vorhanden.)                              | Dieser Fehler tritt in der Regel bei Rückschlüssen auf. Die Variable wurde in den Trainingsdaten angezeigt, fehlt jedoch in den Rückschlussdaten. |
| `RedundantFile`        | File \<filename> is redundant. (Die Datei \<filename> ist redundant.)                                | Dieser Fehler tritt in der Regel bei Rückschlüssen auf. Die Variable war in den Trainingsdaten nicht enthalten, wird jedoch in den Rückschlussdaten angezeigt. |
| `FileSizeTooLarge`     | The size of file \<filename> is too large. (Die Datei \<filename> ist zu groß.)                    | Die Größe der einzelnen CSV-Datei \<filename> überschreitet das Limit. Trainieren Sie mit weniger Daten. |
| `ReadingFileError`     | Errors occurred when reading \<filename>. (Beim Lesen von \<filename> traten Fehler auf.) \<error messages>    | Fehler beim Lesen der Datei \<filename>. Weitere Informationen finden Sie unter \<error messages>, oder überprüfen Sie es mit `pd.read_csv(filename)` in einer lokalen Umgebung. |
| `FileColumnsNotExist`  | Columns timestamp or value in file \<filename> do not exist. (Die Spalten für „timestamp“ oder „value“ sind in der Datei \<filename> nicht vorhanden.)  | Jede CSV-Datei muss zwei Spalten mit den Namen **timestamp** und **value** enthalten (Groß- und Kleinschreibung beachten). |
| `VariableParseError`   | Variable \<variable> parse \<error message> error. (Fehler \<error message> beim Analysieren der Variablen \<variable>.)             | Die \<variable> kann aufgrund von Laufzeitfehlern nicht verarbeitet werden. Weitere Informationen finden Sie unter \<error message>, oder wenden Sie sich mit der \<error message> an uns. |
| `MergeDataFailed`      | Failed to merge data. Please check data format. (Fehler beim Zusammenführen von Daten. Überprüfen Sie das Datenformat.)              | Beim Zusammenführen von Daten ist ein Fehler aufgetreten. Dies ist möglicherweise auf ein falsches Datenformat, die Organisation von Dateien usw. zurückzuführen. Informationen zur aktuellen Dateistruktur finden Sie in den Beispieldaten. |
| `ColumnNotFound`       | Column \<column> cannot be found in the merged data. (Die Spalte \<column> wurde in den zusammengeführten Daten nicht gefunden.)          | Nach dem Zusammenführen fehlt eine Spalte. Überprüfen Sie die Daten.     |
| `NumColumnsMismatch`   | Number of columns of merged data does not match the number of variables. (Die Anzahl der Spalten der zusammengeführten Daten entspricht nicht der Anzahl der Variablen.) | Überprüfen Sie die Daten.                                      |
| `TooManyData`          | Too many data points. Maximum number is 1000000 per variable. (Zu viele Datenpunkte. Die maximale Anzahl beträgt 1000000 pro Variable.)       | Reduzieren Sie die Größe der Eingabedaten.                        |
| `NoData`               | There is no effective data. (Es gibt keine effektiven Daten.)                                   | Nach der Verarbeitung sind keine Daten zum Trainieren/für Rückschlüsse vorhanden. Überprüfen Sie die Start- und Endzeit. |
| `DataExceedsLimit`     | The length of data whose timestamp is between `startTime` and `endTime` exceeds limit(\<limit>). (Die Länge der Daten, deren Zeitstempel zwischen `startTime` und `endTime` liegt, überschreitet das Limit (\<limit>).) | Die Größe der Daten nach der Verarbeitung überschreitet das Limit. (Derzeit kein Limit für verarbeitete Daten.) |
| `NotEnoughInput`       | Not enough data. The length of data is \<data length>, but the minimum length should be larger than sliding window which is \<sliding window size>. (Nicht genügend Daten. Die Länge der Daten ist \<data length>, aber die Mindestlänge sollte größer als das gleitende Fenster sein, das \<sliding window size> ist.) | Die Mindestanzahl von Datenpunkten für Rückschlüsse ist die Größe des gleitenden Fensters. Versuchen Sie, mehr Daten für Rückschlüsse zur Verfügung zu stellen. |