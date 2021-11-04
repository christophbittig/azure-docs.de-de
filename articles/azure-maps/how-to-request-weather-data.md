---
title: Anfordern von Echtzeit- und prognostizierten Wetterdaten mit Azure Maps-Wetterinfodiensten
description: Es wird beschrieben, wie Sie Echtzeitwetterdaten (aktuell) und prognostizierte Wetterdaten (minütlich, stündlich oder täglich) über die Wetterinfodienste von Microsoft Azure Maps anfordern.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 6ee9fefcb0675f35eebfe78b980d6e04d44f5a72
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435261"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services"></a>Anfordern von Echtzeit- und prognostizierten Wetterdaten mit Azure Maps-Wetterinfodiensten

Bei den [Wetterinfodiensten](/rest/api/maps/weather) von Azure Maps handelt es sich um eine Reihe von RESTful-APIs, mit denen Entwickler extrem dynamische Verlaufs-, Echtzeit- und Vorhersagewetterdaten und die zugehörigen Visualisierungen in ihre Lösungen integrieren können. In diesem Artikel wird veranschaulicht, wie Sie Echtzeit- und prognostizierte Wetterdaten anfordern.

Folgendes wird beschrieben:

* Anfordern von Echtzeitwetterdaten (aktuell) mit der [Get Current Conditions-API](/rest/api/maps/weather/getcurrentconditions)
* Anfordern von Unwetterwarnungen mit der [Get Severe Weather Alerts-API](/rest/api/maps/weather/getsevereweatheralerts)
* Anfordern von Tagesvorhersagen mit der [Get Daily Forecast-API](/rest/api/maps/weather/getdailyforecast)
* Anfordern von stündlichen Vorhersagen mit der [Get Hourly Forecast-API](/rest/api/maps/weather/gethourlyforecast)
* Anfordern von minütlichen Vorhersagen mit der [Get Minute Forecast-API](/rest/api/maps/weather/getminuteforecast)

Dieses Video enthält Beispiele dafür, wie REST-Aufrufe für die Wetterinfodienste von Azure Maps ausgeführt werden.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt) Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](./how-to-manage-authentication.md).

    >[!IMPORTANT]
    >Für die [Get Minute Forecast-API](/rest/api/maps/weather/getminuteforecast) ist der Tarif „Gen 1 (S1)“ oder „Gen 2“ erforderlich.  Für alle anderen APIs ist ein Schlüssel für den Tarif S0 erforderlich.

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung verwenden.

## <a name="request-real-time-weather-data"></a>Anfordern von Echtzeitwetterdaten

Mit der [Get Current Conditions-API](/rest/api/maps/weather/getcurrentconditions) werden ausführliche Informationen zu Wetterbedingungen zurückgegeben, z. B. Niederschlag, Temperatur und Windgeschwindigkeit für einen bestimmten Koordinatenstandort. Außerdem können die beobachteten Daten für die letzten 6 oder 24 Stunden für einen bestimmten Standort abgerufen werden. Die Antwort enthält Details wie Datum und Uhrzeit der Beobachtung, eine kurze Beschreibung der Wetterbedingungen, das Wettersymbol, Niederschlagsanzeigen und Temperatur. Die RealFeel™-Temperatur und der UV-Index werden ebenfalls zurückgegeben.

In diesem Beispiel verwenden Sie die [Get Current Conditions-API](/rest/api/maps/weather/getcurrentconditions), um die aktuellen Wetterbedingungen für einen Koordinatenstandort in Seattle (WA) abzurufen.

1. Öffnen Sie die Postman-App. Wählen Sie **Neu** aus, um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **HTTP Request** (HTTP-Anforderung) aus. Geben Sie einen Anforderungsnamen (**Request name**) ein. 

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. Klicken Sie auf die Schaltfläche **Send** (Senden). Der Text der Antwort enthält die aktuellen Wetterinformationen.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Anfordern von Unwetterwarnungen

Mit der [Azure Maps Get Severe Weather Alerts-API](/rest/api/maps/weather/getsevereweatheralerts) werden die Unwetterwarnungen zurückgegeben, die weltweit sowohl von offiziellen staatlichen Stellen für Meteorologie als auch von führenden globalen oder regionalen Wetterdiensten bereitgestellt werden. Mit diesem Dienst können Details wie Warnungstyp, -kategorie und -ebene sowie ausführliche Beschreibungen zu den bestehenden Unwetterwarnungen für den angeforderten Standort, z. B. Hurrikane, Gewitter, Blitze, Hitze oder Waldbrände, zurückgegeben werden. Logistikmanager können Unwetter zusammen mit Geschäftsstandorten und geplanten Routen beispielsweise auf einer Karte visualisieren und so eine bessere Koordinierung mit Fahrern und dem Personal vor Ort ermöglichen.

In diesem Beispiel verwenden Sie die [Get Severe Weather Alerts-API](/rest/api/maps/weather/getsevereweatheralerts), um die aktuellen Wetterbedingungen für einen Koordinatenstandort in Cheyenne (WY) abzurufen.

>[!NOTE]
>Dieses Beispiel veranschaulicht das Abrufen von Unwetterwarnungen, die bei der Erstellung dieses Dokuments bestanden haben. Wahrscheinlich gelten für den angeforderten Standort mittlerweile keine Unwetterwarnungen mehr. Wenn Sie bei der Ausführung dieses Beispiels aktuelle Unwetterdaten abrufen möchten, müssen Sie die Daten eines anderen Koordinatenstandorts verwenden.

1. Wählen Sie in der Postman-App **Neu** aus, um die Anforderung zu erstellen. Klicken Sie im Fenster **Create New** (Neu erstellen) auf **HTTP Request** (HTTP-Anforderung). Geben Sie einen Anforderungsnamen (**Request name**) ein.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. Klicken Sie auf die Schaltfläche **Send** (Senden). Falls keine Unwetterwarnungen vorhanden sind, enthält der Antworttext ein leeres `results[]`-Array. Bei vorhandenen Unwetterwarnungen enthält der Antworttext eine JSON-Antwort der folgenden Art:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Anfordern von täglichen Wettervorhersagedaten

Mit der [Get Daily Forecast-API](/rest/api/maps/weather/getdailyforecast) werden Daten für die ausführliche tägliche Wettervorhersage zurückgegeben, z. B. Temperatur und Windgeschwindigkeit. Hierbei kann angegeben werden, für wie viele Tage Daten zurückgegeben werden sollen: 1, 5, 10, 15, 25 oder 45 Tage für einen bestimmten Koordinatenstandort. Die Antwort enthält Details wie Temperatur, Windgeschwindigkeit, Niederschlag, Luftqualität und UV-Index.  In diesem Beispiel fordern wir Daten für fünf Tage an, indem wir `duration=5` angeben.

>[!IMPORTANT]
>Im Tarif S0 können Sie die tägliche Vorhersage für die nächsten 1, 5, 10 und 15 Tage anfordern. Im Tarif „Gen 1 (S1)“ oder „Gen 2“ können Sie eine tägliche Vorhersage für die nächsten 25 Tage und 45 Tage anfordern.

In diesem Beispiel verwenden Sie die [Get Daily Forecast-API](/rest/api/maps/weather/getdailyforecast), um die 5-Tage-Wettervorhersage für einen Koordinatenstandort in Seattle (WA) abzurufen.

1. Wählen Sie in der Postman-App **Neu** aus, um die Anforderung zu erstellen. Klicken Sie im Fenster **Create New** (Neu erstellen) auf **HTTP Request** (HTTP-Anforderung). Geben Sie einen Anforderungsnamen (**Request name**) ein.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. Klicken Sie auf die Schaltfläche **Send** (Senden). Der Antworttext enthält die Daten für die 5-Tage-Wettervorhersage. Aus Platzgründen ist unten in der JSON-Antwort nur die Vorhersage für den ersten Tag dargestellt.

    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Anfordern von stündlichen Wettervorhersagedaten

Mit der [Get Hourly Forecast-API](/rest/api/maps/weather/gethourlyforecast) wird die ausführliche stündliche Wettervorhersage für die nächsten 1, 12, 24 (1 Tag), 72 (3 Tage), 120 (5 Tage) und 240 Stunden (10 Tage) für den angegebenen Koordinatenstandort zurückgegeben. Von der API werden Details wie Temperatur, Luftfeuchtigkeit, Windgeschwindigkeit, Niederschlag und UV-Index zurückgegeben.

>[!IMPORTANT]
>Im Tarif S0 können Sie die stündliche Vorhersage für die nächsten 1, 12, 24 (1 Tag) und 72 Stunden (3 Tage) anfordern. Im Tarif „Gen 1 (S1)“ oder „Gen 2“ können Sie eine stündliche Vorhersage für die nächsten 120 Stunden (5 Tage) und 240 Stunden (10 Tage) anfordern.

In diesem Beispiel verwenden Sie die [Get Hourly Forecast-API](/rest/api/maps/weather/gethourlyforecast), um die stündliche Wettervorhersage für die nächsten zwölf Stunden für einen Koordinatenstandort in Seattle (WA) abzurufen.

1. Wählen Sie in der Postman-App **Neu** aus, um die Anforderung zu erstellen. Klicken Sie im Fenster **Create New** (Neu erstellen) auf **HTTP Request** (HTTP-Anforderung). Geben Sie einen Anforderungsnamen (**Request name**) ein.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. Klicken Sie auf die Schaltfläche **Send** (Senden). Der Antworttext enthält die Daten der Wettervorhersage für die nächsten zwölf Stunden. Aus Platzgründen ist unten in der JSON-Antwort nur die Vorhersage für die erste Stunde dargestellt.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```

## <a name="request-minute-by-minute-weather-forecast-data"></a>Anfordern von minütlichen Wettervorhersagedaten

 Mit der [Get Minute Forecast-API](/rest/api/maps/weather/getminuteforecast) werden die minütlichen Vorhersagen für einen bestimmten Standort für die nächsten 120 Minuten zurückgegeben. Benutzer können Wettervorhersagen in Intervallen von 1, 5 und 15 Minuten anfordern. Die Antwort enthält Details wie die Niederschlagsart (z. B. Regen, Schnee oder Schneeregen), die Startzeit und den Wert für die Niederschlagsintensität.

In diesem Beispiel verwenden Sie die [Get Minute Forecast-API](/rest/api/maps/weather/getminuteforecast), um die minütliche Wettervorhersage für einen Koordinatenstandort in Seattle (WA) abzurufen. Die Wettervorhersagedaten werden für die nächsten 120 Minuten zurückgegeben. Mit unserer Abfrage fordern wir an, dass die Vorhersage in Intervallen von 15 Minuten erfolgen soll, aber Sie können den Parameter auch anpassen und auf 1 oder 5 Minuten festlegen.

1. Wählen Sie in der Postman-App **Neu** aus, um die Anforderung zu erstellen. Klicken Sie im Fenster **Create New** (Neu erstellen) auf **HTTP Request** (HTTP-Anforderung). Geben Sie einen Anforderungsnamen (**Request name**) ein.

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Your-Azure-Maps-Primary-Subscription-key}
    ```

3. Klicken Sie auf die Schaltfläche **Send** (Senden). Der Antworttext enthält Wettervorhersagedaten für die nächsten 120 Minuten (in Intervallen von 15 Minuten).

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Wetterinfodienste in Azure Maps](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [REST-API für Wetterinfodienste in Azure Maps](/rest/api/maps/weather)
