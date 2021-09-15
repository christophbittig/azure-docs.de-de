---
title: Herstellen einer Verbindung mit einem WFS-Dienst (Web Feature Service) | Microsoft Azure Maps
description: Erfahren Sie, wie Sie eine Verbindung mit einem WFS-Dienst herstellen und anschließend den WFS-Dienst mit dem Azure Maps Web SDK und Modul Spatial IO abfragen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: c870767c63c265c13e18f527d20954f8e72abae5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437704"
---
# <a name="connect-to-a-wfs-service"></a>Herstellen einer Verbindung mit einem WFS-Dienst

Ein WFS-Dienst (Web Feature Service) ist ein Webdienst zum Abfragen räumlicher Daten mit einer standardisierten API, die vom Open Geospatial Consortium (OGC) definiert wurde. Die Klasse `WfsClient` im Modul Spatial IO ermöglicht Entwicklern, eine Verbindung mit einem WFS-Dienst herzustellen und Daten aus dem Dienst abzufragen.

Die folgenden Features werden von der Klasse `WfsClient` unterstützt:

- Unterstützte Versionen: `1.0.0`, `1.1.0` und `2.0.0`
- Unterstützte Filteroperatoren: binäre Vergleichs-, Logik-, mathematische und Wertoperatoren sowie `bbox`.
- Anforderungen werden nur über `HTTP GET` gestellt.
- Unterstützte Vorgänge:

    | Vorgang | BESCHREIBUNG |
    | :-- | :-- |
    | GetCapabilities | Generiert ein Metadatendokument mit gültigen WFS-Vorgängen und -Parametern |
    | GetFeature | Gibt eine Auswahl von Features aus einer Datenquelle zurück |
    | DescribeFeatureType | Gibt die unterstützten Featuretypen zurück |

## <a name="using-the-wfs-client"></a>Verwenden des WFS-Clients

Die Klasse `atlas.io.ogc.WfsClient` im Modul Spatial IO ermöglicht die einfache Abfrage eines WFS-Diensts und die Konvertierung der Antworten in GeoJSON-Objekte. Dieses GeoJSON-Objekt kann dann für andere Kartierungszwecke genutzt werden.

Der folgende Code fragt einen WFS-Dienst ab und rendert die zurückgegebenen Features auf der Karte.

<br/>

<iframe height='700' scrolling='no' title='Simple WFS example' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Simple WFS example</a> (Einfaches WFS-Beispiel) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Unterstützte Filter

In der Spezifikation für den WFS-Standard werden OGC-Filter verwendet. Die folgenden Filter werden vom WFS-Client unterstützt, vorausgesetzt, der aufgerufene Dienst unterstützt diese Filter ebenfalls. Benutzerdefinierte Filterzeichenfolgen können an die `CustomFilter`-Klasse übergeben werden.

**Logische Operatoren**

- `And`
- `Or`
- `Not`

**Wertoperatoren**

- `GmlObjectId`
- `ResourceId`

**Mathematische Operatoren**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Vergleichsoperatoren**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

Der folgende Code veranschaulicht die Verwendung verschiedener Filter mit dem WFS-Client.

<br/>

<iframe height='500' scrolling='no' title= 'WFS filter examples' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS filter examples</a> (Beispiele für WFS-Filter) von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>WFS service explorer

Im folgenden Code wird der WFS-Client zum Erkunden von WFS-Diensten verwendet. Wählen Sie eine Eigenschaftstypebene innerhalb des Diensts aus, und sehen Sie sich die zugehörige Legende an.

<br/>

<iframe height='700' scrolling='no' title= 'WFS service explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'>Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS service explorer</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

Für den Zugriff auf WFS-Dienste, die auf nicht CORS-fähigen Endpunkten gehostet werden, kann ein CORS-fähiger Proxydienst wie unten dargestellt der `proxyService`-Option des WFS-Clients übergeben werden. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die Sie Ihren Karten hinzufügen können:

> [!div class="nextstepaction"]
> [Nutzen von Kernvorgängen](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details zu unterstützten Datenformaten](spatial-io-supported-data-format-details.md)