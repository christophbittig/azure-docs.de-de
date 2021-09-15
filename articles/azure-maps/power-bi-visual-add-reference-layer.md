---
title: Hinzufügen einer Referenzebene zum Azure Maps-Visual in Power BI | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie die Referenzebene im Microsoft Azure Maps-Visual für Power BI verwenden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: f1488584f025edc9e8b3e098b283efbb56b449d4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430430"
---
# <a name="add-a-reference-layer"></a>Hinzufügen einer Referenzebene

Mit dem Feature „Referenzebene“ kann ein sekundäres räumliches Dataset in das Visual hochgeladen und auf der Karte platziert werden, um zusätzlichen Kontext bereitzustellen. Dieses Dataset wird von Power BI gehostet. Hierbei muss es sich um eine [GeoJSON-Datei](https://wikipedia.org/wiki/GeoJSON) mit der Endung `.json` oder `.geojson` handeln.

Wenn Sie eine **GeoJSON**-Datei als Referenzebene hinzufügen möchten, navigieren Sie zum Bereich **Format**, erweitern Sie den Bereich **Referenzebene**, und klicken Sie auf die Schaltfläche **+ Lokale Datei hinzufügen**.

Nachdem der Referenzebene eine GeoJSON-Datei hinzugefügt wurde, wird anstelle der Schaltfläche **+ Lokale Datei hinzufügen** der Name der Datei mit einem **X** daneben angezeigt. Klicken Sie auf die Schaltfläche **X**, um die Daten aus dem Visual zu entfernen und die GeoJSON-Datei aus Power BI zu löschen.

Die folgende Karte zeigt die [Volkszählungsdaten von 2016 für Colorado](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson) an, farblich gekennzeichnet nach Einwohnerzahl.

> [!div class="mx-imgBorder"]
> ![Karte mit Volkszählungsdaten von 2016 für Colorado, farblich gekennzeichnet nach Einwohnerzahl, als Referenzebene](media/power-bi-visual/reference-layer-CO-census-tract.png)

Im Folgenden finden Sie alle Einstellungen im Bereich **Format**, die im Abschnitt **Referenzebene** verfügbar sind.

| Einstellung              | BESCHREIBUNG   |
|----------------------|---------------|
| Daten der Referenzebene | Die GeoJSON-Datendatei, die als zusätzliche Ebene innerhalb der Karte in das Visual hochgeladen werden soll. Mit der Schaltfläche **+ Lokale Datei hinzufügen** wird ein Dateidialogfeld geöffnet, in dem der Benutzer einer GeoJSON-Datei mit der Endung `.json` oder `.geojson` auswählen kann. |

> [!NOTE]
> In dieser Vorschauversion des Azure Maps-Visuals werden durch die Referenzebene nur die ersten 5.000 Formfeatures in die Karte geladen. Dieses Limit wird in einem zukünftigen Update erhöht.

## <a name="styling-data-in-a-reference-layer"></a>Formatieren von Daten in einer Referenzebene

Den einzelnen Features in der GeoJSON-Datei können Eigenschaften hinzugefügt werden, um deren Formatierung auf der Karte anzupassen. Dieses Feature verwendet das Feature der einfachen Datenebene im Azure Maps-Web-SDK. Weitere Informationen finden Sie in diesem Dokument über [unterstützte Stileigenschaften](spatial-io-add-simple-data-layer.md#default-supported-style-properties). Als Sicherheitsmaßnahme werden benutzerdefinierte Symbolbilder im Azure Maps-Visual nicht unterstützt.

Im Folgenden sehen Sie ein Beispiel für ein GeoJSON-Punktfeature, mit dem die angezeigte Farbe auf Rot festgelegt wird.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Nächste Schritte

Fügen Sie der Karte mehr Kontext hinzu:

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Anzeigen von Verkehrsinfo in Echtzeit](power-bi-visual-show-real-time-traffic.md)
