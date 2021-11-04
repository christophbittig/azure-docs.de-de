---
title: Erste Schritte mit dem Android-Kartensteuerelement | Microsoft Azure Maps
description: Machen Sie sich mit dem Azure Maps Android SDK vertraut. Erfahren Sie, wie Sie ein Projekt in Android Studio erstellen, das SDK installieren und eine interaktive Karte erstellen.
author: stevemunk
ms.author: v-munksteve
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 47af15633803fe0dab299c1f2c4a80c10d32efb0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036198"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Erste Schritte mit dem Android SDK für Azure Maps

Das Android SDK in Azure Maps ist eine Vektorenzuordnungsbibliothek für Android. Dieser Artikel führt Sie durch die Installation des Android SDK für Azure Maps und das Laden einer Karte.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Android-App](quick-android-map.md) aus.

## <a name="localizing-the-map"></a>Lokalisieren der Karte

Das Azure Maps Android SDK bietet drei verschiedene Möglichkeiten, die Sprache und die regionale Ansicht der Karte festzulegen. Der folgende Code zeigt, wie die Sprache auf Französisch („fr-FR“) und die regionale Ansicht auf „Auto“ festgelegt werden.

1. Übergeben Sie die Informationen zu Sprache und regionaler Ansicht mithilfe der statischen Eigenschaften `setLanguage` und `setView` an die Klasse `AzureMaps`. Damit werden die Standardsprache und die Eigenschaften der regionalen Ansicht in Ihrer App festgelegt.
    
    ::: zone pivot="programming-language-java-android"
    
    ```java
    static {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
    ```
    
    ::: zone-end
    
    ::: zone pivot="programming-language-kotlin"
    
    ```kotlin
    companion object {
        init {
            //Alternatively use Azure Active Directory authenticate.
            AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
            //Set your Azure Maps Key.
            //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
        
            //Set the language to be used by Azure Maps.
            AzureMaps.setLanguage("fr-FR");
        
            //Set the regional view to be used by Azure Maps.
            AzureMaps.setView("Auto");
        }
    }
    ```
    
    ::: zone-end

1. Sie können die Informationen zu Sprache und regionaler Ansicht auch an den XML-Code des Kartensteuerelements übergeben.

    ```xml
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        />
    ```
1. Die dritte Möglichkeit, die Eigenschaften von Sprache und regionaler Ansicht programmgesteuert festzulegen, verwendet die `setStyle`-Methode für Karten. Dies kann jederzeit geschehen, um die Sprache und regionale Ansicht der Karte zu ändern.

    ::: zone pivot="programming-language-java-android"

    ```java
    mapControl.onReady(map -> {
        map.setStyle(
            language("fr-FR"),
            view("Auto")
        );
    });
    ```

    ::: zone-end

    ::: zone pivot="programming-language-kotlin"

    ```kotlin
    mapControl.onReady(OnReady { map: AzureMap ->
        map.setStyle(
            language("fr-FR"),
            view("Auto")
        )
    })
    ```

    ::: zone-end

Hier ist ein Beispiel für Azure Maps, bei dem die Sprache auf „fr-FR“ und die regionale Ansicht auf „Auto“ festgelegt wurden.

![Azure Maps, Kartenbild mit Beschriftungen in Französisch](media/how-to-use-android-map-control-library/android-localization.png)

Eine vollständige Liste der unterstützten Sprachen und regionalen Ansichten finden Sie unter [Unterstützte Sprachen in Azure Maps](supported-languages.md).

## <a name="navigating-the-map"></a>Navigieren auf einer Karte

Es gibt verschiedene Möglichkeiten für das Zoomen, Schwenken, Drehen und Neigen der Karte. Im Folgenden werden die verschiedenen Möglichkeiten zum Navigieren auf der Karte erläutert.

### <a name="zoom-the-map"></a>Zoomen der Karte

* Berühren Sie die Karte mit zwei Fingern, und führen Sie diese zusammen, um zu verkleinern, oder führen Sie die Finger auseinander, um zu vergrößern.
* Doppeltippen Sie auf die Karte, um um eine Ebene zu vergrößern.
* Doppeltippen Sie mit zwei Fingern, um die Karte um eine Ebene zu verkleinern.
* Tippen Sie zweimal, halten Sie beim zweiten Tippen den Finger auf der Karte, und ziehen Sie nach oben, um zu vergrößern, oder nach unten, um zu verkleinern.

### <a name="pan-the-map"></a>Schwenken der Karte

* Berühren Sie die Karte, und ziehen Sie sie in eine beliebige Richtung.

### <a name="rotate-the-map"></a>Drehen der Karte

* Berühren Sie die Karte mit zwei Fingern, und drehen Sie sie.

### <a name="pitch-the-map"></a>Neigen der Karte

* Berühren Sie die Karte mit zwei Fingern, und ziehen Sie diese zusammen nach oben oder unten.

## <a name="azure-government-cloud-support"></a>Unterstützung für die Azure Government-Cloud

Das Azure Maps Android SDK unterstützt die Azure Government-Cloud. Der Zugriff auf das Azure Maps Android SDK erfolgt über dasselbe Maven-Repository. Die folgenden Aufgaben müssen ausgeführt werden, um eine Verbindung mit der Azure Government-Cloudversion der Azure Maps-Plattform herzustellen.

Fügen Sie an derselben Stelle, an der die Azure Maps-Authentifizierungsdetails angegeben werden, die folgende Codezeile hinzu, um die Karte anzuweisen, die Government-Clouddomäne von Azure Maps zu verwenden.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Verwenden Sie beim Authentifizieren der Karte und Dienste unbedingt die Azure Maps-Authentifizierungsinformationen der Azure Government-Cloudplattform.

## <a name="migrating-from-a-preview-version"></a>Migrieren von einer Vorschauversion

Beim Wechsel von der Vorschauversion zur allgemeinen Verfügbarkeit haben sich einige Breaking Changes für das Azure Maps Android SDK ergeben. Im Anschluss finden Sie die wichtigsten Details:

* Der Maven-Bezeichner wurde von `"com.microsoft.azure.maps:mapcontrol:0.7"` in `"com.azure.android:azure-maps-control:1.0.0"` geändert. Der Namespace und die Hauptversionsnummer wurden geändert.
* Der Namespace für den Import wurde von `com.microsoft.azure.maps.mapcontrol` in `com.azure.android.maps.control` geändert.
* Bei Ressourcennamen für XML-Optionen, Farbressourcen und Bildressourcen wurde der Text `mapcontrol_` durch `azure_maps_` ersetzt.

    **Vorher:**

    ```xml
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
    />
    ```

    **Nachher:**

    ```xml
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        app:azure_maps_centerLat="47.602806"
        app:azure_maps_centerLng="-122.329330"
        app:azure_maps_zoom="12"
    />
    ```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie der Karte Overlaydaten hinzugefügt werden:

> [!div class="nextstepaction"]
> [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

> [!div class="nextstepaction"]
> [Ändern von Kartenstilen auf Android-Karten](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Symbolebene](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](how-to-add-shapes-to-android-map.md)
