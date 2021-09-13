---
title: Anzeigen von Verkehrsdaten auf Android-Karten | Microsoft Azure Maps
description: In diesem Artikel erfahren Sie, wie Sie mithilfe des Android SDK für Microsoft Azure Maps auf einer Karte Verkehrsdaten anzeigen.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 012fdf6e35b0b0c27f8ad9afe10b5f70709fcc64
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093557"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Anzeigen von Verkehrsdaten auf der Karte (Android SDK)

Flussdaten und Störungsdaten sind die zwei Arten von Verkehrsdaten, die auf der Karte angezeigt werden können. In diesem Leitfaden wird gezeigt, wie Sie beide Arten von Verkehrsdaten anzeigen können. Störungsdaten bestehen aus punkt- und linienbasierten Daten für Dinge wie Baustellen, Straßensperrungen und Unfälle. Flussdaten zeigen Metriken zum Verkehrsfluss auf der Straße.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die Schritte im Dokument [Schnellstart: Erstellen einer Android-App](quick-android-map.md) ausführen. Codeblöcke in diesem Artikel können in den `onReady`-Ereignishandler von Karten eingefügt werden.

## <a name="show-traffic-on-the-map"></a>Anzeigen von Datenverkehr auf einer Karte

In Azure Maps sind zwei Arten von Verkehrsdaten verfügbar:

- Vorfallsdaten: Punkt- und linienbasierte Daten, z. B. für Baustellen, Straßensperrungen und Unfälle.
- Flussdaten: Stellt Metriken zum Verkehrsfluss auf Straßen bereit. Daten zum Verkehrsfluss werden häufig verwendet, um Straßen farbig zu markieren. Die Farben basieren auf dem Verkehrsaufkommen, das aufgrund einer Geschwindigkeitsbeschränkung oder anderen Metrik zu einer Verlangsamung des Verkehrsflusses führt. An die Verkehrsflussoption `flow` der Karte können vier Werte übergeben werden.

    |Flow-Enumeration | BESCHREIBUNG|
    | :-- | :-- |
    | `TrafficFlow.NONE` | Zeigt keine Verkehrsdaten auf der Karte an. |
    | `TrafficFlow.RELATIVE` | Zeigt Verkehrsdaten an, die relativ zur Geschwindigkeit bei ungehindertem Verkehrsfluss auf der Straße sind. |
    | `TrafficFlow.RELATIVE_DELAY` | Zeigt Bereiche mit einer langsameren Geschwindigkeit als der durchschnittlichen erwarteten Verzögerung an. |
    | `TrafficFlow.ABSOLUTE` | Zeigt die absolute Geschwindigkeit aller Fahrzeuge auf der Straße an. |

Der folgende Code verdeutlicht, wie Sie Verkehrsdaten auf der Karte anzeigen.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

Der folgende Screenshot veranschaulicht, wie durch den oben aufgeführten Code Verkehrsinformationen in Echtzeit auf der Karte gerendert werden.

![Karte mit Verkehrsinformationen in Echtzeit](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Abrufen von Details zu Verkehrsstörungen

Details zu Verkehrsstörungen sind in den Eigenschaften des Features verfügbar, das zum Anzeigen der Störung auf der Karte verwendet wird. Verkehrsstörungen werden der Karte mithilfe des Vektorkacheldiensts für Verkehrsstörungen in Azure Maps hinzugefügt. Das Format der Daten in diesen Vektorkacheln ist [hier dokumentiert](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). Durch den folgenden Code werden der Karte ein Click-Ereignis hinzugefügt, das Feature für Verkehrsstörungen abgerufen, auf das geklickt wurde, und eine Popupmeldung mit einigen Details angezeigt.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

Der folgende Screenshot veranschaulicht, wie durch den oben aufgeführten Code Verkehrsinformationen in Echtzeit auf der Karte gerendert und eine Popupmeldung mit Details zur Störung angezeigt werden.

![Karte mit Verkehrsinformationen in Echtzeit und einer Popupmeldung, die Details zur Störung enthält](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="filter-traffic-incidents"></a>Filtern von Verkehrsstörungen

An einem gewöhnlichen Tag gibt es in den meisten Großstädten eine überwältigende Anzahl von Verkehrsstörungen. Je nach Szenario kann es jedoch wünschenswert sein, eine Teilmenge dieser Störungen zu filtern und anzuzeigen. Beim Festlegen von Datenverkehrsoptionen gibt es `incidentCategoryFilter`- und `incidentMagnitudeFilter`-Optionen, die ein Array von Incidentkategorien oder Größenenumeratoren oder Zeichenfolgenwerten enthalten.

In der folgenden Tabelle sind alle Kategorien von Verkehrsstörungen aufgeführt, die innerhalb der Option `incidentCategoryFilter` verwendet werden können.

| Kategorieenumeration | Zeichenfolgenwert | BESCHREIBUNG |
|--------------------|--------------|-------------|
| `IncidentCategory.UNKNOWN` | `"unknown"` | Eine Störung, die entweder nicht zu einer der definierten Kategorien passt oder noch nicht klassifiziert wurde. |
| `IncidentCategory.ACCIDENT` | `"accident"` | Verkehrsunfall. |
| `IncidentCategory.FOG` | `"fog"` | Nebel, der die Sicht einschränkt, den Verkehrsfluss behindert und möglicherweise das Unfallrisiko erhöht. |
| `IncidentCategory.DANGEROUS_CONDITIONS` | `"dangerousConditions"` | Gefährliche Situation auf der Straße, z. B. ein Gegenstand auf der Fahrbahn. |
| `IncidentCategory.RAIN` | `"rain"` | Starker Regen, der die Sicht beeinträchtigt, die Fahrbedingungen erschwert und möglicherweise das Unfallrisiko erhöht. |
| `IncidentCategory.ICE` | `"ice"` | Vereiste Fahrbahnen, die das Fahren erschweren oder gefährlich machen können. |
| `IncidentCategory.JAM` | `"jam"` | Stau, der zu einem langsameren Verkehrsfluss führt. |
| `IncidentCategory.LANE_CLOSED` | `"laneClosed"` | Eine Fahrspur ist gesperrt. |
| `IncidentCategory.ROAD_CLOSED` | `"roadClosed"` | Eine Straße ist gesperrt. |
| `IncidentCategory.ROAD_WORKS` | `"roadWorks"` | Straßenarbeiten/Bauarbeiten in diesem Bereich. |
| `IncidentCategory.WIND` | `"wind"` | Starker Wind, der das Fahren für Fahrzeuge mit einem großen Seitenprofil oder einem hohen Schwerpunkt erschweren kann. |
| `IncidentCategory.FLOODING` | `"flooding"` | Überschwemmungen auf der Straße. |
| `IncidentCategory.DETOUR` | `"detour"` | Verkehr wird über eine Umleitung geleitet. |
| `IncidentCategory.CLUSTER` | `"cluster"` | Eine Ansammlung von Verkehrsstörungen verschiedener Kategorien. Beim Vergrößern der Karte wird der Cluster in die einzelnen Störungen aufgegliedert. |
| `IncidentCategory.BROKEN_DOWN_VEHICLE` | `"brokenDownVehicle"` | Pannenfahrzeug auf oder neben der Straße. |

In der folgenden Tabelle sind alle Größenordnungen von Verkehrsstörungen aufgeführt, die innerhalb der Option `incidentMagnitudeFilter` verwendet werden können.

| Größenordnungsenumeration | Zeichenfolgenwert | BESCHREIBUNG |
|--------------------|--------------|-------------|
| `IncidentMagnitude.UNKNOWN` | `"unknown"` | Eine Störung, deren Größenordnung noch nicht klassifiziert wurde. |
| `IncidentMagnitude.MINOR` | `"minor"` | Ein geringfügiges Verkehrsproblem, das oft nur der Information dient und nur minimale Auswirkungen auf den Verkehrsfluss hat. |
| `IncidentMagnitude.MODERATE` | `"moderate"` | Ein mäßiges Verkehrsproblem, das sich etwas auf den Verkehrsfluss auswirkt. |
| `IncidentMagnitude.MAJOR` | `"major"` |  Ein größeres Verkehrsproblem, das erhebliche Auswirkungen auf den Verkehrsfluss hat. |

Im Folgenden werden Verkehrsstörungen so gefiltert, dass nur mäßige Staus und Störungen mit gefährlichen Bedingungen auf der Karte angezeigt werden.

::: zone pivot="programming-language-java-android"

``` java
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(new String[] { IncidentMagnitude.MODERATE }),
    incidentCategoryFilter(new String[] { IncidentCategory.DANGEROUS_CONDITIONS, IncidentCategory.JAM })              
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(*arrayOf(IncidentMagnitude.MODERATE)),
    incidentCategoryFilter(
        *arrayOf(
            IncidentCategory.DANGEROUS_CONDITIONS,
            IncidentCategory.JAM
        )
    )
)
```

::: zone-end

Der folgende Screenshot zeigt eine Karte mit mäßigen Verkehrsstaus und Störungen mit gefährlichen Bedingungen.

![Karte mit mäßigen Verkehrsstaus und Störungen mit gefährlichen Bedingungen.](media/how-to-show-traffic-android/android-traffic-incident-filters.jpg)

> [!NOTE]
> Einigen Verkehrsstörungen können mehrere Kategorien zugewiesen sein. Wenn eine Störung einer Kategorie angehört, die einer in `incidentCategoryFilter` übergebenen Option entspricht, wird sie angezeigt. Die primäre Störungskategorie kann sich von den im Filter angegebenen Kategorien unterscheiden und daher ein anderes Symbol anzeigen.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die folgenden Leitfäden an, um zu erfahren, wie Sie Ihrer Karte weitere Daten hinzufügen:

> [!div class="nextstepaction"]
> [Hinzufügen einer Kachelebene](how-to-add-tile-layer-android-map.md)
