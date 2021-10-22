---
title: Neuerungen in Azure Gesichtserkennung
titleSuffix: Azure Cognitive Services
description: Bleiben Sie über aktuelle Releases und Updates des Azure-Gesichtserkennungsdiensts auf dem laufenden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: contperf-fy21q3, contperf-fy22q1
ms.openlocfilehash: 77e3eb54f344039130f5971f87ccdca1e6461afd
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808327"
---
# <a name="whats-new-in-azure-face-service"></a>Neuerungen in Azure Gesichtserkennung

Der Azure-Dienst „Gesichtserkennung“ wird fortlaufend aktualisiert. In diesem Artikel finden Sie aktuelle Informationen zu Featureverbesserungen, Fixes und Dokumentationsaktualisierungen.

## <a name="july-2021"></a>Juli 2021

### <a name="new-headpose-and-landmarks-improvements-for-detection_03"></a>Neue Verbesserungen für HeadPose und Landmarks in Detection_03

* Das Modell „Detection_03“ wurde aktualisiert, um Gesichtsmerkmale zu unterstützen.
* Das Feature für Merkmale in Detection_03 ist insbesondere bei den Augapfelmerkmalen viel präziser, die für die Blicknachverfolgung entscheidend sind.


## <a name="april-2021"></a>April 2021

### <a name="persondirectory-data-structure"></a>PersonDirectory-Datenstruktur

* Um Gesichtserkennungsvorgänge wie „Identifizieren“ und „Ähnliches suchen“ durchzuführen, müssen Kunden der Gesichtserkennungs-API eine gemischte Liste mit Objekten vom Typ **Person** erstellen. Das neue **PersonDirectory**-Element ist eine Datenstruktur, die eindeutige IDs, optionale Namenszeichenfolgen und optionale Benutzermetadaten-Zeichenfolgen für jede **Person**-Identität enthält, die dem Verzeichnis hinzugefügt wird. Derzeit bietet die Gesichtserkennungs-API die **LargePersonGroup**-Struktur, die über ähnliche Funktionen verfügt, aber auf 1 Mio. Identitäten beschränkt ist. Die **PersonDirectory**-Struktur kann auf bis zu 75 Milo. Identitäten hochskaliert werden. 
* Ein weiterer wichtiger Unterschied zwischen **PersonDirectory** und vorherigen Datenstrukturen besteht darin, dass Sie nach dem Hinzufügen von Gesichtern zu einem **Person**-Objekt keine Train-Aufrufe mehr tätigen müssen – der Aktualisierungsprozess erfolgt automatisch. Weitere Informationen finden Sie unter [Verwenden der PersonDirectory-Struktur](Face-API-How-to-Topics/use-persondirectory.md).


## <a name="february-2021"></a>Februar 2021

### <a name="new-face-api-detection-model"></a>Neues Erkennungsmodell für die Gesichtserkennungs-API
* Das neue Modell „Detection 03“ ist das genaueste derzeit verfügbare Erkennungsmodell. Wenn Sie Neukunde sind, empfehlen wir die Verwendung dieses Modells. Mit dem Modell „detection 03“ werden sowohl der Rückruf als auch die Genauigkeit für kleinere Gesichter in Bildern (64 x 64 Pixel) verbessert. Zu den weiteren Verbesserungen zählen eine Gesamtverringerung von False Positives und eine verbesserte Erkennung bei gedrehter Ausrichtung von Gesichtern. Die Kombination von „Detection 03“ mit dem neuen Modell „Recognition 04“ sorgt zudem für eine höhere Erkennungsgenauigkeit. Ausführlichere Informationen finden Sie unter [Angeben eines Gesichtserkennungsmodells](./face-api-how-to-topics/specify-detection-model.md).
### <a name="new-detectable-face-attributes"></a>Neue erkennbare Gesichtsattribute
* Das Attribut `"noseAndMouthCovered"` ist im aktuellen Modell „Detection 03“ zusammen mit dem zusätzlichen Attribut `faceMask` verfügbar. Mit Letzterem wird ermittelt, ob die Gesichtsmaske wie vorgesehen Nase und Mund bedeckt. Um die aktuelle Funktion zur Maskenerkennung zu verwenden, müssen Benutzer das Erkennungsmodell in der API-Anforderung angeben: Weisen Sie `detection_03` mit dem Parameter _detectionModel_ die Modellversion zu. Ausführlichere Informationen finden Sie unter [Angeben eines Gesichtserkennungsmodells](./face-api-how-to-topics/specify-detection-model.md).
### <a name="new-face-api-recognition-model"></a>Neues Erkennungsmodell für die Gesichtserkennungs-API
* Das neue Modell „Recognition 04“ ist das genaueste derzeit verfügbare Erkennungsmodell. Wenn Sie Neukunde sind, empfehlen wir die Verwendung dieses Modells zur Überprüfung und Identifizierung. Es erhöht die Genauigkeit von „Recognition 03“ und bietet u. a. eine verbesserte Erkennung von registrierten Benutzern, die Mund-Nasen-Bedeckungen (OP-Masken, N95-Masken, Stoffmasken) tragen. Kunden können nun sichere und nahtlose Benutzerumgebungen erstellen, in denen mithilfe des neuesten Modells „Detection 03“ ermittelt wird, ob ein registrierter Benutzer eine Mund-Nasen-Bedeckung trägt, und anschließend mithilfe des ebenfalls neuesten Modells „Recognition 04“ die Identität dieses Benutzers bestimmt wird. Ausführlichere Informationen finden Sie unter [Angeben eines Gesichtserkennungsmodells](./face-api-how-to-topics/specify-recognition-model.md).


## <a name="january-2021"></a>Januar 2021
### <a name="mitigate-latency"></a>Verbessern der Wartezeit
* Das Team für den Dienst „Gesichtserkennung“ hat einen neuen Artikel veröffentlicht, in dem potenzielle Ursachen für die Wartezeit beim Verwenden des Diensts sowie mögliche Strategien zur Risikominderung erläutert werden. Weitere Informationen finden Sie unter [Vorgehensweise: Verringern der Wartezeit bei Verwendung des Diensts „Gesichtserkennung“](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>Dezember 2020
### <a name="customer-configuration-for-face-id-storage"></a>Kundenkonfiguration für den Gesichtserkennungs-ID-Speicher
* Während der Dienst „Gesichtserkennung“ keine Kundenbilder speichert, werden die extrahierten Features für die Gesichtserkennung auf dem Server gespeichert. Die Gesichtserkennungs-ID ist ein Bezeichner des Features für die Gesichtserkennung und wird in [Face – Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face – Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) und [Face – Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) verwendet. Die gespeicherten Features für die Gesichtserkennung laufen ab und werden 24 Stunden nach dem ursprünglichen Erkennungsaufruf gelöscht. Kunden können jetzt festlegen, wie lange diese Gesichtserkennungs-IDs zwischengespeichert werden. Der Höchstwert beträgt immer noch bis zu 24 Stunden, aber es kann nun ein Mindestwert von 60 Sekunden festgelegt werden. Die neuen Zeitbereiche für zwischengespeicherte Gesichtserkennungs-IDs sind alle Werte zwischen 60 Sekunden und 24 Stunden. Weitere Informationen finden Sie in der API-Referenz [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Gesichtserkennung – Detect) (*faceIdTimeToLive*-Parameter).

## <a name="november-2020"></a>November 2020
### <a name="sample-face-enrollment-app"></a>Beispielregistrierungs-App für die Gesichtserkennung
* Das Team hat eine Beispielregistrierungs-App für die Gesichtserkennung veröffentlicht, um bewährte Methoden für das Erreichen einer aussagekräftigen Einwilligung und das Erstellen von hochpräzisen Gesichtserkennungssystemen durch Registrierungen mit hoher Qualität zu aufzuzeigen. Das Open-Source-Beispiel finden Sie im Leitfaden [Erstellen einer Registrierungs-App für Android mit React](build-enrollment-app.md) und auf [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample). Entwickler können es bereitstellen oder anpassen. 

## <a name="august-2020"></a>August 2020
### <a name="customer-managed-encryption-of-data-at-rest"></a>Kundenseitig verwaltete Verschlüsselung ruhender Daten
* Der Dienst „Gesichtserkennung“ verschlüsselt Ihre Daten beim Speichern in der Cloud automatisch. Durch die Verschlüsselung des Diensts „Gesichtserkennung“ werden Ihre Daten ausreichend geschützt, um den Sicherheits- und Complianceanforderungen Ihrer Organisation gerecht zu werden. Standardmäßig verwendet Ihr Abonnement von Microsoft verwaltete Verschlüsselungsschlüssel. Es gibt auch eine neue Option namens „CMK“ (kundenseitig verwaltete Schlüssel) zum Verwalten Ihres Abonnements mit Ihren eigenen Schlüsseln. Weitere Informationen finden Sie unter [Verschlüsselung für ruhende Daten des Gesichtserkennungsdiensts](./encrypt-data-at-rest.md).

## <a name="april-2020"></a>April 2020
### <a name="new-face-api-recognition-model"></a>Neues Erkennungsmodell für die Gesichtserkennungs-API
* Das neue Modell „recognition 03“ ist das genaueste derzeit verfügbare Modell. Wenn Sie Neukunde sind, empfehlen wir die Verwendung dieses Modells. „recognition 03“ bietet eine höhere Genauigkeit sowohl bei Ähnlichkeitsvergleichen als auch bei Vergleichen zur Personenzuordnung. Weitere Informationen finden Sie unter [Angeben eines Gesichtserkennungsmodells](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>Juni 2019

### <a name="new-face-api-detection-model"></a>Neues Erkennungsmodell für die Gesichtserkennungs-API
* Das neue Modell „Detection 02“ bietet verbesserte Genauigkeit bei kleinen, im Profil sichtbaren, verdeckten oder unscharfen Gesichtern. Verwenden Sie es über [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) und [LargePersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), indem Sie den neuen Namen des Gesichtserkennungsmodells `detection_02` im Parameter `detectionModel` angeben. Weitere Informationen finden Sie unter [Angeben eines Erkennungsmodells](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>April 2019

### <a name="improved-attribute-accuracy"></a>Verbesserte Attributgenauigkeit
* Die allgemeine Genauigkeit der Attribute `age` und `headPose` wurde verbessert. Das Attribut `headPose` wird ebenfalls aktualisiert, wobei der Wert `pitch` jetzt aktiviert ist. Verwenden Sie diese Attribute, indem Sie sie im Parameter `returnFaceAttributes` des Parameters [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` angeben. 
### <a name="improved-processing-speeds"></a>Verbesserte Verarbeitungsgeschwindigkeit
* Die Geschwindigkeit wurde für Vorgänge vom Typ [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Face – Erkennen), [FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList – Gesicht hinzufügen), [LargeFaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList – Gesicht hinzufügen), [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person – Gesicht hinzufügen) und [LargePersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person – Gesicht hinzufügen) verbessert.

## <a name="march-2019"></a>März 2019

### <a name="new-face-api-recognition-model"></a>Neues Erkennungsmodell für die Gesichtserkennungs-API
* Das Modell „Recognition 02“ verfügt über eine verbesserte Genauigkeit. Verwenden Sie es über [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) und [LargePersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), indem Sie den neuen Namen des Gesichtserkennungsmodells `recognition_02` im Parameter `recognitionModel` angeben. Weitere Informationen finden Sie unter [Angeben eines Erkennungsmodells](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Januar 2019

### <a name="face-snapshot-feature"></a>Momentaufnahmefeature für die Gesichtserkennung
* Das folgende Feature ermöglicht es dem Dienst, die Datenmigration zwischen Abonnements zu unterstützen: [Momentaufnahme](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Weitere Informationen finden Sie unter [Migrieren Ihrer Gesichtserkennungsdaten in ein anderes Abonnement für die Gesichtserkennung](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Oktober 2018

### <a name="api-messages"></a>API-Meldungen
* Optimierte Beschreibung für `status`, `createdDateTime`, `lastActionDateTime` und `lastSuccessfulTrainingDateTime` in [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5), and [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Mai 2018

### <a name="improved-attribute-accuracy"></a>Verbesserte Attributgenauigkeit
* Die Attribute `gender`, `age`, `glasses`, `facialHair`, `hair` und `makeup` wurden deutlich verbessert. Sie können sie mit dem Parameter `returnFaceAttributes` von [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) verwenden.
### <a name="increased-file-size-limit"></a>Höhere maximale Dateigröße
* Die maximale Dateigröße des Eingabebilds wurde für [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) und [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) von 4 MB auf 6 MB erhöht.

## <a name="march-2018"></a>März 2018

### <a name="new-data-structure"></a>Neue Datenstruktur
* [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) und [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Weitere Informationen finden Sie unter [How to use the large-scale feature (Verwenden des Features für die Verarbeitung in großem Umfang)](Face-API-How-to-Topics/how-to-use-large-scale.md).
* Der [Face – Identify-Parameter ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` wurde von [1, 5] auf [1, 100] erhöht und entspricht standardmäßig 10.

## <a name="may-2017"></a>Mai 2017

### <a name="new-detectable-face-attributes"></a>Neue erkennbare Gesichtsattribute
* Die Attribute `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` und `noise` wurden dem [Face – Detect-Parameter ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` hinzugefügt.
* In PersonGroup und in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) werden 10.000 Personen unterstützt.
* Die Paginierung wird mit den optionalen Parametern `start` und `top` in [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) unterstützt.
* Beim Hinzufügen und Löschen von Gesichtern für verschiedene „FaceLists“ und verschiedene Personen in „PersonGroup“ wird die Parallelität unterstützt.

## <a name="march-2017"></a>März 2017

### <a name="new-detectable-face-attribute"></a>Neues erkennbares Gesichtsattribut
* Das Attribut `emotion` wurde dem [Face – Detect-Parameter ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` hinzugefügt.
### <a name="fixed-issues"></a>Behobene Probleme
* Das Gesicht konnte nicht mit dem von [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Face – Erkennen) als `targetFace` zurückgegebenen Rechteck in [FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList – Gesicht hinzufügen) und [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person – Gesicht hinzufügen) wiedererkannt werden.
* Die erkennbare Gesichtsgröße wurde festgelegt, um sicherzustellen, dass sie immer zwischen 36 × 36 Pixel und 4.096 × 4.096 Pixel liegt.

## <a name="november-2016"></a>November 2016
### <a name="new-subscription-tier"></a>Neuer Abonnementtarif
* Ein Standardabonnement wurde für den Gesichtserkennungsspeicher hinzugefügt, damit zusätzliche Gesichter gespeichert werden können, wenn [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) oder [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) zur Identifikation oder für den Ähnlichkeitsabgleich verwendet wird. Die gespeicherten Bilder werden mit 0,5 US-Dollar pro 1000 Gesichter abgerechnet. Diese Rate wird täglich anteilig berechnet. Abonnements im Free-Tarif sind weiterhin auf eine Gesamtzahl von 1.000 Personen begrenzt.

## <a name="october-2016"></a>Oktober 2016
### <a name="api-messages"></a>API-Meldungen
* Die englische Fehlermeldung „There are more than one face in the image“, die für [FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList – Gesicht hinzufügen) und [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person – Gesicht hinzufügen) angezeigt wird, wenn in `targetFace` mehrere Gesichter vorhanden sind, wurde in „There is more than one face in the image“ geändert.

## <a name="july-2016"></a>Juli 2016
### <a name="new-features"></a>Neue Funktionen
* Die Objektauthentifizierung „Face to Person“ wird in [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) unterstützt.
* Ein optionaler `mode`-Parameter wurde hinzugefügt. Mit diesem Parameter wird die Auswahl von zwei Arbeitsmodi unterstützt: `matchPerson` und `matchFace` in [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), `matchPerson` ist der Standardmodus.
* Ein optionaler `confidenceThreshold`-Parameter wurde hinzugefügt, damit Benutzer den Schwellenwert festlegen können, der besagt, ob ein Gesicht zu einem Personenobjekt in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) gehört.
* Die optionalen Parameter `start` und `top` wurden in [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) hinzugefügt, damit Benutzer den Startpunkt und die Gesamtzahl der aufzuführenden PersonGroups angeben können.

## <a name="v10-changes-from-v0"></a>Änderungen von V0 bis V1.0

* Der Stammdienstendpunkt wurde von ```https://westus.api.cognitive.microsoft.com/face/v0/``` auf ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` aktualisiert. Änderungen wurden an [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) und [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) vorgenommen.
* Die minimal erkennbare Gesichtsgröße wurde auf 36×36 Pixel aktualisiert. Gesichter, die kleiner als 36×36 Pixel sind, werden nicht erkannt.
* PersonGroup- und Personendaten von V0 wurden als veraltet gekennzeichnet. Auf diese Daten kann mit dem Face-Dienst V1.0 nicht zugegriffen werden.
* Der V0-Endpunkt der Face-API ist seit dem 30. Juni 2016 veraltet.