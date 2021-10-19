---
title: Übersicht über den DICOM-Änderungsfeed – Azure Healthcare-APIs
description: In diesem Artikel lernen Sie die Konzepte des DICOM-Änderungsfeeds kennen.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fc23c4cedbb6ee94c4f253cf53e0cdde27531fb4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355522"
---
# <a name="change-feed-overview"></a>Übersicht über Änderungsfeeds

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der Änderungsfeed stellt Protokolle aller Änderungen bereit, die im DICOM-Dienst auftreten. Der Änderungsfeed stellt geordnete, garantierte, unveränderliche und schreibgeschützte Protokolle dieser Änderungen bereit. Der Änderungsfeed bietet die Möglichkeit, den Verlauf des DICOM-Diensts zu durchlaufen und wirkt auf die Erstellungen und Löschungen im Dienst.

Clientanwendungen können diese Protokolle jederzeit lesen, entweder im Streamingmodus oder im Batchmodus. Mit dem Änderungsfeed können Sie effiziente und skalierbare Lösungen erstellen, die Änderungsereignisse verarbeiten, die in Ihrem DICOM-Dienst auftreten.

Sie können diese Änderungsereignisse asynchron, inkrementell oder vollständig verarbeiten. Eine beliebige Anzahl von Clientanwendungen kann den Änderungsfeed unabhängig voneinander parallel und in ihrem eigenen Tempo lesen.

Stellen Sie sicher, dass Sie die Version als Teil der URL angeben, wenn Sie Anforderungen stellen. Weitere Informationen finden Sie in der Dokumentation zur [API-Versionierung für den DICOM-Dienst.](api-versioning-dicom-service.md)

## <a name="api-design"></a>API-Entwurf

Die API macht zwei `GET` Endpunkte für die Interaktion mit dem Änderungsfeed verfügbar. Ein typischer Ablauf zum Nutzen des Änderungsfeeds finden [Sie unter](#example-usage-flow).

Verb | Route              | Gibt zurück     | BESCHREIBUNG
:--- | :----------------- | :---------- | :---
GET  | /changefeed        | JSON-Array  | [Lesen des Änderungsfeeds](#read-change-feed)
GET  | /changefeed/latest | JSON-Objekt | [Lesen des aktuellen Eintrags im Änderungsfeed](#get-latest-change-feed-item)

### <a name="object-model"></a>Objektmodell

Feld               | type      | BESCHREIBUNG
:------------------ | :-------- | :---
Sequenz            | INT       | Die Sequenz-ID, die zum Paging (per Offset) oder zur Ankerung verwendet werden kann
StudyInstanceUid    | Zeichenfolge    | Die UiD der Studieinstanz
SeriesInstanceUid   | Zeichenfolge    | Die UiD der Reiheninstanz
SopInstanceUid      | Zeichenfolge    | Die SOP-Instanz-UID
Aktion              | Zeichenfolge    | Die aktion, die ausgeführt wurde – entweder `create` oder `delete`
Timestamp           | datetime  | Datum und Uhrzeit der Aktion in UTC
State               | Zeichenfolge    | [Der aktuelle Status der Metadaten](#states)
Metadaten            | Objekt    | Optional die aktuellen DICOM-Metadaten, wenn die Instanz vorhanden ist

#### <a name="states"></a>Zustände

State    | BESCHREIBUNG
:------- | :---
Aktuell  | Diese Instanz ist die aktuelle Version.
replaced | Diese Instanz wurde durch eine neue Version ersetzt.
deleted  | Diese Instanz wurde gelöscht und ist im Dienst nicht mehr verfügbar.

### <a name="read-change-feed"></a>Lesen des Änderungsfeeds

**Route**: /changefeed?offset={int}&limit={int}&includemetadata={**true**|false}
```
[
    {
        "Sequence": 1,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-04T01:03:08.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    },
    {
        "Sequence": 2,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-05T07:13:16.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    }
    ...
]
```
#### <a name="parameters"></a>Parameter

Name            | type | BESCHREIBUNG
:-------------- | :--- | :---
offset          | INT  | Die Anzahl von Datensätzen, die vor den zurückzugebende Werten übersprungen werden sollen
limit           | INT  | Die Anzahl der zurückzugebende Datensätze (Standard: 10, min: 1, max: 100)
includemetadata | bool | Angabe, ob die Metadaten eingeschlossen werden sollen (Standardeinstellung: TRUE)

### <a name="get-latest-change-feed-item"></a>Abrufen des aktuellen Änderungsfeedelements

**Route:**/changefeed/latest?includemetadata={**true**|false}

```
{
    "Sequence": 2,
    "StudyInstanceUid": "{uid}",
    "SeriesInstanceUid": "{uid}",
    "SopInstanceUid": "{uid}",
    "Action": "create|delete",
    "Timestamp": "2020-03-05T07:13:16.4834Z",
    "State": "current|replaced|deleted",
    "Metadata": {
        "actual": "metadata"
    }
}
```

#### <a name="parameters"></a>Parameter

Name            | type | BESCHREIBUNG
:-------------- | :--- | :---
includemetadata | bool | Angabe, ob die Metadaten eingeschlossen werden sollen (Standardeinstellung: TRUE)

## <a name="usage"></a>Verwendung

### <a name="example-usage-flow"></a>Beispiel für nutzungsfluss

Im Folgenden finden Sie den Nutzungsablauf für eine Beispielanwendung, die andere Verarbeitungen für die Instanzen innerhalb des DICOM-Diensts übernimmt.

1. Anwendung, die den Start des Änderungsfeeds überwachen möchte.
2. Er bestimmt, ob es einen aktuellen Zustand gibt, mit dem er beginnen soll:
   * Wenn der Zustand vorliegt, wird der gespeicherte Offset (Sequenz) verwendet.
   * Wenn es noch nie gestartet wurde und von Anfang an beginnen möchte, wird `offset=0` verwendet.  
   * Wenn sie nur von jetzt an verarbeiten möchte, fragt sie `/changefeed/latest` ab, um die letzte Sequenz zu erhalten.
3. Der Änderungsfeed wird mit dem angegebenen Offset abgefragt. `/changefeed?offset={offset}`
4. Wenn Einträge vorhanden sind:
   * Er führt eine zusätzliche Verarbeitung durch.  
   * Der aktuelle Zustand wird aktualisiert.  
   * Sie wird weiter oben in Schritt 2 erneut gestartet.
5. Wenn keine Einträge enthalten sind, wird er für einen konfigurierten Zeitraum in den Ruhezustand übergeht und mit Schritt 2 zurück gestartet.

### <a name="other-potential-usage-patterns"></a>Andere potenzielle Verwendungsmuster

Die Unterstützung des Änderungsfeeds eignet sich gut für Szenarien, in denen Daten basierend auf geänderten Objekten verarbeiten. Sie kann z. B. für Folgendes verwendet werden:

* Erstellen Sie verbundene Anwendungspipelines wie ML, die auf Änderungsereignisse reagieren oder Ausführungen basierend auf einer erstellten oder gelöschten Instanz planen.
* Extrahieren Sie Erkenntnisse und Metriken für Geschäftsanalysen basierend auf Änderungen, die an Ihren Objekten auftreten.
* Stellen Sie den Änderungsfeed ab, um eine Ereignisquelle für Pushbenachrichtigungen zu erstellen.

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben wir den REST-API-Entwurf des Änderungsfeeds und mögliche Verwendungsszenarien überprüft. Informationen zum Änderungsfeed finden Sie unter [Pullen von Änderungen aus dem Änderungsfeed.](pull-dicom-changes-from-change-feed.md)

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)

