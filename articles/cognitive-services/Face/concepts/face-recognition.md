---
title: Konzepte der Gesichtserkennung
titleSuffix: Azure Cognitive Services
description: Lernen Sie das Konzept der Gesichtserkennung, die zugehörigen Vorgänge und die zugrunde liegenden Datenstrukturen kennen.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 3ae6a67ce0f906a4b498abaec703ddd6e64485bf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426257"
---
# <a name="face-recognition-concepts"></a>Konzepte der Gesichtserkennung

In diesem Artikel werden das Konzept der Gesichtserkennung, die zugehörigen Vorgänge und die zugrunde liegenden Datenstrukturen erläutert. Im Allgemeinen bezieht sich die Gesichtserkennung auf die Methode zum Überprüfen oder Identifizieren einer Person anhand ihres Gesichts. 

Bei der Überprüfung handelt es sich um einen 1:1-Abgleich, der zwei Gesichter erfasst und zurückgibt, ob es sich um das gleiche Gesicht handelt. Die Identifizierung ist ein 1:n-Abgleich, der ein einzelnes Gesicht als Eingabe annimmt und eine Reihe übereinstimmender Kandidaten zurückgibt. Die Gesichtserkennung ist wichtig bei der Implementierung des Identitätsüberprüfungsszenarios, das Unternehmen und Apps verwenden können, um zu überprüfen, ob ein (Remote-)Benutzer die Person ist, die er zu sein angibt.

## <a name="related-data-structures"></a>Verwandte Datenstrukturen

Bei den Erkennungsvorgängen werden hauptsächlich die folgenden Datenstrukturen verwendet. Diese Objekte werden in der Cloud gespeichert und durch ihre ID-Zeichenfolgen referenziert. ID-Zeichenfolgen sind innerhalb eines Abonnements immer eindeutig, aber Namensfelder können doppelt vorkommen werden.

|Name|BESCHREIBUNG|
|:--|:--|
|DetectedFace| Diese Darstellung eines einzelnen Gesichts wird mit dem Vorgang [Gesichtserkennung](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) abgerufen. Die ID läuft 24 Stunden nach der Erstellung ab.|
|PersistedFace| Wenn DetectedFace-Objekte einer Gruppe, z. B. FaceList oder Person, hinzugefügt werden, werden sie zu PersistedFace-Objekten. Sie können jederzeit [abgerufen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) werden und laufen nicht ab.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) oder [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Diese Datenstruktur ist eine gemischte Liste von PersistedFace-Objekten. Eine FaceList verfügt über eine eindeutige ID, eine Namenszeichenfolge und optional eine Benutzerdatenzeichenfolge.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Diese Datenstruktur ist eine Liste von PersistedFace-Objekten, die zu derselben Person gehören. Sie verfügt über eine eindeutige ID, eine Namenszeichenfolge und optional eine Benutzerdatenzeichenfolge.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) oder [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Diese Datenstruktur ist eine gemischte Liste von Person-Objekten. Sie verfügt über eine eindeutige ID, eine Namenszeichenfolge und optional eine Benutzerdatenzeichenfolge. Eine PersonGroup muss [trainiert](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) werden, bevor sie in Erkennungsvorgängen verwendet werden kann.|
|PersonDirectory | Diese Datenstruktur ähnelt **LargePersonGroup**, bietet aber mehr Speicherkapazität und weitere zusätzliche Features. Weitere Informationen finden Sie unter [Verwenden der PersonDirectory-Struktur](../Face-API-How-to-Topics/use-persondirectory.md).

## <a name="recognition-operations"></a>Erkennungsvorgänge

In diesem Abschnitt wird erläutert, wie die oben beschriebenen Datenstrukturen von den zugrunde liegenden Vorgängen verwendet werden, um ein Gesicht zu identifizieren und zu überprüfen.

### <a name="persongroup-creation-and-training"></a>Erstellen und Trainieren von PersonGroups

Sie müssen eine [PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) oder [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) erstellen, um die Personen zu speichern, mit denen ein Abgleich durchgeführt werden soll. PersonGroups enthalten [Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)-Objekte, die jeweils eine einzelne Person darstellen und Gesichtsdaten enthalten, die zu dieser Person gehören.

Der [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)-Vorgang bereitet das DataSet für die Verwendung in Gesichtsdatenvergleichen vor.

### <a name="identification"></a>Identifikation

Der [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)-Vorgang nimmt eine oder mehrere Quellgesichts-IDs (aus einem DetectedFace- oder PersistedFace-Objekt) und eine PersonGroup oder LargePersonGroup an. Es wird eine Liste der Person-Objekte zurückgegeben, zu denen jedes Quellgesichtsobjekt gehören kann. Die zurückgegebenen Person-Objekte werden als Candidate-Objekte umschlossen und erhalten einen Zuverlässigkeitswert für die Vorhersage.

### <a name="verification"></a>Überprüfung

Der [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)-Vorgang nimmt eine Quellgesichts-ID (aus einem DetectedFace- oder PersistedFace-Objekt) und ein Person-Objekt an. Er bestimmt, ob das Gesicht zu derselben Person gehört. Die Verifizierung ist ein 1:1-Abgleich und kann als abschließende Überprüfung der Ergebnisse des Identify-API-Aufrufs verwendet werden. Sie können jedoch optional die PersonGroup übergeben, zu der die Person gehört, um die API-Leistung zu verbessern.

## <a name="input-data"></a>Eingabedaten

Anhand der folgenden Tipps können Sie sicherstellen, dass Ihre Eingabebilder möglichst genaue Erkennungsergebnisse liefern:

* Als Eingabebildformate werden JPEG, PNG, GIF (der erste Frame), BMP unterstützt.
* Bilddateien dürfen maximal 6 MB groß sein.
* Wenn Sie die Person-Objekte erstellen, verwenden Sie Fotos mit verschiedenen Winkeln und Belichtungen.
* Einige Gesichter können möglicherweise aufgrund technischer Probleme wie der folgenden nicht erkannt werden:
  * Bilder mit extremer Beleuchtung (z. B. starkes Gegenlicht)
  * Hindernisse, die ein oder beide Augen verdecken
  * Unterschiede beim Haartyp oder der Gesichtsbehaarung
  * Veränderungen am Gesicht (z. B. durch Alterung)
  * Extreme Gesichtsausdrücke
* Sie können das QualityForRecognition-Attribut im [Gesichtserkennungsvorgang](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) verwenden, wenn Sie geeignete Erkennungsmodelle als allgemeine Richtlinie verwenden, um zu prüfen, ob das Bild wahrscheinlich eine ausreichende Qualität aufweist, um die Gesichtserkennung zu versuchen. Für die Registrierung von Personen werden nur Bilder der Qualitätsstufe „hoch“ empfohlen, und die Qualität „mittel“ oder höher wird bei Identifikationen empfohlen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den Konzepten der Gesichtserkennung vertraut sind, schreiben Sie ein Skript, das Gesichter in einer trainierten Personengruppe (PersonGroup) erkennt.

* [Schnellstart: Clientbibliothek zur Gesichtserkennung](../Quickstarts/client-libraries.md)