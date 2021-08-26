---
title: Fehlercodes bei der Modellkonvertierung
description: Fehlercodes für die Modellkonvertierung für Azure Object Anchors-Dienst.
author: jastenze
manager: vriveras
ms.author: jastenze
ms.date: 04/20/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: e47e4e9f784e51c646da22063ee954d9b60a4e11
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202669"
---
# <a name="model-conversion-error-codes"></a>Fehlercodes bei der Modellkonvertierung

Bei gängigen Modi des Modellkonvertierungsfehlers enthält das aus dem `Value`-Feld im `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionOperation`erhaltene `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionProperties`-Objekt ein ErrorCode-Feld des Typs `ConversionErrorCode`. Dieser Typ enumeriert diese gängigen Fehlermodi für die Lokalisierung von Fehlermeldungen, die Wiederherstellung nach Fehlern und Tipps für den Benutzer, wie der Fehler behoben werden kann.

| Fehlercode                    | BESCHREIBUNG                       |  Minderung                       |
| ---                      | ---                               | ---                               |
| INVALID_ASSET_URI | Das Objekt am URI, das beim Starten des Konvertierungsauftrags angegeben wurde, konnte nicht gefunden werden. | Geben Sie beim Auslösen eines Auftrags zur Objektkonvertierung einen Upload-URI an, der von dem Dienst erhalten wurde, in den das zu konvertierende Objekt hochgeladen wurde. |
| INVALID_JOB_ID | Die bereitgestellte ID für den zu erstellenden Objektkonvertierungsauftrag wurde auf die standardmäßige GUID mit 0 (null) festgelegt. | Wenn beim Erstellen eines Objektkonvertierungsauftrags eine GUID angegeben wird, stellen Sie sicher, dass es sich nicht um die standardmäßige GUID mit 0 (null) handelt. |
| INVALID_GRAVITY | Der Schwerkraftvektor, der beim Erstellen des Objektkonvertierungsauftrags bereitgestellt wurde, war ein Vektor mit vollständig nulliertem Wert. | Geben Sie beim Starten einer Objektkonvertierung den Schwerkraftvektor an, der dem hochgeladenen Objekt entspricht. |
| INVALID_SCALE | Der bereitgestellte Skalierungsfaktor war kein positiver Wert, der nicht 0 (null) ist. | Geben Sie beim Starten einer Objektkonvertierung den Skalarwert an, der der Maßeinheitsskala (in Bezug auf Meter) des hochgeladenen Objekts entspricht. |
| ASSET_SIZE_TOO_LARGE | Die zwischengeschaltete .PLY-Datei, die aus dem Objekt oder der serialisierten Entsprechung generiert wurde, war zu groß. | Lesen Sie die Richtlinien zur Ressourcengröße, bevor Sie eine Ressource zur Konvertierung übermitteln, um die Konformität sicherzustellen: aka.ms/aoa/faq |
| ASSET_DIMENSIONS_OUT_OF_BOUNDS | Die Dimensionen des Objekts haben das physische Dimensionslimit überschritten. Dies kann ein Zeichen für eine nicht ordnungsgemäß festgelegte Skalierung für das Objekt sein, wenn ein Auftrag erstellt wird. | Lesen Sie die Richtlinien für die Objektgröße, bevor Sie eine Ressource zur Konvertierung übermitteln, um die Konformität sicherzustellen, und stellen Sie sicher, dass die bereitgestellte Skalierung dem hochgeladenen Objekt entspricht: aka.ms/aoa/faq |
| ZERO_FACES | Die zwischengeschaltete und aus dem Medienobjekt generierte .PLY-Datei hat keine Gesichter, sodass sie für die Konvertierung ungültig wird. | Stellen Sie sicher, dass das Objekt ein gültiges Gitternetz ist. |
| INVALID_FACE_VERTICES | Der zwischengeschaltete und aus dem Medienobjekt generierte .PLY-Datei enthielt Gesichter, die auf nicht vorhandene Scheitelpunkte verwiesen haben. | Stellen Sie sicher, dass die Objektdatei gültig erstellt wurde. |
| ZERO_TRAJECTORIES_GENERATED | Die aus dem hochgeladenen Objekt generierten Kameratrajektorien waren leer. | Lesen Sie die Objekt-Richtlinien, bevor Sie eine Ressource zur Konvertierung übermitteln, um die Konformität sicherzustellen: aka.ms/aoa/faq |
| TOO_MANY_RIG_POSES | Die Anzahl von „rig“-Posen in der Zwischenversion der .PLY-Datei hat die Dienstgrenzwerte überschritten. | Lesen Sie die Richtlinien zur Ressourcengröße, bevor Sie eine Ressource zur Konvertierung übermitteln, um die Konformität sicherzustellen: aka.ms/aoa/faq |
| SERVICE_ERROR | Ein unbekannter Fehler ist aufgetreten. | Wenden Sie sich an ein Mitglied des Object Anchors-Serviceteams, wenn das Problem weiterhin besteht: https://github.com/Azure/azure-object-anchors/issues |
| ASSET_CANNOT_BE_CONVERTED | Das bereitgestellte Objekt wurde beschädigt, falsch formatiert oder kann auf andere Weise nicht im angegebenen Format konvertiert werden. | Stellen Sie sicher, dass es sich bei dem Objekt um eine gültig konstruierte Datei des angegebenen Typs handelt, und lesen Sie die Richtlinien für die Objektgröße, bevor Sie eine Ressource zur Konvertierung übermitteln, um die Konformität sicherzustellen: aka.ms/aoa/faq |

Alle Fehler, die außerhalb der tatsächlichen Objektkonvertierungsaufträge auftreten, werden als Ausnahmen ausgelöst. Insbesondere kann `Azure.RequestFailedException` für Dienstaufrufe ausgelöst werden, die einen nicht erfolgreichen (4xx oder 5xx) oder unerwarteten HTTP-Antwortcode empfangen. Weitere Informationen zu diesen Ausnahmen finden Sie in den `Status`, `ErrorCode` oder `Message`-Feldern für die Ausnahme.

| Ausnahme                  | Ursache                       |
| ---                      | ---                               |
| ArgumentException |  <ul><li>Diese Ausnahme tritt auf, wenn eine ungültig konstruierte oder mit Null bezifferte Konto-IDs verwendet werden, um eine Anforderung mit dem ObjectAnchorsConversionClient zu erstellen.</li><li>Diese Ausnahme tritt auf, wenn versucht wird, den ObjectAnchorsConversionClient mithilfe einer ungültigen Leerraumkontodomäne zu initialisieren.</li><li>Diese Ausnahme tritt auf, wenn dem ObjectAnchorsConversionClient über die ObjectAnchorsConversionClientOptions eine nicht unterstützte Dienstversion bereitgestellt wird.</li></ul> |
| ArgumentNullException | <ul><li>Diese Ausnahme tritt auf, wenn versucht wird, den ObjectAnchorsConversionClient mithilfe einer ungültigen Leerraumkontodomäne zu initialisieren.</li><li>Diese Ausnahme tritt auf, wenn versucht wird, den ObjectAnchorsConversionClient mithilfe einer ungültigen mit Null bezifferten Anmeldeinformation zu benutzen.</li></ul> |
| RequestFailedException | <ul><li>Die Ausnahme tritt für alle anderen Probleme auf, die auf einen fehlerhaften HTTP-Statuscode zurückzuführen sind (unabhängig vom Status eines Auftrags, der ausgeführt wird oder wurde), z. B. wenn ein Konto nicht gefunden wurde, eine ungültige Upload-URI vom Frontend erkannt wurde, ein Fehler im Frontend-Dienst vorliegt usw.</li></ul> |
| UnsupportedAssetFileTypeException | <ul><li>Dies Ausnahme tritt auf, wenn versucht wird, einen Auftrag mit einem Objekt mit einer Erweiterung oder einem angegebenen Dateityp zu übermitteln, der nicht vom Konvertierungsdienst Azure Object Anchors unterstützt wird.</li></ul> |