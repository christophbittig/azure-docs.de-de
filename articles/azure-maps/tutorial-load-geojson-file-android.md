---
title: 'Tutorial: Laden von GeoJSON-Daten in das Android SDK für Azure Maps | Microsoft Azure Maps'
description: Tutorial zum Laden einer GeoJSON-Datendatei in das Android-Karten-SDK für Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: b36c82a12b984aa956cc7d9d41cbc13ffb3d083f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425210"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Tutorial: Laden von GeoJSON-Daten in das Android SDK für Azure Maps

In diesem Tutorial wird Schritt für Schritt beschrieben, wie Sie eine GeoJSON-Datei mit Standortdaten in das Android SDK für Azure Maps importieren. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen von Azure Maps zu einer Android-Anwendung
> * Erstellen einer Datenquelle und Laden in eine GeoJSON-Datei aus einer lokalen Datei oder dem Web
> * Anzeigen der Daten in der Karte
> * Interagieren mit den Daten in den Karten, um sich die dazugehörigen Details anzeigen zu lassen

## <a name="prerequisites"></a>Voraussetzungen

1. Schließen Sie den [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ab. In diesem Tutorial wird der Code erweitert, der in dieser Schnellstartanleitung verwendet wurde.
2. Laden Sie die GeoJSON-Datei mit den [Point of Interest-Beispielen](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) herunter.

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importieren von GeoJSON-Daten aus dem Web oder einem Ressourcenordner

Bei den meisten GeoJSON-Dateien sind alle Daten mit einem `FeatureCollection`-Element umschlossen. Dieses Szenario bedeutet Folgendes: Wenn die GeoJSON-Dateien als Zeichenfolge in die Anwendung geladen werden, können sie an die statische `fromJson`-Methode der Funktionssammlung übergeben werden. Mit dieser Methode wird die Zeichenfolge in ein `FeatureCollection`-GeoJSON-Objekt deserialisiert, das der Karte hinzugefügt werden kann.

In den folgenden Schritten wird veranschaulicht, wie Sie eine GeoJSON-Datei in die Anwendung importieren und als `FeatureCollection`-GeoJSON-Objekt deserialisieren.

1. Schließen Sie den [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ab, da die folgenden Schritte auf dieser Anwendung aufbauen.
2. Klicken Sie im Projektbereich von Android Studio mit der rechten Maustaste auf den Ordner **app**, und navigieren Sie zu `New > Folder > Assets Folder`.
3. Ziehen Sie die GeoJSON-Datei mit den [Point of Interest-Beispielen](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) in den Ressourcenordner.

::: zone pivot="programming-language-java-android"

4. Öffnen Sie die Datei **MainActivity.java**, und fügen Sie im Rückruf für das Ereignis `mapControl.onReady` innerhalb der `onCreate`-Methode den folgenden Code hinzu. Dieser Code lädt die Datei **SamplePoiDataSet.json** aus dem Ordner „assets“ mithilfe der `importDataFromUrl`-Methode in eine Datenquelle und fügt sie dann der Karte hinzu.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("asset://SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. Öffnen Sie die Datei **MainActivity.kt**, und fügen Sie im Rückruf für das Ereignis `mapControl.onReady` innerhalb der `onCreate`-Methode den folgenden Code hinzu. Dieser Code lädt die Datei **SamplePoiDataSet.json** aus dem Ordner „assets“ mithilfe der `importDataFromUrl`-Methode in eine Datenquelle und fügt sie dann der Karte hinzu.

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("asset://SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

5. Wenn Sie den Code zum Laden der GeoJSON-Daten als Datenquelle verwenden, müssen Sie nun angeben, wie diese Daten in der Karte angezeigt werden sollen. Es gibt mehrere unterschiedliche Renderingebenen für Punktdaten. [Blasenebenen](map-add-bubble-layer-android.md), [Symbolebenen](how-to-add-symbol-to-android-map.md) und [Wärmebildebenen](map-add-heat-map-layer-android.md) werden am häufigsten verwendet. Fügen Sie den folgenden Code, mit dem die Daten auf einer Blasenebene im Rückruf für das Ereignis `mapControl.onReady` gerendert werden, nach dem Code für den Datenimport hinzu.

::: zone pivot="programming-language-java-android"

```java
//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a layer and add it to the map.
val layer = new BubbleLayer(source)
map.layers.add(layer)
```

::: zone-end

6. Klicken Sie im Projektbereich von Android Studio mit der rechten Maustaste auf den Ordner **layout** unter dem `app > res > layout`-Pfad und navigieren Sie zu `New > File`. Erstellen Sie eine neue Datei mit dem Namen **popup_text.xml**.
7. Öffnen Sie die Datei **popup_text.xml**. Wenn die Datei in einer Designeransicht geöffnet wird, klicken Sie mit der rechten Maustaste auf den Bildschirm, und wählen Sie „Zu XML wechseln“ aus. Kopieren Sie den folgenden XML-Code und fügen Sie ihn in diese Datei ein. Dieser XML-Code erstellt ein einfaches Layout, das in einem Popup verwendet werden kann und eine Textansicht enthält.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

::: zone pivot="programming-language-java-android"

8. Wechseln Sie zurück zur Datei **MainActivity.java** und fügen Sie nach dem Code für die Blasenebene den folgenden Code ein, mit dem ein wiederverwendbares Popup erstellt wird.

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

//Close it initially.
popup.close();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

8. Wechseln Sie zurück zur Datei **MainActivity.kt** und fügen Sie nach dem Code für die Blasenebene den folgenden Code ein, mit dem ein wiederverwendbares Popup erstellt wird.

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)
    
//Close it initially.
popup.close()
```

::: zone-end

9. Fügen Sie den folgenden Code ein, um ein Klickereignis mit der Blasenebene zu verknüpfen. Sobald eine Blase in der Blasenebene angeklickt wird, wird das Ereignis ausgelöst, das zuerst einige Details aus den Eigenschaften des ausgewählten Features abruft. Danach erstellt es eine Ansicht mithilfe der Layoutdatei **popup_text.xml**, übergibt sie als Inhalt an das Popup und zeigt das Popup dann an der Position der jeweiligen Features an.

::: zone pivot="programming-language-java-android"

```java
//Add a click event to the layer.
map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Display the name and entity type information of the feature into the text view of the popup layout.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText("%s\n%s",
        f.getStringProperty("Name"),
        f.getStringProperty("EntityType")
    );

    //Get the position of the clicked feature.
    Position pos = MapMath.getPosition((Point)f.geometry());

    //Set the options on the popup.
    popup.setOptions(
            //Set the popups position.
            position(pos),

            //Set the anchor point of the popup content.
            anchor(AnchorType.BOTTOM),

            //Set the content of the popup.
            content(customView)
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue to bubble up.
    return false;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a click event to the layer.
map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Display the name and entity type information of the feature into the text view of the popup layout.
    val tv = customView.findViewById<TextView>(R.id.message)
    tv.text = String.format(
        "%s\n%s",
        f.getStringProperty("Name"),
        f.getStringProperty("EntityType")
    )

    //Get the position of the clicked feature.
    val pos = MapMath.getPosition(f.geometry() as Point?)

    //Set the options on the popup.
    popup.setOptions( //Set the popups position.
        position(pos),  //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  //Set the content of the popup.
        content(customView)
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue to bubble up.
    false
} as OnFeatureClick, layer)
```

::: zone-end

10. Führen Sie die Anwendung aus. Es wird eine Karte angezeigt, die sich überlagernde Blasen für die einzelnen Standorte der GeoJSON-Datei enthält. Wenn Sie auf eine Blase klicken, wird ein Popup mit dem Namen und dem Entitätstyp des ausgewählten Features angezeigt.

    ![Karte mit Daten aus einer GeoJSON-Datei, die in einem Popup angezeigt werden, das nach dem Anklicken des jeweiligen Standorts geöffnet wird.](media/tutorial-load-geojson-file-android/android-import-geojson.gif)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die folgenden Schritte aus, um die Ressourcen dieses Tutorials zu bereinigen:

1. Schließen Sie Android Studio, und löschen Sie die von Ihnen erstellte Anwendung.
2. Falls Sie die Anwendung auf einem externen Gerät getestet haben, sollten Sie die Anwendung auf diesem Gerät deinstallieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Codebeispiele und eine Benutzeroberfläche für das interaktive Codieren:

> [!div class="nextstepaction"]
> [Verwenden von datengesteuerten Formatvorlagenausdrücken](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Anzeigen von Featureinformationen](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](how-to-add-shapes-to-android-map.md)
