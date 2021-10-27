---
title: Erste Schritte mit dem iOS-Kartensteuerelement | Microsoft Azure Maps
description: Machen Sie sich mit dem Azure Maps iOS SDK vertraut. Hier erfahren Sie, wie sie das SDK installieren und eine interaktive Karte erstellen.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/08/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 53d5b70f62d3ccea3d881a4e91a4b16287e1aa49
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043337"
---
# <a name="getting-started-with-azure-maps-ios-sdk-public-preview"></a>Erste Schritte mit dem Azure Maps iOS SDK (Public Preview)

Das Azure Maps iOS SDK ist eine Vektorkartenbibliothek für iOS. Dieser Artikel erklärt die Installation des Azure Maps iOS SDK und das Laden einer Karte.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer iOS-App](quick-ios-app.md) aus.

## <a name="localizing-the-map"></a>Lokalisieren der Karte

Das Azure Maps iOS SDK bietet drei verschiedene Möglichkeiten, die Sprache und die regionale Ansicht der Karte festzulegen. Der folgende Code zeigt die verschiedenen Möglichkeiten, die *Sprache* auf Französisch („fr-FR“) und die *regionale Ansicht* auf „Auto“ festzulegen.

1. Übergeben Sie die Informationen zu Sprache und regionaler Ansicht mithilfe der statischen Eigenschaften `language` und `view` an die Klasse `AzureMaps`. Damit werden die Standardsprache und die Eigenschaften der regionalen Ansicht in Ihrer App festgelegt.

    ```swift
    // Alternatively use Azure Active Directory authenticate.
    AzureMaps.configure(aadClient: "<Your aad clientId>", aadAppId: "<Your aad AppId>", aadTenant: "<Your aad Tenant>")
    
    // Set your Azure Maps Key.
    // AzureMaps.configure(subscriptionKey: "<Your Azure Maps Key>")
    
    // Set the language to be used by Azure Maps.
    AzureMaps.language = "fr-FR"
    
    // Set the regional view to be used by Azure Maps.
    AzureMaps.view = "Auto"
    ```

1. Sie können die Informationen zu Sprache und regionaler Ansicht auch an das Kartensteuerelement übergeben.

    ```swift
    MapControl(options: [
        StyleOptions.language("fr-FR"),
        StyleOptions.view("Auto")
    ])
    ```

1. Die dritte Möglichkeit, die Eigenschaften von Sprache und regionaler Ansicht programmgesteuert festzulegen, verwendet die `setStyle`-Methode für Karten. Diese können Sie jederzeit verwenden, um die Sprache und regionale Ansicht der Karte zu ändern.

    ```swift
    mapControl.getMapAsync { map in
        map.setStyle([
            StyleOptions.language("fr-FR"),
            StyleOptions.view("Auto")
        ])
    }
    ```

Hier sehen Sie ein Beispiel für eine Azure Maps-App, bei der die Sprache auf „fr-FR“ und die regionale Ansicht auf „Auto“ festgelegt wurden.

:::image type="content" source="media/ios-sdk/how-to-use-ios-map-control-library/fr-borderless.png" alt-text="Bild einer Karte mit Beschriftungen auf Französisch":::

Eine vollständige Liste der unterstützten Sprachen und regionalen Ansichten finden Sie unter [Lokalisierungsunterstützung für Azure Maps](supported-languages.md).

## <a name="navigating-the-map"></a>Navigieren auf einer Karte

In diesem Abschnitt werden die verschiedenen Möglichkeiten zum Navigieren in einem Azure Maps-Programm beschrieben.

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

Das Azure Maps iOS SDK unterstützt die Azure Government-Cloud. Geben Sie an, welche Azure Maps Government-Clouddomäne verwendet werden soll, indem Sie die folgende Codezeile dort hinzufügen, wo die Azure Maps-Authentifizierungsdetails angegeben sind:

```
AzureMaps.domain = "atlas.azure.us"
```

Verwenden Sie beim Authentifizieren der Karte und Dienste unbedingt die Azure Maps-Authentifizierungsinformationen der Azure Government-Cloudplattform.