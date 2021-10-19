---
title: 'Verwenden von DICOMweb : Standard-APIs mit Azure Healthcare-APIs DICOM-Dienst'
description: In diesem Tutorial wird beschrieben, wie Sie DICOMweb Standard-APIs mit dem DICOM-Dienst verwenden.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: aersoy
ms.openlocfilehash: b219c4d624c5823804371bba156911aaa94eeccb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631985"
---
# <a name="using-dicomwebtradestandard-apis-with-dicom-services"></a>Verwenden von &trade; DICOMweb-Standard-APIs mit DICOM-Diensten

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Dieses Tutorial bietet eine Übersicht über die Verwendung der DICOMweb &trade; Standard-APIs mit dem DICOM-Dienst.

Der DICOM-Dienst unterstützt eine Teilmenge des &trade; DICOMweb-Standards, die Folgendes enthält:

* Store (STOW-RS)
* Abrufen (WADO-RS)
* Search (QIDO-RS)

Darüber hinaus werden die folgenden nicht standardmäßigen APIs unterstützt:

* Löschen
* Änderungsfeed

Weitere Informationen zur Unterstützung der DICOM-Webstandard-APIs finden Sie im Referenzdokument [zur DICOM-Konformitätserklärung.](dicom-services-conformance-statement.md)

## <a name="prerequisites"></a>Voraussetzungen

Um die DICOMweb Standard-APIs verwenden zu &trade; können, muss eine Instanz der DICOM-Dienste bereitgestellt sein. Wenn Sie noch keine Instanz des DICOM-Diensts bereitgestellt haben, finden Sie weitere Informationen unter Bereitstellen des [DICOM-Diensts mithilfe der Azure-Portal](deploy-dicom-services-in-azure.md).

Nach Abschluss der Bereitstellung können Sie mithilfe des Azure-Portal zum neu erstellten DICOM-Dienst navigieren, um die Details einschließlich Ihrer Dienst-URL anzuzeigen. Die Dienst-URL für den Zugriff auf Ihren DICOM-Dienst ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` ist: . Stellen Sie sicher, dass Sie die Version als Teil der URL angeben, wenn Sie Anforderungen stellen. Weitere Informationen finden Sie in der Dokumentation zur [API-Versionierung für den DICOM-Dienst.](api-versioning-dicom-service.md)

## <a name="overview-of-various-methods-to-use-with-dicom-service"></a>Übersicht über verschiedene Methoden zur Verwendung mit dem DICOM-Dienst

Da der DICOM-Dienst als REST-API verfügbar gemacht wird, können Sie mit jeder modernen Entwicklungssprache darauf zugreifen. Sprachunabhängige Informationen zum Arbeiten mit dem Dienst finden Sie unter [DICOM Conformance Statement (DICOM-Konformitätsbestimmungen).](dicom-services-conformance-statement.md)

Sprachspezifische Beispiele finden Sie in den folgenden Beispielen. Sie können Beispiele für Postman-Sammlungen in mehreren Sprachen anzeigen, z. B.:

* Go 
* Java 
* JavaScript 
* C# 
* PHP 
* C 
* NodeJS
* Objective-C
* OCaml
* PowerShell
* Python
* Ruby 
* Schnell.

### <a name="c"></a>C#

Informationen zur Verwendung von C# mit dem DICOM-Dienst finden Sie im Tutorial [Verwenden von DICOMweb™ Standard-APIs mit C#.](dicomweb-standard-apis-c-sharp.md)

### <a name="curl"></a>cURL

cURL ist ein gängiges Befehlszeilentool zum Aufrufen von Webendpunkten, das für nahezu jedes Betriebssystem verfügbar ist. [Laden Sie cURL](https://curl.haxx.se/download.html) herunter, um zu beginnen.

Informationen zur Verwendung von cURL mit dem DICOM-Dienst finden Sie im Tutorial [Verwenden von DICOMWeb™ Standard-APIs mit cURL.](dicomweb-standard-apis-curl.md)

### <a name="python"></a>Python

Informationen zur Verwendung von Python mit dem DICOM-Dienst finden Sie im Tutorial [Verwenden von DICOMWeb™ Standard-APIs mit Python.](dicomweb-standard-apis-python.md)

### <a name="postman"></a>postman

Postman ist ein hervorragendes Tool zum Entwerfen, Erstellen und Testen von REST-APIs. [Laden Sie Postman](https://www.postman.com/downloads/) herunter, um zu beginnen. Informationen zur effektiven Verwendung von Postman finden Sie auf der [Postman-Lernwebsite.](https://learning.postman.com/)

Ein wichtiger Nachteil bei Postman und dicomweb &trade; Standard ist, dass Postman nur das Hochladen von DICOM-Dateien mithilfe der im DICOM-Standard definierten Nutzlast mit einem Teil unterstützen kann. Dies liegt daran, dass Postman benutzerdefinierte Trennzeichen in einer mehrteiligen/verknüpften POST-Anforderung nicht unterstützen kann. Weitere Informationen finden Sie unter [Multipart POST not working for me # 576](https://github.com/postmanlabs/postman-app-support/issues/576). Daher wird allen Beispielen in der Postman-Sammlung zum Hochladen von DICOM-Dokumenten mit einer mehrteiligen Anforderung das Präfix [funktioniert nicht – siehe Beschreibung] vorangestellt. Die Beispiele für das Hochladen mit einer einteiligen Anforderung sind in der Auflistung enthalten und haben das Präfix "Store-Single-Instance".

Um die Postman-Sammlung zu verwenden, müssen Sie die Sammlung lokal herunterladen und über Postman importieren. Informationen zum Zugriff auf diese Sammlung finden Sie unter [Beispiele für Postman-Sammlungen.](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)

## <a name="summary"></a>Zusammenfassung

Dieses Tutorial bot eine Übersicht über die apis, die vom DICOM-Dienst unterstützt werden. Erste Schritte mit diesen APIs mit den folgenden Tools:

- [Verwenden von DICOMweb™ Standard-APIs mit C #](dicomweb-standard-apis-c-sharp.md)
- [Verwenden von DICOMWeb™ Standard-APIs mit cURL](dicomweb-standard-apis-curl.md)
- [Verwenden von DICOMWeb™ Standard-APIs mit Python](dicomweb-standard-apis-python.md)
- [Verwenden von DICOM-Webstandard-APIs mit Postman-Beispielsammlung](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)

### <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
