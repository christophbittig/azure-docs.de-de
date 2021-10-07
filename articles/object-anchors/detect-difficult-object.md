---
title: Erkennen eines schwierigen Objekts
description: Beschreiben Sie Mechanismen, die konfiguriert werden können, um schwierige Objekte zu erkennen.
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 09/08/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: cbdf29aa7bb9c342e08b64af0745c689910b26bd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791971"
---
# <a name="how-to-detect-a-difficult-object"></a>Erkennen eines schwierigen Objekts

Manchmal kann es vorkommen, dass ein Objekt schwieriger zu erkennen ist. Beispiel:

- Wenn auf eine große Oberfläche nicht zugegriffen werden kann, da sich das Objekt vor einer Wand befindet.
- Wenn ein Objekt zu groß ist und es zu lange dauert, um es zu umgehen.
- Wenn die Oberfläche des Objekts von den Gerätesensoren nicht erkannt wird.

## <a name="adjusting-object-query-values"></a>Anpassen von Objektabfragewerten

Einige Mechanismen, die vom Azure Object Anchors SDK bereitgestellt werden und in diesen Situationen hilfreich sein können, sind die folgenden:

- Die `ObjectQuery.MinSurfaceCoverage`-Eigenschaft Sie stellt das minimal erforderliche Oberflächenabdeckungsverhältnis dar, um eine Objektinstanz als True Positive zu betrachten. Sie ermöglicht einen Bereich zwischen 0 und 1,0 (entspricht 0 % bis 100 %). Die Standardeinstellung variiert je nach Objekt (je größer die Oberfläche, desto kleiner die erforderliche Mindestabdeckung). Das funktioniert in den meisten Situationen ohne Änderung. Bei schwierigen Objekten empfiehlt es sich jedoch, den Wert für diese Eigenschaft zu verringern, damit weniger Oberflächenabdeckung erforderlich ist, um das Objekt zu erkennen.

- Die `ObjectQuery.MaxScaleChange`-Eigenschaft Wenn das ursprüngliche Modell nicht über eine `1:1`-Skalierung für das erkannte Objekt verfügt, kann diese Einstellung angepasst werden. Sie ermöglicht einen Bereich zwischen 0 und 1,0 (entspricht 0 % bis 100 %). Die Standardeinstellung deaktiviert bei 0 die Skalierungsschätzung, die eine `1:1`-Skalierungszuordnung erfordert. Wenn diese Eigenschaft z. B. auf 10 % festgelegt ist, wird die Skalierungsschätzung aktiviert und eine gewisse Flexibilität in Fällen ermöglicht, in denen die Modellskalierung keine `1:1`-Übereinstimmung mit dem Objekt aufwies.

- Die `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees`-Eigenschaft Sie stellt den maximalen Winkel in Grad zwischen der Aufwärtsrichtung des Objekts und der Schwerkraft dar. Sie reicht von 0 bis 180. Anders ausgedrückt: Sie stellt die Neigung des Objekts relativ zum ursprünglichen Modell dar. Die Standardeinstellung von 3 Grad kann erhöht werden, um mehr Flexibilität in Fällen zu ermöglichen, in denen die Objektneigung nicht mit dem ursprünglichen Modell übereinstimmt.

- Die `ObjectQuery.IsExpectedToBeStandingOnGroundPlane`-Eigenschaft Es handelt sich um einen booleschen Wert, der angibt, ob sich das Objekt voraussichtlich auf der Bodenebene befindet. Der Standardwert ist FALSE. Er kann auf TRUE umgestellt werden, um die Erkennung für Fälle zu beschleunigen, in denen sich das Objekt auf der Bodenebene befindet.

- Die `ObjectQuery.SearchAreas`-Eigenschaft Sie stellt eine Sammlung von Bereichen dar, in denen nach Objekten gesucht wird. Die Bereitstellung eingeschränkter Suchbereiche bei gleichzeitiger Abdeckung des gesamten oder eines Großteils des Objekts verbessert die Erkennungsgeschwindigkeit und -genauigkeit. Sie können Folgendes auswählen:

  - Einen ausgerichteten Begrenzungsrahmen, in dem Sie `ObjectSearchArea.FromOriented` verwenden.
  - Ein Sichtfeld, indem Sie `ObjectSearchArea.FromFieldOfView` verwenden.
  - Einen Standort, indem Sie `ObjectSearchArea.FromLocation` verwenden.
  - Eine Kugel, indem Sie `ObjectSearchArea.FromSphere` verwenden.

Weitere Informationen finden Sie in der `ObjectQuery`-Klasse für [Unity](/dotnet/api/microsoft.azure.objectanchors.objectquery) oder [HoloLens C++/WinRT](/cpp/api/object-anchors/winrt/objectquery).

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden zur Problembehandlung haben Sie erfahren, wie Sie Probleme bei der Erkennung von schwer zu erkennenden Objekten behandeln.
Hier sind einige verwandte Artikel:

> [!div class="nextstepaction"]
> [Problembehandlung bei der Objekterkennung](./troubleshoot/object-detection.md)
