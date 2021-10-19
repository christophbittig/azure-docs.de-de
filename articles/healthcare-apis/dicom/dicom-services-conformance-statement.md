---
title: DICOM-Konformitätserklärung für Azure Healthcare-APIs
description: Dieses Dokument enthält Details zur DICOM-Konformitätserklärung für Azure Healthcare-APIs.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/05/2021
ms.author: aersoy
ms.openlocfilehash: 32a368243ab2114799dd9ce9d2ffcdf3306115c9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044665"
---
# <a name="dicom-conformance-statement"></a>DICOM-Konformitätserklärung

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der **Azure API for DICOM-Dienst** unterstützt eine Teilmenge des DICOMweb-Standards. &trade; Diese Unterstützung umfasst Folgendes:

* [Store (STOW-RS)](#store-stow-rs)
* [Abrufen (WADO-RS)](#retrieve-wado-rs)
* [Suche (QIDO-RS)](#search-qido-rs)

Darüber hinaus werden die folgenden nicht standardmäßigen APIs unterstützt:

- [Löschen](#delete)

Unser Dienst nutzt auch die REST-API-Versionsierung. Informationen zum Angeben der Version beim Senden von Anforderungen finden Sie in der [Dokumentation zur API-Versionierung für den DICOM-Dienst.](api-versioning-dicom-service.md)

## <a name="store-stow-rs"></a>Store (STOW-RS)

Diese Transaktion verwendet die POST-Methode, um Darstellungen von Studien, Reihen und Instanzen zu speichern, die in der Anforderungsnutzlast enthalten sind.

| Methode | Pfad               | BESCHREIBUNG |
| :----- | :----------------- | :---------- |
| POST   | .. /studies         | Store -Instanzen. |
| POST   | .. /studies/{study} | Store Instanzen für eine bestimmte Studie. |

Der `study` Parameter entspricht dem DICOM-Attribut StudyInstanceUID. Wenn sie angegeben wird, wird jede Instanz, die nicht zur bereitgestellten Studie gehört, mit einem `43265` Warnungscode abgelehnt.

Die folgenden `Accept` Header für die Antwort werden unterstützt:

* `application/dicom+json`

Die folgenden `Content-Type` Header werden unterstützt:

* `multipart/related; type="application/dicom"`
* `application/dicom`

> [!NOTE]
> Der Server **ersingt** oder ersetzt keine Attribute, die mit vorhandenen Daten in Konflikt stehen. Alle Daten werden wie angegeben gespeichert.

Die folgenden DICOM-Elemente müssen in jeder DICOM-Datei vorhanden sein, die versucht, gespeichert zu werden:

* StudyInstanceUID
* SeriesInstanceUID
* SOPInstanceUID
* SOPClassUID
* Patienten-ID

> [!NOTE]
> Alle Bezeichner müssen zwischen 1 und 64 Zeichen lang sein und dürfen nur alphanumerische Zeichen oder die folgenden Sonderzeichen enthalten: ".", "-".

Jede gespeicherte Datei muss eine eindeutige Kombination aus StudyInstanceUID, SeriesInstanceUID und SopInstanceUID haben. Der `45070` Warnungscode wird zurückgegeben, wenn bereits eine Datei mit denselben Bezeichnern vorhanden ist.

DICOM-Dateigrößenlimit: Für eine DICOM-Datei gilt standardmäßig eine Größenbeschränkung von 2 GB.

### <a name="store-response-status-codes"></a>Store antwortstatuscodes

| Code                         | BESCHREIBUNG |
| :--------------------------- | :---------- |
| 200 (OK)                     | Alle SOP-Instanzen in der Anforderung wurden gespeichert. |
| 202 (Akzeptiert)               | Einige Instanzen in der Anforderung wurden gespeichert, andere sind jedoch fehlgeschlagen. |
| 204 (Kein Inhalt)             | In der Anforderung der Speichertransaktion wurde kein Inhalt bereitgestellt. |
| 400 (Ungültige Anforderung)            | Die Anforderung wurde schlecht formatiert. Beispielsweise entspricht der bereitgestellte Bezeichner der Studieinstanz nicht dem erwarteten UID-Format. |
| 401 (Nicht autorisiert)           | Der Client ist nicht authentifiziert. |
| 403 (Unzulässig)              | Der Benutzer ist nicht autorisiert. |
| 406 (nicht akzeptabel)         | Der angegebene `Accept` Header wird nicht unterstützt. |
| 409 (Konflikt)               | Keine der Instanzen in der Speichertransaktionsanforderung wurde gespeichert. |
| 415 (Nicht unterstützter Medientyp) | Die `Content-Type` bereitgestellte wird nicht unterstützt. |
| 503 (Dienst nicht verfügbar)    | Der Dienst ist nicht verfügbar oder ausgelastet. Versuchen Sie es später noch mal. |

### <a name="store-response-payload"></a>Store Antwortnutzlast

Die Antwortnutzlast füllt ein DICOM-Dataset mit den folgenden Elementen auf:

| Tag          | Name                  | BESCHREIBUNG |
| :----------- | :-------------------- | :---------- |
| (0008, 1190) | RetrieveURL           | Die Abruf-URL der Studie, wenn studyInstanceUID in der Speicheranforderung angegeben wurde und mindestens eine Instanz erfolgreich gespeichert wurde. |
| (0008, 1198) | FailedSOPSequence     | Die Sequenz der Instanzen, die nicht gespeichert werden konnten. |
| (0008, 1199) | ReferencedSOPSequence | Die Sequenz der gespeicherten Instanzen. |

Jedes Dataset in der enthält die folgenden Elemente (wenn die zu speichernde `FailedSOPSequence` DICOM-Datei gelesen werden kann):

| Tag          | Name                     | BESCHREIBUNG |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | Der eindeutige Bezeichner der SOP-Klasse der Instanz, die nicht gespeichert werden konnte. |
| (0008, 1150) | ReferencedSOPInstanceUID | Der eindeutige Bezeichner der SOP-Instanz der Instanz, die nicht gespeichert werden konnte. |
| (0008, 1197) | FailureReason            | Der Grundcode, warum diese Instanz nicht gespeichert werden konnte. |

Jedes Dataset in `ReferencedSOPSequence` der enthält die folgenden Elemente:

| Tag          | Name                     | BESCHREIBUNG |
| :----------- | :----------------------- | :---------- |
| (0008, 1150) | ReferencedSOPClassUID    | Der eindeutige Bezeichner der SOP-Klasse der Instanz, die nicht gespeichert werden konnte. |
| (0008, 1150) | ReferencedSOPInstanceUID | Der eindeutige Bezeichner der SOP-Instanz der Instanz, die nicht gespeichert werden konnte. |
| (0008, 1190) | RetrieveURL              | Die Abruf-URL dieser Instanz auf dem DICOM-Server. |

Im Folgenden finden Sie eine Beispielantwort mit `Accept` dem Header `application/dicom+json` :

```json
{
  "00081190":
  {
    "vr":"UR",
    "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232"]
  },
  "00081198":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI","Value":["cd70f89a-05bc-4dab-b6b8-1f3d2fcafeec"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["22c35d16-11ce-43fa-8f86-90ceed6cf4e7"]
      },
      "00081197":
      {
        "vr":"US",
        "Value":[43265]
      }
    }]
  },
  "00081199":
  {
    "vr":"SQ",
    "Value":
    [{
      "00081150":
      {
        "vr":"UI",
        "Value":["d246deb5-18c8-4336-a591-aeb6f8596664"]
      },
      "00081155":
      {
        "vr":"UI",
        "Value":["4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      },
      "00081190":
      {
        "vr":"UR",
        "Value":["http://localhost/studies/d09e8215-e1e1-4c7a-8496-b4f6641ed232/series/8c4915f5-cc54-4e50-aa1f-9b06f6e58485/instances/4a858cbb-a71f-4c01-b9b5-85f88b031365"]
      }
    }]
  }
}
```

### <a name="store-failure-reason-codes"></a>Store von Fehlergrundcodes

| Code  | BESCHREIBUNG |
| :---- | :---------- |
| 272   | Die Speichertransaktion hat die Instanz aufgrund eines allgemeinen Fehlers bei der Verarbeitung des Vorgangs nicht gespeichert. |
| 43264 | Bei der DICOM-Instanz ist die Überprüfung fehlgeschlagen. |
| 43265 | Die bereitgestellte Instanz StudyInstanceUID hat nicht mit der angegebenen StudyInstanceUID in der Store-Anforderung übereingelangen. |
| 45070 | Eine DICOM-Instanz mit denselben StudyInstanceUID, SeriesInstanceUID und SopInstanceUID wurde bereits gespeichert. Wenn Sie den Inhalt aktualisieren möchten, löschen Sie zuerst diese Instanz. |
| 45071 | Eine DICOM-Instanz wird von einem anderen Prozess erstellt, oder der vorherige Erstellungsversuch ist fehlgeschlagen, und der Bereinigungsprozess konnte noch nicht bereinigt werden. Löschen Sie zuerst die Instanz, bevor Sie versuchen, erneut zu erstellen. |

## <a name="retrieve-wado-rs"></a>Abrufen (WADO-RS)

Diese Retrieve Transaction bietet Unterstützung für das Abrufen gespeicherter Studien, Reihen, Instanzen und Frames als Referenz.

| Methode | Pfad                                                                    | BESCHREIBUNG |
| :----- | :---------------------------------------------------------------------- | :---------- |
| GET    | .. /studies/{study}                                                      | Ruft alle Instanzen innerhalb einer Studie ab. |
| GET    | .. /studies/{study}/metadata                                             | Ruft die Metadaten für alle Instanzen innerhalb einer Studie ab. |
| GET    | .. /studies/{study}/series/{series}                                      | Ruft alle Instanzen innerhalb einer Reihe ab. |
| GET    | .. /studies/{study}/series/{series}/metadata                             | Ruft die Metadaten für alle Instanzen innerhalb einer Reihe ab. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}                 | Ruft eine einzelne -Instanz ab. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}/metadata        | Ruft die Metadaten für eine einzelne Instanz ab. |
| GET    | .. /studies/{study}/series/{series}/instances/{instance}/frames/{frames} | Ruft einen oder mehrere Frames aus einer einzelnen Instanz ab. Um mehr als einen Frame anzugeben, verwenden Sie ein Komma, um jeden zurückkommenden Frame zu trennen. Beispiel: /studies/1/series/2/instance/3/frames/4,5,6 |

### <a name="retrieve-instances-within-study-or-series"></a>Abrufen von Instanzen innerhalb einer Studie oder Reihe

Die folgenden Header werden zum Abrufen von Instanzen innerhalb einer Studie `Accept` oder einer Reihe unterstützt:


* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `multipart/related; type="application/dicom";` (Wenn transfer-syntax nicht angegeben ist, wird standardmäßig 1.2.840.10008.1.2.1 verwendet.)
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-an-instance"></a>Abrufen einer Instanz

Die folgenden `Accept` Header werden zum Abrufen einer bestimmten Instanz unterstützt:

* `application/dicom; transfer-syntax=*`
* `multipart/related; type="application/dicom"; transfer-syntax=*`
* `application/dicom;` (Wenn transfer-syntax nicht angegeben ist, wird standardmäßig 1.2.840.10008.1.2.1 verwendet.)
* `multipart/related; type="application/dicom"` (Wenn transfer-syntax nicht angegeben ist, wird standardmäßig 1.2.840.10008.1.2.1 verwendet.)
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.1`
* `application/dicom; transfer-syntax=1.2.840.10008.1.2.4.90`
* `multipart/related; type="application/dicom"; transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-frames"></a>Abrufen von Frames

Die folgenden `Accept` Header werden zum Abrufen von Frames unterstützt:

* `multipart/related; type="application/octet-stream"; transfer-syntax=*`
* `multipart/related; type="application/octet-stream";` (Wenn transfer-syntax nicht angegeben ist, wird standardmäßig 1.2.840.10008.1.2.1 verwendet.)
* `multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1`
* `multipart/related; type="image/jp2";` (Wenn transfer-syntax nicht angegeben ist, wird standardmäßig 1.2.840.10008.1.2.4.90 verwendet.)
* `multipart/related; type="image/jp2";transfer-syntax=1.2.840.10008.1.2.4.90`

### <a name="retrieve-transfer-syntax"></a>Abrufen der Übertragungssyntax

Wenn sich die angeforderte Übertragungssyntax von der ursprünglichen Datei unterscheiden, wird die ursprüngliche Datei in die angeforderte Übertragungssyntax transcodiert. Die ursprüngliche Datei muss eines der folgenden Formate sein, damit die Transcodierung erfolgreich ist. Andernfalls kann die Transcodierung fehlschlagen:

* 1.2.840.10008.1.2 (Little Endian Implicit)
* 1.2.840.10008.1.2.1 (Little Endian Explicit)
* 1.2.840.10008.1.2.2 (Explizites VR Big Endian)
* 1.2.840.10008.1.2.4.50 (JPEG-Baselineprozess 1)
* 1.2.840.10008.1.2.4.57 (JPEG Lossless)
* 1.2.840.10008.1.2.4.70 (JPEG Lossless Selection Value 1)
* 1.2.840.10008.1.2.4.90 (nur JPEG 2000 Verlustlos)
* 1.2.840.10008.1.2.4.91 (JPEG 2000)
* 1.2.840.10008.1.2.5 (RLE Lossless)

Eine nicht unterstützte `transfer-syntax` führt zu `406 Not Acceptable` .

### <a name="retrieve-metadata-for-study-series-or-instance"></a>Abrufen von Metadaten (für Studie, Reihe oder Instanz)

Die folgenden Header werden zum Abrufen von Metadaten für eine Studie, eine Reihe oder `Accept` eine Instanz unterstützt:

* `application/dicom+json`

Beim Abrufen von Metadaten werden keine Attribute mit den folgenden Wertdarstellungen zurückgeben:

| VR-Name | BESCHREIBUNG            |
| :------ | :--------------------- |
| OB      | Anderes Byte             |
| OD      | Other Double           |
| OF      | Andere Gleitkomma            |
| OL      | Other Long             |
| OV      | Andere 64-Bit sehr lang |
| OW      | Anderes Wort             |
| UN      | Unbekannt                |

### <a name="retrieve-metadata-cache-validation-for-study-series-or-instance"></a>Abrufen der Überprüfung des Metadatencaches für (Studie, Reihe oder Instanz)

Die Cachevalidierung wird mithilfe des -Mechanismus `ETag` unterstützt. In der Antwort auf eine Metadatenanforderung wird ETag als einer der Header zurückgegeben. Dieses ETag kann in den späteren Anforderungen für dieselben Metadaten zwischengespeichert und als Header `If-None-Match` hinzugefügt werden. Wenn die Daten vorhanden sind, sind zwei Arten von Antworten möglich:

* Daten haben sich seit der letzten Anforderung nicht geändert: Die HTTP 304-Antwort (Nicht geändert) wird ohne Antwortkörper gesendet.
* Daten haben sich seit der letzten Anforderung geändert: Die HTTP 200-Antwort (OK) wird mit dem aktualisierten ETag gesendet. Erforderliche Daten werden auch als Teil des Textkörpers zurückgegeben.

### <a name="retrieve-response-status-codes"></a>Abrufen von Antwortstatuscodes

| Code                         | BESCHREIBUNG |
| :--------------------------- | :---------- |
| 200 (OK)                     | Alle angeforderten Daten wurden abgerufen. |
| 304 (Nicht geändert)           | Die angeforderten Daten wurden seit der letzten Anforderung nicht geändert. Inhalt wird in diesem Fall nicht zum Antwortkörper hinzugefügt. Weitere Informationen finden Sie im abschnitt Abrufen der **Überprüfung des Metadatencaches (für Studie, Reihe oder Instanz).** |
| 400 (Ungültige Anforderung)            | Die Anforderung wurde schlecht formatiert. Beispielsweise entspricht der bereitgestellte Bezeichner der Studieinstanz nicht dem erwarteten UID-Format, oder die angeforderte Übertragungssyntaxcodierung wird nicht unterstützt. |
| 401 (Nicht autorisiert)           | Der Client ist nicht authentifiziert. |
| 403 (Unzulässig)              | Der Benutzer ist nicht autorisiert. |
| 404 (Nicht gefunden)              | Die angegebene DICOM-Ressource wurde nicht gefunden. |
| 406 (nicht akzeptabel)         | Der angegebene `Accept` Header wird nicht unterstützt. |
| 503 (Dienst nicht verfügbar)    | Der Dienst ist nicht verfügbar oder ausgelastet. Versuchen Sie es später noch mal. |

## <a name="search-qido-rs"></a>Suche (QIDO-RS)

Abfragen basierend auf der ID für DICOM-Objekte (QIDO) ermöglichen es Ihnen, nach Studien, Reihen und Instanzen nach Attributen zu suchen.

| Methode | Pfad                                            | BESCHREIBUNG                       |
| :----- | :---------------------------------------------- | :-------------------------------- |
| *Suchen nach Studien*                                                                         |
| GET    | .. /studies?...                                  | Suchen nach Studien                |
| *Suchen nach Reihen*                                                                          |
| GET    | .. /series?...                                   | Suchen nach Reihen                 |
| GET    |.. /studies/{study}/series?...                    | Suchen nach Reihen in einer Studie      |
| *Suchen nach Instanzen*                                                                       |
| GET    |.. /instances?...                                 | Suchen nach Instanzen              |
| GET    |.. /studies/{study}/instances?...                 | Suchen nach Instanzen in einer Studie   |
| GET    |.. /studies/{study}/series/{series}/instances?... | Suchen nach Instanzen in einer Reihe  |

Die folgenden `Accept` Header werden für die Suche unterstützt:

- `application/dicom+json`

### <a name="supported-search-parameters"></a>Unterstützte Suchparameter

Die folgenden Parameter für jede Abfrage werden unterstützt:

| Schlüssel              | Unterstützungswert(n)              | Zulässige Anzahl | BESCHREIBUNG |
| :--------------- | :---------------------------- | :------------ | :---------- |
| `{attributeID}=` | {value}                       | 0...N         | Suchen Sie in der Abfrage nach einem Attribut-Wert-Abgleich. |
| `includefield=`  | `{attributeID}`<br/>`all`   | 0...N         | Die zusätzlichen Attribute, die in der Antwort zurückgeben werden. Öffentliche und private Tags werden unterstützt.<br/>Wenn `all` bereitgestellt wird. Weitere Informationen [dazu,](#search-response) welche Attribute für jeden Abfragetyp zurückgegeben werden, finden Sie unter Suchantwort.<br/>Wenn eine Mischung aus {attributeID} und "all" bereitgestellt wird, verwendet der Server standardmäßig "all". |
| `limit=`         | {value}                       | 0..1          | Ganzzahliger Wert, um die Anzahl der in der Antwort zurückgegebenen Werte zu begrenzen.<br/>Der Wert kann zwischen dem Bereich 1 und >= x <= 200 liegen. Der Standardwert ist 100. |
| `offset=`        | {value}                       | 0..1          | Überspringen Sie {value}-Ergebnisse.<br/>Wenn ein Offset bereitgestellt wird, der größer als die Anzahl der Suchergebnisse ist, wird eine 204-Antwort (kein Inhalt) zurückgegeben. |
| `fuzzymatching=` | `true` \| `false`             | 0..1          | Wenn true fuzzy matching auf das PatientName-Attribut angewendet wird. Es wird eine Präfixwort-Übereinstimmung mit einem beliebigen Namensteil innerhalb des PatientName-Werts erstellt. Wenn PatientName beispielsweise "John^Doe" ist, werden "joh", "do", "jo do", "Doe" und "John Doe" übereinstimmen. "ohn" wird jedoch nicht übereinstimmen. |

#### <a name="searchable-attributes"></a>Durchsuchbare Attribute

Wir unterstützen die Suche nach den folgenden Attributen und Suchtypen.

| Attribute-Schlüsselwort | Studie | Reihen | Instanz |
| :---------------- | :---: | :----: | :------: |
| StudyInstanceUID | X | X | X |
| PatientName | X | X | X |
| Patienten-ID | X | X | X |
| NumberNumber | X | X | X |
| ReferringPhysicianName | X | X | X |
| StudyDate | X | X | X |
| StudyDescription | X | X | X |
| SeriesInstanceUID |  | X | X |
| Modalität |  | X | X |
| PerformedProcedureStepStartDate |  | X | X |
| SOPInstanceUID |  |  | X |

#### <a name="search-matching"></a>Suchabgleich

Wir unterstützen die folgenden übereinstimmenden Typen.

| Suchtyp | Unterstütztes Attribut | Beispiel |
| :---------- | :------------------ | :------ |
| Bereichsabfrage | StudyDate | {attributeID}={value1}-{value2}. Für Datums-/Uhrzeitwerte wurde ein inklusiver Bereich für das Tag unterstützt. Dieser wird `attributeID >= {value1} AND attributeID <= {value2}` zugeordnet. |
| Genaue Übereinstimmung | Alle unterstützten Attribute | {attributeID}={value1} |
| Fuzzy-Übereinstimmung | PatientName | Entspricht jeder Komponente des Patientennamens, die mit dem Wert beginnt. |

#### <a name="attribute-id"></a>Attribut-ID

Tags können für den Abfrageparameter auf unterschiedliche Weise codiert werden. Wir haben den In [PS3.18 6.7.1.1.1](http://dicom.nema.org/medical/dicom/2019a/output/chtml/part18/sect_6.7.html#sect_6.7.1.1.1)definierten Standard teilweise implementiert. Die folgenden Codierungen für ein Tag werden unterstützt:

| Wert            | Beispiel          |
| :--------------- | :--------------- |
| {group} {element} | 0020000D         |
| {dicomKeyword}   | StudyInstanceUID |

Beispielabfrage, die nach Instanzen sucht: **.. /instances? Modaly=CT&00280011=512&includefield=00280010&limit=5&offset=0**

### <a name="search-response"></a>Suchantwort

Die Antwort ist ein Array von DICOM-Datasets. Abhängig von der Ressource werden *standardmäßig* die folgenden Attribute zurückgegeben:

#### <a name="default-study-tags"></a>Standardtags für Studie

| Tag          | Attributname |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0020) | StudyDate |
| (0008, 0030) | StudyTime |
| (0008, 0050) | Number |
| (0008, 0056) | InstanceAvailability |
| (0008, 0090) | ReferringPhysicianName |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0010, 0010) | PatientName |
| (0010, 0020) | Patienten-ID |
| (0010, 0030) | PatientBirthDate |
| (0010, 0040) | PatientSex |
| (0020, 0010) | StudyID |
| (0020, 000D) | StudyInstanceUID |

#### <a name="default-series-tags"></a>Standardreihentags

| Tag          | Attributname |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0060) | Modalität |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0008, 103E) | SeriesDescription |
| (0020, 000E) | SeriesInstanceUID |
| (0040, 0244) | PerformedProcedureStepStartDate |
| (0040, 0245) | PerformedProcedureStepStartTime |
| (0040, 0275) | RequestAttributesSequence |

#### <a name="default-instance-tags"></a>Standardinstanztags

| Tag          | Attributname |
| :----------- | :------------- |
| (0008, 0005) | SpecificCharacterSet |
| (0008, 0016) | SOPClassUID |
| (0008, 0018) | SOPInstanceUID |
| (0008, 0056) | InstanceAvailability |
| (0008, 0201) | TimezoneOffsetFromUTC |
| (0020, 0013) | InstanceNumber |
| (0028, 0010) | Zeilen |
| (0028, 0011) | Spalten |
| (0028, 0100) | BitsAllocated |
| (0028, 0008) | NumberOfFrames |

Wenn includefield=all angegeben ist, werden die folgenden Attribute zusammen mit den Standardattributen eingeschlossen. Zusammen mit den Standardattributen ist dies die vollständige Liste der Attribute, die auf jeder Ressourcenebene unterstützt werden.

#### <a name="other-study-tags"></a>Andere Studiestags

| Tag          | Attributname |
| :----------- | :------------- |
| (0008, 1030) | Beschreibung der Studie |
| (0008, 0063) | AnatomicRegionsInStudyCodeSequence |
| (0008, 1032) | ProcedureCodeSequence |
| (0008, 1060) | NameOfPhysiciansReadingStudy |
| (0008, 1080) | Zugegebene DiagnosenDeskription |
| (0008, 1110) | ReferencedStudySequence |
| (0010, 1010) | PatientAge |
| (0010, 1020) | PatientSize |
| (0010, 1030) | PatientWeight |
| (0010, 2180) | Occupation |
| (0010, 21B0) | AdditionalPatientHistory |

#### <a name="other-series-tags"></a>Andere Reihentags

| Tag          | Attributname |
| :----------- | :------------- |
| (0020, 0011) | SeriesNumber |
| (0020, 0060) | Laterality |
| (0008, 0021) | SeriesDate |
| (0008, 0031) | SeriesTime |

Die folgenden Attribute werden zurückgegeben:

* Alle stimmen mit Abfrageparametern und UIDs in der Ressourcen-URL überein.
* IncludeField-Attribute, die auf dieser Ressourcenebene unterstützt werden. 
* Wenn die Zielressource All Series ist, werden auch Attribute auf Studienebene zurückgegeben.
* Wenn es sich bei der Zielressource um Alle Instanzen handelt, werden auch Die Attribute auf Lern- und Reihenebene zurückgegeben.
* Wenn es sich bei der Zielressource um Die Instanzen von Study handelt, werden auch Attribute auf Reihenebene zurückgegeben.

### <a name="search-response-codes"></a>Suchantwortcodes

Die Abfrage-API gibt einen der folgenden Statuscodes in der Antwort zurück:

| Code                      | BESCHREIBUNG |
| :------------------------ | :---------- |
| 200 (OK)                  | Die Antwortnutzlast enthält alle übereinstimmenden Ressourcen. |
| 204 (Kein Inhalt)          | Die Suche wurde erfolgreich abgeschlossen, aber es wurden keine Ergebnisse zurückgegeben. |
| 400 (Ungültige Anforderung)         | Der Server konnte die Abfrage nicht ausführen, da die Abfragekomponente ungültig war. Der Antworttext enthält Details zum Fehler. |
| 401 (Nicht autorisiert)        | Der Client ist nicht authentifiziert. |
| 403 (Unzulässig)           | Der Benutzer ist nicht autorisiert. |
| 503 (Dienst nicht verfügbar) | Der Dienst ist nicht verfügbar oder ausgelastet. Versuchen Sie es später noch mal. |

### <a name="extra-notes"></a>Zusätzliche Hinweise

* Abfragen mithilfe von `TimezoneOffsetFromUTC` ( ) werden nicht `00080201` unterstützt.
* Die Abfrage-API gibt nicht 413 zurück (Anforderungsentität zu groß). Wenn der Grenzwert für die angeforderte Abfrageantwort außerhalb des zulässigen Bereichs liegt, wird eine ungültige Anforderung zurückgegeben. Alles, was innerhalb des zulässigen Bereichs angefordert wird, wird aufgelöst.
* Wenn es sich bei der Zielressource um "study/series" handelt, besteht ein Potenzial für inkonsistente Metadaten auf Untersuchungs-/Reihenebene über mehrere Instanzen hinweg. Beispielsweise können zwei Instanzen einen anderen patientName haben. In diesem Fall wird die neueste -Version gewonnen, und Sie können nur nach den neuesten Daten suchen.
* Ausgelagerungsergebnisse werden optimiert, um zuerst die *übereinstimmende neueste* Instanz zurückzugeben. Dies kann zu doppelten Datensätzen auf nachfolgenden Seiten führen, wenn neuere Daten hinzugefügt wurden, die der Abfrage entsprechen.
* Beim Abgleich wird die Groß-/Kleinschreibung beachtet, und bei PN-VR-Typen wird nach Akzent unterschieden.
* Beim Abgleich wird die Groß-/Kleinschreibung beachtet und bei anderen Zeichenfolgen-VR-Typen nach Akzent unterschieden.

## <a name="delete"></a>Löschen

Diese Transaktion ist nicht Teil des offiziellen DICOMweb-Standards. &trade; Er verwendet die DELETE-Methode, um Darstellungen von Studien, Reihen und Instanzen aus dem Speicher zu entfernen.

| Methode | Pfad                                                    | BESCHREIBUNG |
| :----- | :------------------------------------------------------ | :---------- |
| Delete | .. /studies/{study}                                      | Löschen Sie alle Instanzen für eine bestimmte Studie. |
| Delete | .. /studies/{study}/series/{series}                      | Löschen Sie alle Instanzen für eine bestimmte Reihe innerhalb einer Studie. |
| Delete | .. /studies/{study}/series/{series}/instances/{instance} | Löscht eine bestimmte Instanz innerhalb einer Reihe. |

Die `study` Parameter , und entsprechen den DICOM-Attributen `series` `instance` StudyInstanceUID, SeriesInstanceUID bzw. SopInstanceUID.

Es gibt keine Einschränkungen für den `Accept` Header-, `Content-Type` Header- oder Textinhalt der Anforderung.

> [!NOTE]
> Nach einer Delete-Transaktion können die gelöschten Instanzen nicht wiederhergestellt werden.

### <a name="response-status-codes"></a>Antwortstatuscodes

| Code                         | BESCHREIBUNG |
| :--------------------------- | :---------- |
| 204 (Kein Inhalt)             | Wenn alle SOP-Instanzen gelöscht wurden. |
| 400 (Ungültige Anforderung)            | Die Anforderung wurde schlecht formatiert. |
| 401 (Nicht autorisiert)           | Der Client ist nicht authentifiziert. |
| 403 (Unzulässig)              | Der Benutzer ist nicht autorisiert. |
| 404 (Nicht gefunden)              | Wenn die angegebene Reihe in einer Studie nicht gefunden wurde oder die angegebene Instanz nicht innerhalb der Reihe gefunden wurde. |
| 503 (Dienst nicht verfügbar)    | Der Dienst ist nicht verfügbar oder ausgelastet. Versuchen Sie es später noch mal. |

### <a name="delete-response-payload"></a>Löschen der Antwortnutzlast

Der Antwortkörper ist leer. Der Statuscode ist die einzige nützliche Information, die zurückgegeben wird.

### <a name="next-steps"></a>Nächste Schritte

Informationen zum DICOM-Dienst finden Sie unter 

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
