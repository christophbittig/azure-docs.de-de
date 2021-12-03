---
title: Übereinstimmungsbedingungen der Regel-Engine für Azure CDN Premium von Verizon
description: Referenzdokumentation zu den Übereinstimmungsbedingungen der Regel-Engine für Azure Content Delivery Network Premium von Verizon.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: duau
ms.openlocfilehash: 2c0c509b2e2fedabb779e135fc3234d116df6b57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282061"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Übereinstimmungsbedingungen der Regel-Engine für Azure CDN Premium von Verizon

Dieser Artikel enthält ausführliche Beschreibungen der verfügbaren Übereinstimmungsbedingungen für die [Regel-Engine](cdn-verizon-premium-rules-engine.md) des Azure Content Delivery Network (CDN) Premium von Verizon.

Der zweite Teil einer Regel ist die Übereinstimmungsbedingung. Eine Übereinstimmungsbedingung gibt bestimmte Typen von Anforderungen an, für die verschiedene sog. Features ausgeführt werden.

Sie können eine Übereinstimmungsbedingung z.B. für Folgendes verwenden:

- Filtern von Anforderungen für Inhalte an einem bestimmten Standort
- Filtern von Anforderungen, die von einer bestimmten IP-Adresse oder einem bestimmten Land bzw. einer bestimmten Region generiert wurden
- Filtern von Anforderungen nach Headerinformationen

## <a name="match-conditions"></a><a name="top"></a>Übereinstimmungsbedingungen

* [Always](#always)
* [Device](#device)
* [Location](#location)
* [Ursprung](#origin)
* [Anforderung](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>Always (Immer)

Die [Übereinstimmungsbedingung „Always“ (Immer)](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) dient dazu, eine Standardmenge von Features auf alle Anforderungen anzuwenden.

### <a name="device"></a><a name="device"></a>Device (Gerät)

Diese Übereinstimmungsbedingungen dienen dazu, Anforderungen anhand des Benutzer-Agents des Clients zu ermitteln.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| Brand Name | Bestimmt Anforderungen, indem geprüft wird, ob der Markenname des Geräts mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([Brand Name Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm), „Markenname, wörtlich“) <br> **-** Einem regulären Ausdruck ([Brand Name Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm), „Markenname, RegEx“) <br> **-** Einem bestimmten Muster ([Brand Name Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm), „Markenname, Platzhalter“) |
| Device OS | Bestimmt Anforderungen, indem geprüft wird, ob das Betriebssystem des Geräts mit Folgendem übereinstimmt: <br> **-** Einem bestimmen Wert ([Device OS Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm), „Gerätebetriebssystem, wörtlich“) <br> **-** Einem regulären Ausdruck ([Device OS Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm), „Gerätebetriebssystem, RegEx“) <br> **-** Einem bestimmten Muster ([Device OS Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm), „Gerätebetriebssystem, Platzhalter“) |
| Device OS Version | Bestimmt Anforderungen, indem geprüft wird, ob die Betriebssystemversion des Geräts mit Folgendem übereinstimmt: <br> **-** Einem bestimmen Wert ([Device OS Version Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm), „Betriebssystemversion des Geräts, wörtlich“) <br> **-** Einem regulären Ausdruck ([Device OS Version Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm), „Betriebssystemversion des Geräts, RegEx“) <br> **-** Einem bestimmten Muster ([Device OS Version Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm), „Betriebssystemversion des Geräts, Platzhalter“) |
| [Dual Orientation?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) (Duale Ausrichtung?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät die duale Ausrichtung unterstützt |
| HTML Preferred DTD | Bestimmt Anforderungen, indem geprüft wird, ob die bevorzugte Dokumenttypdefinition (DTD) des Geräts für HTML-Inhalte mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([HTML Preferred DTD Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm), „Bevorzugte Dokumenttypdefinition für HTML-Inhalte, wörtlich“) <br> **-** Einem regulären Ausdruck ([HTML Preferred DTD Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm), „Bevorzugte Dokumenttypdefinition für HTML-Inhalte, RegEx“) <br> **-** Einem bestimmten Muster ([HTML Preferred DTD Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm), „Bevorzugte Dokumenttypdefinition für HTML-Inhalte, Platzhalter“) |
| [Image Inlining?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) (Imageinlining?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät Base64 -codierte Images unterstützt |
| [Is Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) (Android?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät Android verwendet |
| [Is App?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) (App?) | Bestimmt Anforderungen, indem überprüft wird, ob eine native Anwendung Inhalte angefordert hat |
| [Is Full Desktop?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) (Vollständiger Desktop?) | Bestimmt Anforderungen, indem überprüft wird, ob das Gerät über einen vollständigen Desktop verfügt |
| [Is iOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) (iOS?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät iOS verwendet. |
| [Is Robot?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) (Roboter?) | Bestimmt Anforderungen, indem überprüft wird, ob das Gerät als automatisierter HTTP-Client eingestuft wird (z. B. als Robotercrawler) |
| [Is Smart TV?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) (Smart TV?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät ein Smart-TV-Gerät ist |
| [Is Smartphone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) (Smartphone?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät ein Smartphone ist
| [Is Tablet?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) (Tablet?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät ein Tablet ist |
| [Is Touchscreen?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) (Touchscreen?) | Bestimmt Anforderungen, indem geprüft wird, ob das primäre Zeigegerät des Geräts ein Touchscreen ist |
| [Is Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) (Windows Phone?) | Bestimmt Anforderungen, indem geprüft wird, ob es sich bei dem Gerät um ein Windows Handy 6.5 / Windows Phone 7 oder höher handelt. |
| [Is Wireless Device?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) (Kabelloses Gerät?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät kabellos ist 
| Marketing Name | Bestimmt Anforderungen, indem geprüft wird, ob der Marketingname des Geräts mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([Marketing Name Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm), „Marketingname, wörtlich“) <br> **-** Einem regulären Ausdruck ([Marketing Name Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm), „Marketingname, RegEx“) <br> **-** Einem bestimmten Muster ([Marketing Name Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm), „Marketingname, Platzhalter“) |
| Mobile Browser | Bestimmt Anforderungen, indem geprüft wird, ob der Browser des Geräts mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([Mobile Browser Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm), „Mobiler Browser, wörtlich“) <br> **-** Einem regulären Ausdruck ([Mobile Browser Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm), „Mobiler Browser, RegEx“) <br> **-** Einem bestimmten Muster ([Mobile Browser Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm), „Mobiler Browser, Platzhalter“) |
| Mobile Browser Version | Bestimmt Anforderungen, indem geprüft wird, ob die Browserversion des Geräts mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([Mobile Browser Version Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm), „Version des mobilen Browsers, wörtlich“) <br> **-** Einem regulären Ausdruck ([Mobile Browser Version Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm), „Version des mobilen Browsers, RegEx“) <br> **-** Einem bestimmten Muster ([Mobile Browser Version Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm), „Version des mobilen Browsers, Platzhalter“) |
| Model Name | Bestimmt Anforderungen, indem geprüft wird, ob der Modellname des Geräts mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([Model Name Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm), „Modellname, wörtlich“) <br> **-** Einem regulären Ausdruck ([Model Name Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm), „Modellname, RegEx“) <br> **-** Einem bestimmten Muster ([Model Name Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm), „Modellname, Platzhalter“) |
| [Progressive Download?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) (Progressiver Download?) | Bestimmt Anforderungen, indem geprüft wird, ob das Gerät den progressiven Download unterstützt |
| Veröffentlichungsdatum | Bestimmt Anforderungen, indem geprüft wird, ob das Releasedatum des Geräts mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([Release Date Literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm), „Releasedatum, wörtlich“) <br> **-** Einem regulären Ausdruck ([Release Date Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm), „Releasedatum, RegEx“) <br> **-** Einem bestimmten Muster ([Release Date Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm), „Releasedatum, Platzhalter“) |
| [Resolution Height](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) (Auflösung, Höhe) | Bestimmt Anforderungen anhand der Bildschirmhöhe |
| [Resolution Width](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) (Auflösung, Breite) | Bestimmt Anforderungen anhand der Bildschirmbreite |

**[Nach oben](#top)**

### <a name="location"></a><a name="location"></a>Standort

Diese Übereinstimmungsbedingungen dienen zum Bestimmen von Anforderungen basierend auf dem Standort des Anfordernden.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [AS Number](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Bestimmt Anforderungen, die aus einem bestimmten Netzwerk stammen. |
| City Name (Name der Stadt) | Bestimmt Anforderungen, indem überprüft wird, ob sie aus einer Stadt stammen, deren Name mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([City Name Literal](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm), „Name der Stadt, wörtlich“) <br> **-** Einem regulären Ausdruck ([City Name Regex](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm), „Name der Stadt, RegEx“) |
| [Kontinent](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Bestimmt Anforderungen, die aus den angegebenen Kontinenten stammen |
| [Country](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Bestimmt Anforderungen, die aus den angegebenen Ländern stammen. |
| [DMA Code](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) (DMA-Code) | Bestimmt Anforderungen, die aus den angegebenen Metropolregionen stammen (Designated Market Areas, DMAs) |
| [Latitude](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) (Breitengrad) | Bestimmt Anforderungen, die aus den angegebenen Breitengraden stammen |
| [Longitude](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) (Längengrad) | Bestimmt Anforderungen, die aus den angegebenen Längengraden stammen |
| [Metro Code](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) (Metropolcode) | Bestimmt Anforderungen, die aus den angegebenen Metropolregionen stammen (Designated Market Areas, DMAs) |
| [Postal Code](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Bestimmt Anforderungen, die aus der Region mit der angegebenen Postleitzahl stammen |
| [Region Code](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) (Regionscode) | Bestimmt Anforderungen, die aus den angegebenen Regionen stammen |

> [!NOTE]
> **Sollte ich einen Metropolcode oder einen DMA-Code verwenden?** <br>
Beide Übereinstimmungsbedingungen bieten dieselbe Funktionalität. Es wird jedoch empfohlen, die Übereinstimmungsbedingung „Metro Code“ zu verwenden, um Anforderungen nach DMA zu bestimmten.

**[Nach oben](#top)**

### <a name="origin"></a><a name="origin"></a>Origin (Ursprung)

Diese Übereinstimmungsbedingungen dienen zum Bestimmen von Anforderungen, die auf CDN-Speicher oder einen Kundenursprungsserver zeigen.

| Name       | Zweck                                                           |
|------------|-------------------------------------------------------------------|
| [CDN Origin](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Bestimmt Anforderungen von Inhalten, die in CDN-Speicher gespeichert sind. |
| [Customer Origin](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Bestimmt Anforderungen von Inhalten, die auf einem spezifischen Kundenursprungsserver gespeichert sind. |

**[Nach oben](#top)**

### <a name="request"></a><a name="request"></a>Anforderung

Diese Übereinstimmungsbedingungen dienen zum Bestimmen von Anforderungen basierend auf ihren Eigenschaften.

| Name              | Zweck                                                                |
|-------------------|------------------------------------------------------------------------|
| [Client IP Address](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Bestimmt Anforderungen, die von einer bestimmten IP-Adresse stammen. |
| Cookie Parameter  | Bestimmt Anforderungen, indem überprüft wird, ob diese ein Cookie enthalten, das mit Folgendem übereinstimmt: <br> **-** Einem bestimmen Wert ([Cookie Parameter Literal](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm), „Cookieparameter, wörtlich“) <br> **-** Einem regulären Ausdruck ([Cookie Parameter Regex](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm), „Cookieparameter, RegEx“) <br> **-** Einem bestimmten Muster ([Cookie Parameter Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm), „Cookieparameter, Platzhalter“) |
| [Edge CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) (Edge-CNAME) | Bestimmt Anforderungen, die auf einen bestimmten Edge-CNAME zeigen. |
| Referring Domain | Bestimmt Anforderungen, indem überprüft wird, ob ein Hostname darauf verweist, der mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([Referring Domain Literal](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm), „Verweisende Domäne, wörtlich“) <br> **-** Einem bestimmten Muster ([Referring Domain Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm), „Verweisende Domäne, Platzhalter“) |
| Anforderungsheader | Bestimmt Anforderungen, indem überprüft wird, ob diese einen Header enthält, der mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([Request Header Literal](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm), „Anforderungsheader, wörtlich“) <br> **-** Einem regulären Ausdruck ([Request Header Regex](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm), „Anforderungsheader, RegEx“) <br> **-** Einem bestimmten Muster ([Request Header Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm), „Anforderungsheader, Platzhalter“) |
| [Request Method](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Bestimmt Anforderungen anhand ihrer HTTP-Methode. |
| [Request Scheme](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Bestimmt Anforderungen anhand ihres HTTP-Protokolls. |

**[Nach oben](#top)**

### <a name="url"></a><a name="url"></a>URL

| Name              | Zweck                                                                |
|-------------------|------------------------------------------------------------------------|
| URL-Pfad | Bestimmt Anforderungen, indem überprüft wird, ob ihr relativer Pfad, einschließlich des Dateinamens, mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([URL Path Literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm), „URL-Pfad, wörtlich“) <br> **-** Einem regulären Ausdruck ([URL Path Regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm), „URL-Pfad, RegEx“) <br> **-** Einem bestimmten Muster ([URL Path Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm), „URL-Pfad, Platzhalter“) |
| URL Path Directory | Bestimmt Anforderungen, indem überprüft wird, ob ihr relativer Pfad mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([URL Path Directory Literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm), „URL-Pfadverzeichnis, wörtlich“) <br> **-** Einem bestimmten Muster ([URL Path Directory Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm), „URL-Pfadverzeichnis, Platzhalter“) |
| URL Path Extension | Bestimmt Anforderungen, indem überprüft wird, ob ihre Dateiendung mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([URL Path Extension Literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm), „URL-Pfadendung, wörtlich“) <br> **-** Einem bestimmten Muster ([URL Path Extension Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm), „URL-Pfadendung, Platzhalter“) |
| URL Path Filename | Bestimmt Anforderungen, indem überprüft wird, ob ihr Dateiname mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([URL Path Filename Literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm), „URL-Pfaddateiname, wörtlich“) <br> **-** Einem bestimmten Muster ([URL Path Filename Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm), „URL-Pfaddateiname, Platzhalter“) |
| URL Query (URL-Abfrage) | Bestimmt Anforderungen, indem überprüft wird, ob ihre Abfragezeichenfolge mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([URL Query Literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm), „URL-Abfrage, wörtlich“) <br> **-** Einem regulären Ausdruck ([URL Query Regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm), „URL-Abfrage, RegEx“) <br> **-** Einem bestimmten Muster ([URL Query Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm), „URL-Abfrage, Platzhalter“) |
| URL Query Parameter | Bestimmt Anforderungen, indem überprüft wird, ob sie einen Abfragezeichenfolgen-Parameter enthalten, der auf einen Wert festgelegt ist, der mit Folgendem übereinstimmt: <br> **-** Einem bestimmten Wert ([URL Query Parameter Literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm), „URL-Abfrageparameter, wörtlich“) <br> **-** Einem bestimmten Muster ([URL Query Parameter Wildcard](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm), „URL-Abfrageparameter, Platzhalter“) |

**[Nach oben](#top)**

Die neuesten Übereinstimmungsbedingungen finden Sie in der [Dokumentation zur Verizon-Regel-Engine](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio).

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure Content Delivery Network](cdn-overview.md)
- [Regel-Engine – Referenz](cdn-verizon-premium-rules-engine-reference.md)
- [Regel-Engine – bedingte Ausdrücke](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regel-Engine – Features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Überschreiben des HTTP-Standardverhaltens mithilfe der Regel-Engine](cdn-verizon-premium-rules-engine.md)
