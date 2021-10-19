---
title: Verwenden von &trade; DICOMweb-Standard-APIs mit C# – Azure Healthcare-APIs
description: In diesem Tutorial erfahren Sie, wie Sie DICOMweb Standard-APIs mit C# verwenden.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: aersoy
ms.openlocfilehash: 2594ff27dd7e4bae5c5463c32a5d4ee3d481cfe8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785036"
---
# <a name="using-dicomwebtrade-standard-apis-with-c"></a>Verwenden von &trade; DICOMweb-Standard-APIs mit C #

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial wird C# verwendet, um die Arbeit mit dem DICOM-Dienst zu veranschaulichen.

In diesem Tutorial verwenden wir die folgenden [DCM DICOM-Beispieldateien.](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)

* blue-circle.dcm
* dicom-metadata.csv
* green-square.dcm
* red-triangle.dcm

Der Dateiname, studyUID, seriesUID und instanceUID der DICOM-Beispieldateien lautet wie folgt:

| Datei | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> Jede dieser Dateien stellt eine einzelne Instanz dar und ist Teil derselben Studie. Außerdem sind das grüne Quadrat und das rote Dreieck Teil derselben Reihe, während sich der blaue Kreis in einer separaten Reihe befindet.

## <a name="prerequisites"></a>Voraussetzungen

Um die DICOMweb Standard-APIs verwenden zu &trade; können, muss eine Instanz des DICOM-Diensts bereitgestellt sein. Wenn Sie noch keine Instanz des DICOM-Diensts bereitgestellt haben, finden Sie weitere Informationen unter Bereitstellen des [DICOM-Diensts mithilfe der Azure-Portal](deploy-dicom-services-in-azure.md).

Nachdem Sie eine Instanz des DICOM-Diensts bereitgestellt haben, rufen Sie die URL für Ihren App Service ab:

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.
1. Suchen Sie **Nach zuletzt verfügbaren Ressourcen,** und wählen Sie Ihre DICOM-Dienstinstanz aus.
1. Kopieren Sie die **Dienst-URL** Ihres DICOM-Diensts. Stellen Sie sicher, dass Sie die Version als Teil der URL angeben, wenn Sie Anforderungen stellen. Weitere Informationen finden Sie in der Dokumentation zur [API-Versionierung für den DICOM-Dienst.](api-versioning-dicom-service.md)

Installieren Sie in Ihrer Anwendung die folgenden NuGet Pakete:

*  [DICOM-Client](https://microsofthealthoss.visualstudio.com/FhirServer/_packaging?_a=package&feed=Public&package=Microsoft.Health.Dicom.Client&protocolType=NuGet)

*  [fo-dicom](https://www.nuget.org/packages/fo-dicom/)

## <a name="create-a-dicomwebclient"></a>Erstellen eines DicomWebClient

Nachdem Sie Ihren DICOM-Dienst bereitgestellt haben, erstellen Sie einen DicomWebClient. Führen Sie den folgenden Codeausschnitt aus, um DicomWebClient zu erstellen, den wir für den Rest dieses Tutorials verwenden werden. Stellen Sie sicher, dass sie beide NuGet Pakete installiert haben, wie zuvor erwähnt. Wenn Sie noch kein Token erhalten haben, finden Sie weitere Informationen unter Abrufen des [Zugriffstokens für den DICOM-Dienst mit Azure CLI](dicom-get-access-token-azure-cli.md).

```c#
string webServerUrl ="{Your DicomWeb Server URL}"
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri(webServerUrl);
IDicomWebClient client = new DicomWebClient(httpClient);
client.HttpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", “{Your token value}”); 
```
Mit DicomWebClient können wir jetzt die Vorgänge Store, Abrufen, Suchen und Löschen ausführen.

## <a name="store-dicom-instances-stow"></a>Store DICOM-Instanzen (STOW)

Mit dem dicomWebClient, den wir erstellt haben, können wir jetzt DICOM-Dateien speichern.

### <a name="store-single-instance"></a>Store einzelne Instanz

Store einzelne Instanz veranschaulicht, wie eine einzelne DICOM-Datei hochgeladen wird.

_Details:_

* POST /studies

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To blue-circle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile });
```

### <a name="store-instances-for-a-specific-study"></a>Store Instanzen für eine bestimmte Studie

Store Instanzen für eine bestimmte Studie veranschaulichen, wie eine DICOM-Datei in eine angegebene Studie hochgeladen wird.

_Details:_

* POST /studies/{study}

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To red-triangle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile }, "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420");
```

Bevor Sie mit dem nächsten Teil des Tutorials fortfahren, laden Sie die `green-square.dcm` Datei mit einer der oben genannten Methoden hoch.

## <a name="retrieving-dicom-instances-wado"></a>Abrufen von DICOM-Instanzen (WADO)

Die folgenden Codeausschnitte veranschaulichen, wie die einzelnen Abrufabfragen mithilfe des zuvor erstellten DicomWebClient ausgeführt werden.

Die folgenden Variablen werden in den restlichen Beispielen verwendet:

```c#
string studyInstanceUid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; //StudyInstanceUID for all 3 examples
string seriesInstanceUid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; //SeriesInstanceUID for green-square and red-triangle
string sopInstanceUid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; //SOPInstanceUID for red-triangle
```

### <a name="retrieve-all-instances-within-a-study"></a>Abrufen aller Instanzen innerhalb einer Studie

Alle Instanzen innerhalb einer Studie abrufen ruft alle Instanzen innerhalb einer einzelnen Studie ab.

_Details:_

* GET /studies/{study}

```c#
DicomWebResponse response = await client.RetrieveStudyAsync(studyInstanceUid);
```

Alle drei dcm-Dateien, die wir zuvor hochgeladen haben, sind Teil derselben Studie, sodass die Antwort alle drei Instanzen zurückgeben sollte. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass alle drei Instanzen zurückgegeben werden.

### <a name="use-the-retrieved-instances"></a>Verwenden der abgerufenen Instanzen

Der folgende Codeausschnitt zeigt, wie Sie auf die abgerufenen Instanzen zugreifen. Außerdem wird gezeigt, wie Sie auf einige Felder der -Instanzen zugreifen und sie als DCM-Datei speichern.

```c#
DicomWebAsyncEnumerableResponse<DicomFile> response = await client.RetrieveStudyAsync(studyInstanceUid);
await foreach (DicomFile file in response)
{
    string patientName = file.Dataset.GetString(DicomTag.PatientName);
    string studyId = file.Dataset.GetString(DicomTag.StudyID);
    string seriesNumber = file.Dataset.GetString(DicomTag.SeriesNumber);
    string instanceNumber = file.Dataset.GetString(DicomTag.InstanceNumber);

    file.Save($"<path_to_save>\\{patientName}{studyId}{seriesNumber}{instanceNumber}.dcm");
}
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Abrufen von Metadaten aller untersuchten Instanzen

Diese Antwort ruft die Metadaten für alle Instanzen innerhalb einer einzelnen Studie ab.

_Details:_

* GET /studies/{study}/metadata

```c#
DicomWebResponse response = await client.RetrieveStudyMetadataAsync(studyInstanceUid);
```

Alle drei dcm-Dateien, die wir zuvor hochgeladen haben, sind Teil derselben Studie, sodass die Antwort die Metadaten für alle drei Instanzen zurückgeben sollte. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass alle Metadaten zurückgegeben werden.

### <a name="retrieve-all-instances-within-a-series"></a>Abrufen aller Instanzen innerhalb einer Reihe

Diese Antwort ruft alle Instanzen innerhalb einer einzelnen Reihe ab.

_Details:_

* GET /studies/{study}/series/{series}


```c#
DicomWebResponse response = await client.RetrieveSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

Diese Reihe verfügt über zwei Instanzen (grünes Quadrat und rotes Dreieck), sodass die Antwort beide Instanzen zurückgeben sollte. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass beide Instanzen zurückgegeben werden.

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>Abrufen von Metadaten aller Instanzen innerhalb einer Reihe

Diese Antwort ruft die Metadaten für alle Instanzen innerhalb einer einzelnen Studie ab.

_Details:_

* GET /studies/{study}/series/{series}/metadata

```c#
DicomWebResponse response = await client.RetrieveSeriesMetadataAsync(studyInstanceUid, seriesInstanceUid);
```

Diese Reihe verfügt über zwei Instanzen (grünes Quadrat und rotes Dreieck), sodass die Antwort Metadaten für beide Instanzen zurückgeben sollte. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass beide Instanzen der Metadaten zurückgegeben werden.

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Abrufen einer einzelnen Instanz innerhalb einer Reihe von Studien

Diese Anforderung ruft eine einzelne -Instanz ab.

_Details:_

* GET /studies/{study}/series{series}/instances/{instance}

```c#
DicomWebResponse response = await client.RetrieveInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

Diese Antwort sollte nur das rote Dreieck der Instanz zurückgeben. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass die Instanz zurückgegeben wird.

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Abrufen von Metadaten einer einzelnen Instanz innerhalb einer Reihe von Studien

Diese Anforderung ruft die Metadaten für eine einzelne Instanz innerhalb einer einzelnen Studie und Reihe ab.

_Details:_

* GET /studies/{study}/series/{series}/instances/{instance}/metadata

```c#
DicomWebResponse response = await client.RetrieveInstanceMetadataAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

Diese Antwort sollte nur die Metadaten für das rote Dreieck der Instanz zurückgeben. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und die Metadaten zurückgegeben werden.

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Abrufen eines oder mehrere Frames aus einer einzelnen Instanz

Diese Anforderung ruft einen oder mehrere Frames aus einer einzelnen Instanz ab.

_Details:_

* GET /studies/{study}/series/{series}/instances/{instance}/frames/{frames}

```c#
DicomWebResponse response = await client.RetrieveFramesAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid, frames: new[] { 1 });

```

Diese Antwort sollte den einzigen Frame aus dem roten Dreieck zurückgeben. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und der Frame zurückgegeben wird.

## <a name="query-dicom-qido"></a>Abfragen von DICOM (QIDO)

> [!NOTE]
> Unterstützte DICOM-Attribute finden Sie in der [DICOM-Konformitätserklärung.](dicom-services-conformance-statement.md#supported-search-parameters)

### <a name="search-for-studies"></a>Suchen nach Studien

Diese Anforderung sucht nach mindestens einer Studie nach DICOM-Attributen.

_Details:_

* GET /studies? StudyInstanceUID={study}

```c#
string query = $"/studies?StudyInstanceUID={studyInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Überprüft, ob die Antwort eine Studie enthält und ob der Antwortcode in Ordnung ist.

### <a name="search-for-series"></a>Suchen nach Reihen

Diese Anforderung sucht nach einer oder mehrere Reihen nach DICOM-Attributen.

_Details:_

* GET /series? SeriesInstanceUID={series}

```c#
string query = $"/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Überprüft, ob die Antwort eine Reihe enthält und ob der Antwortcode OK ist.

### <a name="search-for-series-within-a-study"></a>Suchen nach Reihen innerhalb einer Studie

Diese Anforderung sucht nach einer oder mehrere Reihen innerhalb einer einzelnen Studie nach DICOM-Attributen.

_Details:_

* GET /studies/{study}/series? SeriesInstanceUID={series}

```c#
string query = $"/studies/{studyInstanceUid}/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Überprüft, ob die Antwort eine Reihe enthält und ob der Antwortcode OK ist.

### <a name="search-for-instances"></a>Suchen nach Instanzen

Diese Anforderung sucht nach einer oder mehrere Instanzen nach DICOM-Attributen.

_Details:_

* GET /instances? SOPInstanceUID={instance}

```c#
string query = $"/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Überprüft, ob die Antwort eine Instanz enthält und ob der Antwortcode OK ist.

### <a name="search-for-instances-within-a-study"></a>Suchen nach Instanzen innerhalb einer Studie

Diese Anforderung sucht nach einer oder mehrere Instanzen innerhalb einer einzelnen Studie nach DICOM-Attributen.

_Details:_

* GET /studies/{study}/instances? SOPInstanceUID={instance}

```c#
string query = $"/studies/{studyInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Überprüft, ob die Antwort eine Instanz enthält und ob der Antwortcode OK ist.

### <a name="search-for-instances-within-a-study-and-series"></a>Suchen nach Instanzen innerhalb einer Studie und Reihe

Diese Anforderung sucht nach einer oder mehrere Instanzen innerhalb einer einzelnen Studie und einer einzelnen Reihe nach DICOM-Attributen.

_Details:_

* GET /studies/{study}/series/{series}instances? SOPInstanceUID={instance}

```c#
string query = $"/studies/{studyInstanceUid}/series/{seriesInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

Überprüft, ob die Antwort eine Instanz enthält und ob der Antwortcode OK ist.

## <a name="delete-dicom"></a>Löschen von DICOM

> [!NOTE]
> Das Löschen ist nicht Teil des DICOM-Standards, wurde jedoch der Einfachheit halber hinzugefügt.

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Löschen einer bestimmten Instanz innerhalb einer Studie und Reihe

Diese Anforderung löscht eine einzelne Instanz innerhalb einer einzelnen Studie und einer einzelnen Reihe.

_Details:_

* DELETE /studies/{study}/series/{series}/instances/{instance}

```c#
string sopInstanceUidRed = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395";
DicomWebResponse response = await client.DeleteInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUidRed);
```

Dadurch wird die Instanz des roten Dreiecks vom Server gelöscht. Wenn dies erfolgreich ist, enthält der Antwortstatuscode keinen Inhalt.

### <a name="delete-a-specific-series-within-a-study"></a>Löschen einer bestimmten Reihe innerhalb einer Studie

Diese Anforderung löscht eine einzelne Reihe (und alle untergeordneten Instanzen) innerhalb einer einzelnen Studie.

_Details:_

* DELETE /studies/{study}/series/{series}

```c#
DicomWebResponse response = await client.DeleteSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

Diese Antwort löscht die grün-quadratische Instanz (es ist das einzige in der Reihe übrige Element) vom Server. Wenn dies erfolgreich ist, enthält der Antwortstatuscode keinen Inhalt.

### <a name="delete-a-specific-study"></a>Löschen einer bestimmten Studie

Diese Anforderung löscht eine einzelne Studie (und alle untergeordneten Reihen und Instanzen).

_Details:_

* DELETE /studies/{study}

```c#
DicomWebResponse response = await client.DeleteStudyAsync(studyInstanceUid);
```

Diese Antwort löscht die Blaukreisinstanz (es ist das einzige in der Reihe übrige Element) vom Server. Wenn dies erfolgreich ist, enthält der Antwortstatuscode keinen Inhalt.

### <a name="next-steps"></a>Nächste Schritte

Informationen zum DICOM-Dienst finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
