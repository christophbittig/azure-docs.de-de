---
title: Verwenden von &trade; DICOMweb-Standard-APIs mit cURL – Azure Healthcare-APIs
description: In diesem Tutorial erfahren Sie, wie Sie DICOMweb Standard-APIs mit cURL verwenden.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: afa4b294b71a3cde198001d204d4670cf8ca1ec1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785120"
---
# <a name="using-dicomwebtrade-standard-apis-with-curl"></a>Verwenden von &trade; DICOMWeb-Standard-APIs mit cURL

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial wird cURL verwendet, um die Arbeit mit dem DICOM-Dienst zu veranschaulichen.

In diesem Tutorial verwenden wir die folgenden [DCM DICOM-Beispieldateien.](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)

* blue-circle.dcm
* dicom-metadata.csv
* green-square.dcm
* red-triangle.dcm 

Der Dateiname, studyUID, seriesUID und instanceUID der DICOM-Beispieldateien lauten wie folgt:

| Datei | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

>[!NOTE]
>Jede dieser Dateien stellt eine einzelne Instanz dar und ist Teil derselben Studie. Außerdem sind das grüne Quadrat und das rote Dreieck Teil derselben Reihe, während sich der blaue Kreis in einer separaten Reihe befindet.

## <a name="prerequisites"></a>Voraussetzungen

Um die &trade; DICOMWeb-Standard-APIs verwenden zu können, muss eine Instanz des DICOM-Diensts bereitgestellt sein. Wenn Sie noch keine Instanz des DICOM-Diensts bereitgestellt haben, finden Sie weitere Informationen unter Bereitstellen des [DICOM-Diensts mithilfe Azure-Portal](deploy-dicom-services-in-azure.md).

Nachdem Sie eine Instanz des DICOM-Diensts bereitgestellt haben, rufen Sie die URL für Ihren App Service ab:

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.
2. Suchen Sie **nach Letzte Ressourcen,** und wählen Sie Ihre DICOM-Dienstinstanz aus.
3. Kopieren Sie **die Dienst-URL** Ihres DICOM-Diensts. 
4. Wenn Sie noch kein Token erhalten haben, finden Sie weitere Informationen unter Abrufen des Zugriffstokens für den [DICOM-Dienst mithilfe Azure CLI](dicom-get-access-token-azure-cli.md). 

Für diesen Code greifen wir auf einen Public Preview Azure-Dienst zu. Es ist wichtig, dass Sie keine privaten Integritätsinformationen (Private Health Information, PHI) hochladen.


## <a name="working-with-the-dicom-service"></a>Arbeiten mit dem DICOM-Dienst
 
Der DICOMweb-Standard nutzt http-Anforderungen in Kombination mit &trade; `multipart/related` DICOM-spezifischen Accept-Headern stark. Entwickler, die mit anderen REST-basierten APIs vertraut sind, arbeiten häufig mit dicomweb &trade; Standard. Sobald sie jedoch eingerichtet ist und ausgeführt wird, ist sie einfach zu verwenden. Es dauert nur ein wenig Vertrautheit, um zu beginnen.

Die cURL-Befehle enthalten jeweils mindestens eine und manchmal zwei Variablen, die ersetzt werden müssen. Um die Ausführung der Befehle zu vereinfachen, suchen und ersetzen Sie die folgenden Variablen, indem Sie sie durch Ihre spezifischen Werte ersetzen:

* {Dienst-URL} Dies ist die URL für den Zugriff auf Ihren DICOM-Dienst, den Sie im Azure-Portal bereitgestellt haben, z. B. ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Stellen Sie sicher, dass Sie die Version als Teil der URL angeben, wenn Sie Anforderungen stellen. Weitere Informationen finden Sie in der [Dokumentation zur API-Versionierung für den DICOM-Dienst.](api-versioning-dicom-service.md)
* {path-to-dicoms} – Der Pfad zu dem Verzeichnis, das die Datei red-triangle.dcm enthält, z. B. `C:/dicom-server/docs/dcms`
    * Stellen Sie sicher, dass Schrägstriche als  Trennzeichen verwendet werden, und beenden Sie das Verzeichnis ohne nachdenkenden Schrägstrich.


## <a name="uploading-dicom-instances-stow"></a>Hochladen von DICOM-Instanzen (STOW)

### <a name="store-instances-using-multipartrelated"></a>Store-instances-using-multipart/related

Diese Anforderung soll veranschaulichen, wie DICOM-Dateien mithilfe von mehrteiligen/verwandten Dateien hochgeladen werden. 

>[!NOTE]
>Der DICOM-Dienst ist lenienter als der DICOM-Standard. Im folgenden Beispiel wird jedoch eine POST-Anforderung veranschaulicht, die eng mit dem Standard konform ist.

_Details:_

* Pfad:.. /studies
* Methode: POST
* Headers:
    * Akzeptieren: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * Autorisierung: Bearer {Tokenwert}
* Hauptteil:
    * Content-Type: application/dicom für jede hochgeladene Datei, getrennt durch einen Begrenzungswert

Einige Programmiersprachen und Tools verhalten sich unterschiedlich. Einige erfordern beispielsweise, dass Sie Ihre eigene Grenze definieren. Für diese müssen Sie möglicherweise einen leicht geänderten Content-Type-Header verwenden. Folgendes wurde erfolgreich verwendet.
* Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
* Content-Type: multipart/related; boundary=ABCD1234
* Content-Type: multipart/related

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\""
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/red-triangle.dcm;type=application/dicom"
--trace-ascii "trace.txt"
```

### <a name="store-instances-for-a-specific-study"></a>Store instanzen für eine bestimmte Studie

Diese Anforderung veranschaulicht das Hochladen von DICOM-Dateien mithilfe von mehrerenParts/im Zusammenhang mit einer bestimmten Studie.

_Details:_
* Pfad:.. /studies/{study}
* Methode: POST
* Headers:
    * Akzeptieren: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * Autorisierung: Bearer {Tokenwert}
* Hauptteil:
    * Content-Type: application/dicom für jede hochgeladene Datei, getrennt durch einen Begrenzungswert

Einige Programmiersprachen und Tools verhalten sich unterschiedlich. Einige erfordern beispielsweise, dass Sie Ihre eigene Grenze definieren. Für diese müssen Sie möglicherweise einen leicht geänderten Content-Type-Header verwenden. Folgendes wurde erfolgreich verwendet.

 * Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
 * Content-Type: multipart/related; boundary=ABCD1234
 * Content-Type: multipart/related

```
curl --request POST "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\"
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/blue-circle.dcm;type=application/dicom"
```

### <a name="store-single-instance"></a>Store einzelinstanz

> [!NOTE]
> Dies ist eine nicht standardmäßige API, die das Hochladen einer einzelnen DICOM-Datei ermöglicht, ohne post für mehrteilige/verwandte Dateien konfigurieren zu müssen. Obwohl cURL mehrteilige/verwandte Dateien gut verarbeitet, ermöglicht diese API Tools wie Postman das Hochladen von Dateien in den DICOM-Dienst.

Die folgende Methode ist erforderlich, um eine einzelne DICOM-Datei hochzuladen.

_Details:_
* Pfad:.. /studies
* Methode: POST
* Headers:
   * Akzeptieren: application/dicom+json
   * Content-Type: application/dicom
   * Autorisierung: Bearer {Tokenwert}
* Hauptteil:
    * Enthält eine einzelne DICOM-Datei als binäre Bytes.

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: application/dicom"
--header "Authorization: Bearer {token value}"
--data-binary "@{path-to-dicoms}/green-square.dcm"
```

## <a name="retrieving-dicom-wado"></a>Abrufen von DICOM (WADO)

### <a name="retrieve-all-instances-within-a-study"></a>Abrufen aller Instanzen innerhalb einer Studie

Diese Anforderung ruft alle Instanzen innerhalb einer einzelnen Studie ab und gibt sie als Sammlung von mehrteiligen/verknüpften Bytes zurück.

_Details:_
* Pfad:.. /studies/{study}
* Methode: GET
* Headers:
   * Akzeptieren: mehrteilige/verwandte; type="application/dicom"; transfer-syntax=*
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

Dieser cURL-Befehl zeigt die heruntergeladenen Bytes in der Ausgabedatei (suppressWarnings.txt) an. Dabei handelt es sich jedoch nicht um direkte DICOM-Dateien, sondern nur um eine Textdarstellung des mehrteiligen/verwandten Downloads.

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Abrufen von Metadaten aller untersuchten Instanzen

Diese Anforderung ruft die Metadaten für alle Instanzen innerhalb einer einzelnen Studie ab.

_Details:_
* Pfad:.. /studies/{study}/metadata
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer {Tokenwert}

Dieser cURL-Befehl zeigt die heruntergeladenen Bytes in der Ausgabedatei (suppressWarnings.txt) an. Dabei handelt es sich jedoch nicht um direkte DICOM-Dateien, sondern nur um eine Textdarstellung des mehrteiligen/verwandten Downloads.

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-all-instances-within-a-series"></a>Abrufen aller Instanzen innerhalb einer Reihe

Diese Anforderung ruft alle Instanzen innerhalb einer einzelnen Reihe ab und gibt sie als Auflistung von mehrteiligen/verknüpften Bytes zurück.

_Details:_
* Pfad:.. /studies/{study}/series/{series}
* Methode: GET
* Headers:
   * Akzeptieren: mehrteilige/verwandte; type="application/dicom"; transfer-syntax=*
   * Autorisierung: Bearer {Tokenwert}

Dieser cURL-Befehl zeigt die heruntergeladenen Bytes in der Ausgabedatei (suppressWarnings.txt) an, aber es handelt sich nicht um die DICOM-Datei, sondern nur um eine Textdarstellung des mehrteiligen/verwandten Downloads.

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>Abrufen von Metadaten aller Instanzen innerhalb einer Reihe

Diese Anforderung ruft die Metadaten für alle Instanzen innerhalb einer einzelnen Studie ab.

_Details:_
* Pfad:.. /studies/{study}/series/{series}/metadata
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/studies1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```
 
### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Abrufen einer einzelnen Instanz innerhalb einer Reihe von Studien

Diese Anforderung ruft eine einzelne Instanz ab und gibt sie als DICOM-formatierten Bytedatenstrom zurück.

_Details:_
* Pfad:.. /studies/{study}/series{series}/instances/{instance}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom; transfer-syntax=*
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Abrufen von Metadaten einer einzelnen Instanz innerhalb einer Reihe von Studien

Diese Anforderung ruft die Metadaten für eine einzelne Instanz innerhalb einer einzelnen Studie und Reihe ab.

_Details:_
* Pfad:.. /studies/{study}/series/{series}/instances/{instance}/metadata
* Methode: GET
* Headers:
  * Akzeptieren: application/dicom+json
  * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Abrufen eines oder mehrere Frames aus einer einzelnen Instanz

Diese Anforderung ruft einen oder mehrere Frames aus einer einzelnen Instanz ab und gibt sie als Auflistung von mehrteiligen/verknüpften Bytes zurück. Mehrere Frames können abgerufen werden, indem eine durch Komma getrennte Liste von Framenummern übergeben wird.  Alle DICOM-Instanzen mit Bildern verfügen über mindestens einen Frame, bei dem es sich häufig nur um das Image handelt, das der Instanz selbst zugeordnet ist.

_Details:_
* Pfad:.. /studies/{study}/series{series}/instances/{instance}/frames/1,2,3
* Methode: GET
* Headers:
   * Akzeptieren: mehrteilige/verwandte; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1 (Standard) oder
   * Akzeptieren: mehrteilige/verwandte; type="application/octet-stream"; transfer-syntax=* oder
   * Akzeptieren: mehrteilige/verwandte; type="application/octet-stream";
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/frames/1"
--header "Accept: multipart/related; type=\"application/octet-stream\"; transfer-syntax=1.2.840.10008.1.2.1"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

## <a name="query-dicom-qido"></a>Abfragen von DICOM (QIDO)

In den folgenden Beispielen suchen wir mithilfe ihrer eindeutigen Bezeichner nach Elementen. Sie können auch nach anderen Attributen suchen, z. `PatientName` B. .

### <a name="search-for-studies"></a>Suchen nach Studien

Diese Anforderung ermöglicht die Suche nach mindestens einer Studie nach DICOM-Attributen.

Weitere Informationen zu den unterstützten DICOM-Attributen finden Sie in der [DICOM-Konformitätserklärung](dicom-services-conformance-statement.md).

_Details:_
* Pfad:.. /studies? StudyInstanceUID={study}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/studies?StudyInstanceUID=1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series"></a>Suchen nach Reihen

Diese Anforderung ermöglicht die Suche nach einer oder mehrere Reihen nach DICOM-Attributen.

Weitere Informationen zu den unterstützten DICOM-Attributen finden Sie in der [DICOM-Konformitätserklärung](dicom-services-conformance-statement.md).

_Details:_
* Pfad:.. /series? SeriesInstanceUID={series}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series-within-a-study"></a>Suchen nach Reihen innerhalb einer Studie

Diese Anforderung ermöglicht die Suche nach einer oder mehrere Reihen innerhalb einer einzelnen Studie nach DICOM-Attributen.

Weitere Informationen zu den unterstützten DICOM-Attributen finden Sie in der [DICOM-Konformitätserklärung](dicom-services-conformance-statement.md).

_Details:_
* Pfad:.. /studies/{study}/series? SeriesInstanceUID={series}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances"></a>Suchen nach Instanzen

Diese Anforderung ermöglicht die Suche nach einer oder mehrere Instanzen nach DICOM-Attributen.

Weitere Informationen zu den unterstützten DICOM-Attributen finden Sie in der [DICOM-Konformitätserklärung.](dicom-services-conformance-statement.md)

_Details:_
* Pfad:.. /instances? SOPInstanceUID={instanz}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study"></a>Suchen nach Instanzen innerhalb einer Studie

Diese Anforderung ermöglicht die Suche nach einer oder mehreren Instanzen innerhalb einer einzelnen Studie anhand von DICOM-Attributen.

Weitere Informationen zu den unterstützten DICOM-Attributen finden Sie in der [DICOM-Konformitätserklärung.](dicom-services-conformance-statement.md)

_Details:_
* Pfad:.. /studies/{study}/instances? SOPInstanceUID={instanz}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer {Tokenwert} 

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study-and-series"></a>Suchen nach Instanzen innerhalb einer Studie und Reihe

Diese Anforderung ermöglicht die Suche nach einer oder mehreren Instanzen innerhalb einer einzelnen Studie und einer einzelnen Reihe nach DICOM-Attributen.

Weitere Informationen zu den unterstützten DICOM-Attributen finden Sie in der [DICOM-Konformitätserklärung.](dicom-services-conformance-statement.md)

_Details:_
* Pfad:.. /studies/{study}/series/{series}/instances? SOPInstanceUID={instanz}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer {Tokenwert}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```


## <a name="delete-dicom"></a>Löschen von DICOM 

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Löschen einer bestimmten Instanz innerhalb einer Studie und Reihe

Diese Anforderung löscht eine einzelne Instanz innerhalb einer einzelnen Studie und einer einzelnen Reihe.

Das Löschen ist nicht Teil des DICOM-Standards, wurde jedoch der Einfachheit halber hinzugefügt.

_Details:_
* Pfad:.. /studies/{study}/series/{series}/instances/{instance}
* Methode: DELETE
* Headers:
   * Autorisierung: Bearer {Tokenwert}  

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-series-within-a-study"></a>Löschen einer bestimmten Reihe innerhalb einer Studie

Diese Anforderung löscht eine einzelne Reihe (und alle untergeordneten Instanzen) innerhalb einer einzelnen Studie.

Das Löschen ist nicht Teil des DICOM-Standards, wurde jedoch der Einfachheit halber hinzugefügt.

_Details:_
* Pfad:.. /studies/{study}/series/{series}
* Methode: DELETE
* Headers:
   * Autorisierung: Bearer {Tokenwert}

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-study"></a>Löschen einer bestimmten Studie

Diese Anforderung löscht eine einzelne Studie (und alle untergeordneten Reihen und Instanzen).

Das Löschen ist nicht Teil des DICOM-Standards, wurde jedoch der Einfachheit halber hinzugefügt.

_Details:_
* Pfad:.. /studies/{study}
* Methode: DELETE
* Headers:
   * Autorisierung: Bearer {Tokenwert}

```
curl--request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498
--header "Authorization: Bearer {token value}"
```

### <a name="next-steps"></a>Nächste Schritte

Informationen zum DICOM-Dienst finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
