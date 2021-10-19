---
title: Verwenden von DICOMweb-Standard-APIs mit Python – Azure Healthcare-APIs
description: In diesem Tutorial wird die Verwendung von DICOMweb Standard-APIs mit Python beschrieben.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: c474409b2dfdbca0a921690b1871c7268bde08d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783454"
---
# <a name="using-dicomwebtrade-standard-apis-with-python"></a>Verwenden von &trade; DICOMWeb-Standard-APIs mit Python

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial wird python verwendet, um die Arbeit mit dem DICOM-Dienst zu veranschaulichen.

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

> [!NOTE]
> Jede dieser Dateien stellt eine einzelne Instanz dar und ist Teil derselben Studie. Außerdem sind das grüne Quadrat und das rote Dreieck Teil derselben Reihe, während sich der blaue Kreis in einer separaten Reihe befindet.

## <a name="prerequisites"></a>Voraussetzungen

Um die &trade; DICOMWeb-Standard-APIs verwenden zu können, muss eine Instanz des DICOM-Diensts bereitgestellt sein. Wenn Sie den DICOM-Dienst noch nicht bereitgestellt haben, finden Sie weitere Informationen unter Bereitstellen des [DICOM-Diensts mithilfe Azure-Portal](deploy-dicom-services-in-azure.md).

Nachdem Sie eine Instanz des DICOM-Diensts bereitgestellt haben, rufen Sie die URL für Ihren App Service ab:

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.
1. Suchen Sie **nach Letzte Ressourcen,** und wählen Sie Ihre DICOM-Dienstinstanz aus.
1. Kopieren Sie **die Dienst-URL** Ihres DICOM-Diensts. 
2. Wenn Sie noch kein Token erhalten haben, finden Sie weitere Informationen unter Abrufen des Zugriffstokens für den [DICOM-Dienst mithilfe Azure CLI](dicom-get-access-token-azure-cli.md). 

Für diesen Code greifen wir auf einen Public Preview Azure-Dienst zu. Es ist wichtig, dass Sie keine privaten Integritätsinformationen (Private Health Information, PHI) hochladen.

## <a name="working-with-the-dicom-service"></a>Arbeiten mit dem DICOM-Dienst

Der DICOMweb-Standard nutzt http-Anforderungen in Kombination mit &trade; `multipart/related` DICOM-spezifischen Accept-Headern stark. Entwickler, die mit anderen REST-basierten APIs vertraut sind, arbeiten häufig mit dem &trade; DICOMweb-Standard. Sobald sie jedoch eingerichtet ist und ausgeführt wird, ist sie einfach zu verwenden. Es dauert nur ein wenig Vertrautheit, um zu beginnen.

### <a name="import-the-appropriate-python-libraries"></a>Importieren der entsprechenden Python-Bibliotheken

Importieren Sie zunächst die erforderlichen Python-Bibliotheken.

Wir haben uns entschieden, dieses Beispiel mithilfe der synchronen Bibliothek `requests` zu implementieren. Erwägen Sie für die asynchrone Unterstützung die `httpx` Verwendung von oder einer anderen asynchronen Bibliothek. Darüber hinaus importieren wir zwei unterstützende Funktionen aus , um `urllib3` die Arbeit mit Anforderungen zu `multipart/related` unterstützen.

Darüber hinaus importieren wir , um uns bei Azure zu anmelden `DefaultAzureCredential` und ein Token abzurufen.

```python
import requests
import pydicom
from pathlib import Path
from urllib3.filepost import encode_multipart_formdata, choose_boundary
from azure.identity import DefaultAzureCredential
```

### <a name="configure-user-defined-variables-to-be-used-throughout"></a>Konfigurieren benutzerdefinierter Variablen, die überall verwendet werden sollen

Ersetzen Sie alle Variablenwerte, die in { } umschlossen sind, durch Ihre eigenen Werte. Überprüfen Sie außerdem, ob alle konstruierten Variablen richtig sind.  Beispielsweise wird mithilfe der Dienst-URL erstellt und dann mit der version `base_url` der verwendeten REST-API angefügt. Die Dienst-URL Ihres DICOM-Diensts ist: ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Sie können das Azure-Portal verwenden, um zum DICOM-Dienst zu navigieren und Ihre Dienst-URL zu erhalten. Weitere Informationen zur Versionsbelegung finden Sie auch in der Dokumentation zur API-Versionierung für den [DICOM-Dienst.](api-versioning-dicom-service.md) Wenn Sie eine benutzerdefinierte URL verwenden, müssen Sie diesen Wert mit Ihrer eigenen überschreiben.

```python
dicom_service_name = "{server-name}"
path_to_dicoms_dir = "{path to the folder that includes green-square.dcm and other dcm files}"

base_url = f"{Service URL}/v{version}"

study_uid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; #StudyInstanceUID for all 3 examples
series_uid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; #SeriesInstanceUID for green-square and red-triangle
instance_uid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; #SOPInstanceUID for red-triangle
```

### <a name="authenticate-to-azure-and-get-a-token"></a>Authentifizieren bei Azure und Abrufen eines Tokens

`DefaultAzureCredential` ermöglicht es uns, eine Vielzahl von Möglichkeiten zum Erhalten von Token für die Anmeldung beim Dienst zu erhalten. Wir verwenden , um `AzureCliCredential` ein Token für die Anmeldung beim Dienst abzurufen. Es gibt andere Anmeldeinformationsanbieter wie `ManagedIdentityCredential` und , die ebenfalls verwendet werden `EnvironmentCredential` können. Um AzureCliCredential verwenden zu können, müssen Sie sich über die CLI bei Azure angemeldet haben, bevor Sie diesen Code ausführen. (Weitere Informationen finden Sie unter Abrufen des Zugriffstokens für den [DICOM-Dienst](dicom-get-access-token-azure-cli.md) Azure CLI-Diensts.) Alternativ können Sie das token, das bei der Anmeldung über die CLI abgerufen wurde, einfach kopieren und einfügen.

> [!NOTE]
> `DefaultAzureCredential` gibt mehrere verschiedene Credential-Objekte zurück. Wir verweisen auf `AzureCliCredential` als das 5. Element in der zurückgegebenen Sammlung. Dies ist möglicherweise nicht konsistent. Wenn dies derKomma ist, können Sie die Auskommentierung `print(credential.credential)` der Zeile auskommentiert. Dadurch werden alle Elemente aufgeführt. Suchen Sie den richtigen Index, und denken Sie daran, dass Python nullbasierte Indizierung verwendet.

> [!NOTE]
> Wenn Sie sich nicht über die CLI bei Azure angemeldet haben, ist dies nicht erfolgreich. Damit dies funktioniert, müssen Sie über die CLI bei Azure angemeldet sein. 

```python
from azure.identity import DefaultAzureCredential
credential = DefaultAzureCredential()

#print(credential.credentials) # this can be used to find the index of the AzureCliCredential
token = credential.credentials[4].get_token('https://dicom.healthcareapis.azure.com')
bearer_token = f'Bearer {token.token}'
```

### <a name="create-supporting-methods-to-support-multipartrelated"></a>Erstellen von unterstützenden Methoden zur Unterstützung `multipart\related`

Die Bibliotheken (und die meisten Python-Bibliotheken) funktionieren mit nicht auf eine Weise, die `Requests` `multipart\related` DICOMweb &trade; unterstützt. Aufgrund dieser Bibliotheken müssen wir einige Methoden hinzufügen, um die Arbeit mit DICOM-Dateien zu unterstützen.

`encode_multipart_related` akzeptiert eine Reihe von Feldern (im Fall von DICOM sind diese Bibliotheken in der Regel Part 10-10-Datei) und eine optionale benutzerdefinierte Grenze. Sie gibt sowohl den vollständigen Text als auch die content_type zurück, die verwendet werden können.

```python
def encode_multipart_related(fields, boundary=None):
    if boundary is None:
        boundary = choose_boundary()

    body, _ = encode_multipart_formdata(fields, boundary)
    content_type = str('multipart/related; boundary=%s' % boundary)

    return body, content_type
```

### <a name="create-a-requests-session"></a>Erstellen einer `requests` Sitzung

Erstellt eine `requests` Sitzung namens , die für die Kommunikation mit dem `client` DICOM-Dienst verwendet wird.


```python
client = requests.session()
```

### <a name="verify-authentication-is-configured-correctly"></a>Überprüfen, ob die Authentifizierung ordnungsgemäß konfiguriert ist

Rufen Sie den Changefeed-API-Endpunkt auf, der bei erfolgreicher Authentifizierung 200 zurück gibt.

```python
headers = {"Authorization":bearer_token}
url= f'{base_url}/changefeed'

response = client.get(url,headers=headers)
if (response.status_code != 200):
    print('Error! Likely not authenticated!')
```

## <a name="uploading-dicom-instances-stow"></a>Hochladen von DICOM-Instanzen (STOW)

In den folgenden Beispielen wird das Beibehalten von DICOM-Dateien erläutert.

### <a name="store-instances-using-multipartrelated"></a>Store -Instanzen mit`multipart/related`

In diesem Beispiel wird veranschaulicht, wie eine einzelne DICOM-Datei hochgeladen wird, und es wird ein Bit Python verwendet, um die DICOM-Datei (als Bytes) vorab in den Arbeitsspeicher zu laden. Durch Übergeben eines Arrays von Dateien an den Fields-Parameter von können mehrere Dateien in einem `encode_multipart_related` einzelnen POST hochgeladen werden. Es wird manchmal verwendet, um mehrere Instanzen innerhalb einer vollständigen Reihe oder Studie hochzuladen.

_Details:_

* Pfad:.. /studies
* Methode: POST
* Headers:
    * Akzeptieren: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * Autorisierung: Bearer $token"

* Hauptteil:
    * Content-Type: application/dicom für jede hochgeladene Datei, getrennt durch einen Begrenzungswert

Einige Programmiersprachen und Tools verhalten sich unterschiedlich. Einige davon erfordern beispielsweise, dass Sie Ihre eigene Grenze definieren. Für diese müssen Sie möglicherweise einen leicht geänderten Content-Type-Header verwenden. Folgendes wurde erfolgreich verwendet.
* Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
* Content-Type: multipart/related; boundary=ABCD1234
* Content-Type: multipart/related

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    rawfile = reader.read()
files = {'file': ('dicomfile', rawfile, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```

### <a name="store-instances-for-a-specific-study"></a>Store Instanzen für eine bestimmte Studie

In diesem Beispiel wird veranschaulicht, wie mehrere DICOM-Dateien in die angegebene Studie hochgeladen werden. Es wird ein Python-Bit verwendet, um die DICOM-Datei (als Bytes) vorab in den Arbeitsspeicher zu laden.  

Durch Übergeben eines Arrays von Dateien an den fields-Parameter von `encode_multipart_related` können mehrere Dateien in einem einzelnen POST hochgeladen werden. Es wird manchmal verwendet, um eine vollständige Reihe oder Studie hochzuladen. 

_Details:_
* Pfad:.. /studies/{study}
* Methode: POST
* Headers:
    * Akzeptieren: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * Autorisierung: Bearer $token"
* Hauptteil:
    * Inhaltstyp: Anwendung/Dicom für jede hochgeladene Datei, getrennt durch einen Begrenzungswert


```python

filepath_red = Path(path_to_dicoms_dir).joinpath('red-triangle.dcm')
filepath_green = Path(path_to_dicoms_dir).joinpath('green-square.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath_red,'rb') as reader:
    rawfile_red = reader.read()
with open(filepath_green,'rb') as reader:
    rawfile_green = reader.read()  
       
files = {'file_red': ('dicomfile', rawfile_red, 'application/dicom'),
         'file_green': ('dicomfile', rawfile_green, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```
### <a name="store-single-instance-non-standard"></a>Store einzelinstanz (nicht standardmäßig)

Im folgenden Codebeispiel wird veranschaulicht, wie eine einzelne DICOM-Datei hochgeladen wird. Es handelt sich um einen nicht standardmäßigen API-Endpunkt, der das Hochladen einer einzelnen Datei als binäre Bytes vereinfacht, die im Text einer Anforderung gesendet werden.

_Details:_
* Pfad:.. /studies
* Methode: POST
* Headers:
   *  Akzeptieren: application/dicom+json
   *  Content-Type: application/dicom
   *  Autorisierung: Bearer $token"
* Hauptteil:
    * Enthält eine einzelne DICOM-Datei als binäre Bytes.

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    body = reader.read()

headers = {'Accept':'application/dicom+json', 'Content-Type':'application/dicom', "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
response  # response should be a 409 Conflict if the file was already uploaded in the above request
```

## <a name="retrieve-dicom-instances-wado"></a>Abrufen von DICOM-Instanzen (WADO)

In den folgenden Beispielen wird das Abrufen von DICOM-Instanzen hervorgehoben.

### <a name="retrieve-all-instances-within-a-study"></a>Abrufen aller Instanzen innerhalb einer Studie

In diesem Beispiel werden alle Instanzen innerhalb einer einzelnen Studie abgerufen.

_Details:_
* Pfad:.. /studies/{study}
* Methode: GET
* Headers:
   * Akzeptieren: multipart/related; type="application/dicom"; transfer-syntax=*
   * Autorisierung: Bearer $token"

Alle drei dcm-Dateien, die wir zuvor hochgeladen haben, sind Teil derselben Studie, sodass die Antwort alle drei Instanzen zurückgeben sollte. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass alle drei Instanzen zurückgegeben werden.

```python
url = f'{base_url}/studies/{study_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="use-the-retrieved-instances"></a>Verwenden der abgerufenen Instanzen

Die -Instanzen werden als binäre Bytes abgerufen. Sie können die zurückgegebenen Elemente durchlaufen und die Bytes in eine Datei ähnlich konvertieren, die von gelesen werden `pydicom` kann.


```python
import requests_toolbelt as tb
from io import BytesIO

mpd = tb.MultipartDecoder.from_response(response)
for part in mpd.parts:
    # Note that the headers are returned as binary!
    print(part.headers[b'content-type'])
    
    # You can convert the binary body (of each part) into a pydicom DataSet
    #   And get direct access to the various underlying fields
    dcm = pydicom.dcmread(BytesIO(part.content))
    print(dcm.PatientName)
    print(dcm.SOPInstanceUID)
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>Abrufen von Metadaten aller untersuchten Instanzen

Diese Anforderung ruft die Metadaten für alle Instanzen innerhalb einer einzelnen Studie ab.

_Details:_
* Pfad:.. /studies/{study}/metadata
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer $token"

Alle drei `.dcm` Dateien, die wir zuvor hochgeladen haben, sind Teil derselben Studie, sodass die Antwort die Metadaten für alle drei Instanzen zurückgeben sollte. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass alle Metadaten zurückgegeben werden.

```python
url = f'{base_url}/studies/{study_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-all-instances-within-a-series"></a>Abrufen aller Instanzen innerhalb einer Reihe

Diese Anforderung ruft alle Instanzen innerhalb einer einzelnen Reihe ab.

_Details:_
* Pfad:.. /studies/{study}/series/{series}
* Methode: GET
* Headers:
   * Akzeptieren: multipart/related; type="application/dicom"; transfer-syntax=*
   * Autorisierung: Bearer $token"

Diese Reihe verfügt über zwei Instanzen (grünes Quadrat und rotes Dreieck), sodass die Antwort beide Instanzen zurückgeben sollte. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass beide Instanzen zurückgegeben werden.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-all-instances-in-series"></a>Abrufen von Metadaten aller Instanzen in Reihen

Diese Anforderung ruft die Metadaten für alle Instanzen innerhalb einer einzelnen Reihe ab.

_Details:_
* Pfad:.. /studies/{study}/series/{series}/metadata
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer $token"

Diese Reihe verfügt über zwei Instanzen (grünes Quadrat und rotes Dreieck), sodass die Antwort für beide Instanzen zurückgegeben werden sollte. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass beide Instanzmetadaten zurückgegeben werden.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>Abrufen einer einzelnen Instanz innerhalb einer Reihe von Studien

Diese Anforderung ruft eine einzelne -Instanz ab.

_Details:_
* Pfad:.. /studies/{study}/series{series}/instances/{instance}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom; transfer-syntax=*
   * Autorisierung: Bearer $token"

In diesem Codebeispiel sollte nur das rote Dreieck der Instanz zurückgegeben werden. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass die Instanz zurückgegeben wird.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
headers = {'Accept':'application/dicom; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>Abrufen von Metadaten einer einzelnen Instanz innerhalb einer Reihe von Studien

Diese Anforderung ruft die Metadaten für eine einzelne Instanz innerhalb einer einzelnen Studie und Reihe ab.

_Details:_
* Pfad:.. /studies/{study}/series/{series}/instances/{instance}/metadata
* Methode: GET
* Headers:
  * Akzeptieren: application/dicom+json
  * Autorisierung: Bearer $token"

In diesem Codebeispiel sollten nur die Metadaten für das rote Dreieck der Instanz zurückgegeben werden. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und dass die Metadaten zurückgegeben werden.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>Abrufen eines oder mehrerer Frames aus einer einzelnen Instanz

Diese Anforderung ruft einen oder mehrere Frames aus einer einzelnen Instanz ab.

_Details:_
* Pfad:.. /studies/{study}/series{series}/instances/{instance}/frames/1,2,3
* Methode: GET
* Headers:
   * Autorisierung: Bearer $token"
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1` (Standard) oder
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=*` oder
   * `Accept: multipart/related; type="application/octet-stream";`

Dieses Codebeispiel sollte den einzigen Frame aus dem roten Dreieck zurückgeben. Überprüfen Sie, ob die Antwort über den Statuscode OK verfügt und der Frame zurückgegeben wird.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/frames/1'
headers = {'Accept':'multipart/related; type="application/octet-stream"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

## <a name="query-dicom-qido"></a>Abfragen von DICOM (QIDO)

In den folgenden Beispielen suchen wir mithilfe ihrer eindeutigen Bezeichner nach Elementen. Sie können auch nach anderen Attributen wie PatientName suchen.

Unterstützte DICOM-Attribute finden Sie im Dokument [DICOM Conformance Statement (DICOM-Konformitätserklärung).](dicom-services-conformance-statement.md#supported-search-parameters)

### <a name="search-for-studies"></a>Suchen nach Studien

Diese Anforderung sucht nach mindestens einer Studie nach DICOM-Attributen.

_Details:_
* Pfad:.. /studies? StudyInstanceUID={study}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer $token"

Überprüfen Sie, ob die Antwort eine Studie enthält und ob der Antwortcode in Ordnung ist.

```python
url = f'{base_url}/studies'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'StudyInstanceUID':study_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series"></a>Suchen nach Reihen

Diese Anforderung sucht nach einer oder mehrere Reihen nach DICOM-Attributen.

_Details:_
* Pfad:.. /series? SeriesInstanceUID={series}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer $token"

Überprüfen Sie, ob die Antwort eine Reihe enthält und ob der Antwortcode OK ist.

```python
url = f'{base_url}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series-within-a-study"></a>Suchen nach Reihen innerhalb einer Studie

Diese Anforderung sucht nach einer oder mehrere Reihen innerhalb einer einzelnen Studie nach DICOM-Attributen.

_Details:_
* Pfad:.. /studies/{study}/series? SeriesInstanceUID={series}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer $token"

Überprüfen Sie, ob die Antwort eine Reihe enthält und ob der Antwortcode OK ist.

```python
url = f'{base_url}/studies/{study_uid}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances"></a>Suchen nach Instanzen

Diese Anforderung sucht nach einer oder mehrere Instanzen nach DICOM-Attributen.

_Details:_
* Pfad:.. /instances? SOPInstanceUID={instance}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer $token"

Überprüfen Sie, ob die Antwort eine Instanz enthält und ob der Antwortcode OK ist.

```python
url = f'{base_url}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study"></a>Suchen nach Instanzen innerhalb einer Studie

Diese Anforderung sucht nach einer oder mehrere Instanzen innerhalb einer einzelnen Studie nach DICOM-Attributen.

_Details:_
* Pfad:.. /studies/{study}/instances? SOPInstanceUID={instance}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer $token"

Überprüfen Sie, ob die Antwort eine Instanz enthält und ob der Antwortcode OK ist.

```python
url = f'{base_url}/studies/{study_uid}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study-and-series"></a>Suchen nach Instanzen innerhalb einer Studie und Reihe

Diese Anforderung sucht nach einer oder mehrere Instanzen innerhalb einer einzelnen Studie und einer einzelnen Reihe nach DICOM-Attributen.

_Details:_
* Pfad:.. /studies/{study}/series/{series}/instances? SOPInstanceUID={instance}
* Methode: GET
* Headers:
   * Akzeptieren: application/dicom+json
   * Autorisierung: Bearer $token"

Überprüfen Sie, ob die Antwort eine Instanz enthält und ob der Antwortcode OK ist.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances'
headers = {'Accept':'application/dicom+json'}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

## <a name="delete-dicom"></a>Löschen von DICOM

> [!NOTE]
> Löschen ist nicht Teil des DICOM-Standards, wurde aber der Einfachheit halber hinzugefügt.

Ein 204-Antwortcode wird zurückgegeben, wenn der Löschvorgang erfolgreich war. Ein 404-Antwortcode wird zurückgegeben, wenn die Elemente noch nie vorhanden oder bereits gelöscht wurden.

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>Löschen einer bestimmten Instanz innerhalb einer Studie und Reihe

Diese Anforderung löscht eine einzelne Instanz innerhalb einer einzelnen Studie und einer einzelnen Reihe.

_Details:_
* Pfad:.. /studies/{study}/series/{series}/instances/{instance}
* Methode: DELETE
* Headers:
   * Autorisierung: Bearer $token

Diese Anforderung löscht die Instanz des roten Dreiecks vom Server. Wenn dies erfolgreich ist, enthält der Antwortstatuscode keinen Inhalt.

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-series-within-a-study"></a>Löschen einer bestimmten Reihe innerhalb einer Studie

Diese Anforderung löscht eine einzelne Reihe (und alle untergeordneten Instanzen) innerhalb einer einzelnen Studie.

_Details:_
* Pfad:.. /studies/{study}/series/{series}
* Methode: DELETE
* Headers:
   * Autorisierung: Bearer $token

In diesem Codebeispiel wird die Grün-Quadrat-Instanz (es ist das einzige in der Reihe übrige Element) vom Server gelöscht. Wenn dies erfolgreich ist, wird der Antwortstatuscode nicht angezeigt.

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-study"></a>Löschen einer bestimmten Studie

Diese Anforderung löscht eine einzelne Studie (und alle untergeordneten Reihen und Instanzen).

_Details:_
* Pfad:.. /studies/{study}
* Methode: DELETE
* Headers:
   * Autorisierung: Bearer $token

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="next-steps"></a>Nächste Schritte

Informationen zum DICOM-Dienst finden Sie unter 

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
