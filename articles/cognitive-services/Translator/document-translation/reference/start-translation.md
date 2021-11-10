---
title: Starten der Übersetzung
titleSuffix: Azure Cognitive Services
description: Übermitteln Sie eine Anforderung zur Übersetzung eines Dokuments mit dem Dienst Document Translation.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/22/2021
ms.openlocfilehash: eaa12d4fff9331f5170a2a7b7d28b5ebb4d06a74
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061001"
---
# <a name="start-translation"></a>Starten der Übersetzung

Verwenden Sie diese API, um eine Übersetzungsanforderung mit dem Dokumentübersetzungsdienst zu starten. Jede Anforderung kann mehrere Dokumente enthalten und muss einen Quell- und einen Zielcontainer für jedes Dokument enthalten.

Der Präfix- und Suffixfilter (falls angegeben) wird zum Filtern von Ordnern verwendet. Das Präfix wird auf den Unterpfad nach dem Containernamen angewendet.

Glossare/Translation Memory können in die Anforderung aufgenommen werden und werden vom Dienst angewendet, wenn das Dokument übersetzt wird.

Wenn das Glossar während der Übersetzung ungültig ist oder nicht erreichbar ist, wird im Dokumentstatus ein Fehler ausgegeben. Wenn am Ziel bereits eine Datei mit dem gleichen Namen vorhanden ist, tritt beim Auftrag ein Fehler auf. Die Ziel-URL für die einzelnen Zielsprachen muss jeweils eindeutig sein.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches
```

Erfahren Sie, wie Sie Ihren [benutzerdefinierten Domänennamen](../get-started-with-document-translation.md#find-your-custom-domain-name)finden.

> [!IMPORTANT]
>
> * **Für alle API-Anforderungen an den Dienst für die Dokumentübersetzung muss ein Endpunkt einer benutzerdefinierten Domäne verwendet werden**.
> * Zum Senden von HTTP-Anforderungen für die Dokumentübersetzung verwenden Sie nicht den Endpunkt, der im Azure-Portal auf der Ressourcenseite _Schlüssel und Endpunkt_ angegeben ist, und auch nicht den globalen Übersetzungsendpunkt `api.cognitive.microsofttranslator.com`.

## <a name="request-headers"></a>Anforderungsheader

Anforderungsheader:

|Header|BESCHREIBUNG|
|--- |--- |
|Ocp-Apim-Subscription-Key|Erforderlicher Anforderungsheader|

## <a name="request-body-batch-submission-request"></a>Anforderungstext: Batchübermittlungsanforderung

|Name|type|Beschreibung|
|--- |--- |--- |
|inputs|BatchRequest[]|Batchanforderung unten aufgeführt. Die Eingabeliste der Dokumente oder Ordner, die Dokumente enthalten. Medientypen: „application/json“, „text/json“, „application/*+json“.|

### <a name="inputs"></a>Eingaben

Definition für die Eingabe Batchübersetzungsanforderung.

|Name|type|Erforderlich|Beschreibung|
|--- |--- |--- |--- |
|source|SourceInput[]|Richtig|inputs.source unten aufgeführt. Quelle der Eingabedokumente.|
|Speichertyp|StorageInputType[]|Falsch|inputs.storageType unten aufgeführt. Der Speichertyp der Quellzeichenfolge der Eingabedokumente. Nur für die Übersetzung einzelner Dokumente erforderlich.|
|Ziele|TargetInput[]|Richtig|inputs.target unten aufgeführt. Speicherort des Ziels für die Ausgabe.|

**inputs.source**

Quelle der Eingabedokumente.

|Name|type|Erforderlich|Beschreibung|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Falsch|DocumentFilter[] unten aufgeführt.|
|filter.prefix|Zeichenfolge|Falsch|Eine Präfixzeichenfolge mit Beachtung der Groß-/Kleinschreibung zum Filtern von Dokumenten im Quellpfad für die Übersetzung. Wenn Sie beispielsweise einen Azure Storage-Blob-URI verwenden, können Sie das Präfix nutzen, um Unterordner die Übersetzung einzuschränken.|
|filter.suffix|Zeichenfolge|Falsch|Eine Präfixzeichenfolge mit Beachtung der Groß-/Kleinschreibung zum Filtern von Dokumenten im Quellpfad für die Übersetzung. Dies wird am häufigsten für Dateierweiterungen verwendet.|
|language|Zeichenfolge|Falsch|Sprachcode, wenn kein Wert angegeben ist, wird die automatische Erkennung für das Dokument durchgeführt.|
|sourceUrl|Zeichenfolge|True|Speicherort des Ordners bzw. Containers oder einer einzelnen Datei mit Ihren Dokumenten.|
|storageSource|StorageSource|Falsch|StorageSource unten aufgeführt.|
|storageSource.AzureBlob|Zeichenfolge|Falsch||

**inputs.storageType**

Der Speichertyp der Quellzeichenfolge der Eingabedokumente.

|Name|Typ|
|--- |--- |
|file|Zeichenfolge|
|folder|Zeichenfolge|

**inputs.target**

Das Ziel für die abgeschlossenen übersetzten Dokumente.

|Name|type|Erforderlich|Beschreibung|
|--- |--- |--- |--- |
|category|Zeichenfolge|Falsch|Kategorie/benutzerdefiniertes System für Übersetzungsanforderung.|
|Glossare|Glossary[]|Falsch|Glossar unten aufgeführt. Liste der Glossare.|
|glossaries.format|Zeichenfolge|Falsch|Formatieren.|
|glossaries.glossaryUrl|Zeichenfolge|True (bei Verwendung von Glossaren)|Speicherort des Glossars. Wir verwenden die Dateierweiterung zum Extrahieren der Formatierung, wenn der Formatparameter nicht bereitgestellt wird. Wenn das Übersetzungssprachpaar nicht im Glossar vorhanden ist, wird es nicht angewendet.|
|glossaries.storageSource|StorageSource|Falsch|StorageSource oben aufgeführt.|
|glossaries.version|Zeichenfolge|Falsch|Optionale Version. Ohne eine Angabe wird der Standardwert verwendet.|
|targetUrl|Zeichenfolge|True|Speicherort des Ordners/Containers mit Ihren Dokumenten.|
|language|Zeichenfolge|True|Der Zielsprachencode mit zwei Buchstaben. Siehe [Liste der Sprachcodes](../../language-support.md).|
|storageSource|StorageSource []|Falsch|StorageSource [] oben aufgeführt.|

## <a name="example-request"></a>Beispielanforderung

Im Folgenden werden Beispiele für Batchanforderungen aufgeführt:

**Übersetzen aller Dokumente in einem Container**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Übersetzen aller Dokumente in einem Container, unter Verwendung von Glossaren**

Stellen Sie sicher, dass Sie Glossar-URL- und SAS-Token für das spezifische Blob/Dokument (nicht für den Container) erstellt haben.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr",
                    "glossaries": [
                        {
                            "glossaryUrl": "https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D",
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Übersetzen eines bestimmten Ordners in einen Container**

Stellen Sie sicher, dass Sie den Ordnernamen (Groß-/Kleinschreibung beachten) als Präfix im Filter angegeben haben, obwohl das SAS-Token weiterhin für den Container gilt.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D",
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Übersetzen eines bestimmten Dokuments in einen Container**

* Stellen Sie sicher, dass Sie „storageType“: „File“ angegeben haben.
* Stellen Sie sicher, dass Sie Glossar-URL- und SAS-Token für das spezifische Blob/Dokument (nicht für den Container) erstellt haben.
* Stellen Sie sicher, dass Sie den Zieldateinamen als Teil der Ziel-URL angegeben haben – obwohl das SAS-Token für den Container noch immer vorhanden ist.
* Die Beispielanforderung unten zeigt, wie ein einzelnes Dokument in zwei Zielsprachen übersetzt wird.

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Antwortstatuscodes

Im Folgenden finden Sie die möglichen HTTP-Statuscodes, die eine Anforderung zurückgeben kann.

|Statuscode|BESCHREIBUNG|
|--- |--- |
|202|Akzeptiert: Die Anforderung wurde erfolgreich ausgeführt und die Batchanforderung wird vom Dienst erstellt. Die Header-Operation-Location gibt eine Status-URL mit der Vorgangs-ID „HeadersOperation-Location: string“ an|
|400|Ungültige Anforderung; Ungültige Anforderung. Eingabeparameter prüfen.|
|401|Nicht autorisiert. Anmeldeinformationen prüfen.|
|429|Die Anforderungsrate ist zu hoch.|
|500|Interner Serverfehler.|
|503|Dienst ist derzeit nicht verfügbar.  Versuchen Sie es später noch mal.|
|Andere Statuscodes|<ul><li>Zu viele Anforderungen</li><li>Server vorübergehend nicht verfügbar</li></ul>|

## <a name="error-response"></a>Fehlerantwort

|Name|type|BESCHREIBUNG|
|--- |--- |--- |
|code|Zeichenfolge|Enumerationen, die High-Level-Fehlercodes enthalten. Mögliche Werte:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nicht autorisiert</li></ul>|
|message|Zeichenfolge|Ruft High-Level-Fehlermeldung ab.|
|innerError|InnerTranslationError|Neues internes Fehlerformat, das Cognitive Services API-Richtlinien entspricht. Enthält die erforderlichen Eigenschaften ErrorCode, Message und Optional Properties Target, Details (Key Value Pair), Inner Error (kann geschachtelt werden).|
|inner.Errorcode|Zeichenfolge|Ruft Code der Fehlerzeichenfolge ab.|
|innerError.message|Zeichenfolge|Ruft High-Level-Fehlermeldung ab.|
|innerError.target|Zeichenfolge|Ruft die Ursache des Fehlers ab. Dies wäre z. B. „Dokumente“ oder „Dokument-ID“ im Falle eines ungültigen Dokuments.|

## <a name="examples"></a>Beispiele

### <a name="example-successful-response"></a>Beispiel für erfolgreiche Antwort

Die folgenden Informationen werden bei erfolgreicher Antwort zurückgegeben.

Die Auftrags-ID finden Sie im URL-Wert in der Antwort Header-Operation-Location. Der letzte Parameter der URL ist die Auftrags-ID des Vorgangs (die Zeichenfolge nach "/Operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Beispiel für Fehlerantwort

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie unsere Schnellstartanleitung, um mehr über die Verwendung der Dokumentübersetzung und der Clientbibliothek zu erfahren.

> [!div class="nextstepaction"]
> [Erste Schritte bei der Dokumentübersetzung](../get-started-with-document-translation.md)
