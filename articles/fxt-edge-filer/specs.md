---
title: Spezifikationen von Microsoft Azure FXT Edge Filer | Microsoft-Dokumentation
description: Erfahren Sie mehr über die physischen und umgebungsbedingten Spezifikationen für Microsoft Azure FXT Edge Filer-Hardware.
author: femila
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: femila
ms.openlocfilehash: ee6cf9f85843edbd7bece70039adaa61373b6737
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011142"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Spezifikationen von Azure FXT Edge Filer

In diesem Artikel werden die Hardwarespezifikationen für Azure FXT Edge Filer-Hardwareknoten beschrieben. In der Praxis werden drei oder mehr Knoten zusammen konfiguriert und ergeben das Clustercachesystem.

## <a name="hardware-specifications"></a>Hardwarespezifikationen

| Komponente | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| CPU-Kerne |  16 | 16 |
| DRAM  | 1\.536 GB | 768 GB |
| Netzwerkports | 6 × 25/10 GB + 2 × 1 GB | 6 × 25/10 GB + 2 × 1 GB |
| NVMe SSD-Kapazität | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Laufwerkspezifikationen

Das System verfügt über zehn von vorne zugängliche Laufwerkschächte. Jedes bestückte Laufwerk verfügt rechts über eine Kapazitätsanzeige.

Die Laufwerknummern sind im Zwischenraum zwischen den Laufwerken angegeben. Bei Azure FXT Edge Filer befindet sich das Laufwerk 0 ganz links oben und das Laufwerk 1 direkt darunter.

![Foto eines Laufwerkschachts im FXT-Gehäuse mit Laufwerknummern und Kapazitätsanzeigen](media/fxt-drives-photo.png)

| Laufwerknummern    |  Zweck   |  Spezifikationen |
|------------------|--------|-----------------|
| 0, 1             | OS     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Daten   | FXT 6600: 3,2 TB NVMe SSD <br> FXT 6400: 1,6 TB NVMe SSD |

## <a name="dimensions-and-weight"></a>Abmessungen und Gewicht

Azure FXT Edge Filer passt in ein 19-Zoll-Standard-Rack und hat eine Höhe von einer Rack-Einheit (1U).

<!-- 10x2.5 inches version -->

| Abmessungen Filer            | Wert                    |
|-----------------------------|--------------------------|
| Höhe                      | 42,8 mm (1,68 Zoll)    |
| Breite (einschließlich Rack-Ohren) | 482 mm (18,97 Zoll)  |
| Breite – Hauptgehäuse      | 434 mm (17,08 Zoll) |
| Tiefe – Rack-Ohren an der Rückseite des Hauptgehäuses                   | 733,82 mm (29,61 Zoll) |
| Tiefe – Rack-Ohren bis zum weitesten hinteren Überstand                 | 772,67 mm (30,42 Zoll) |
| Tiefe – Rack-Ohren bis zum weitesten vorderen Überstand ohne Blende | 22 mm (0,87 Zoll)  |
| Tiefe – Rack-Ohren bis zum weitesten vorderen Überstand mit Blende    | 35,84 mm (1.41 Zoll) |

| Weight | Wert |
|-----------------|----------------------|
| Knotengewicht (ohne Verpackung, ohne Zubehör) | 18,1 kg (40 lbs) |
| Nettogewicht (ohne Verpackung, einschließlich Zubehör) | 23,1 kg (51 lbs)|
| Bruttogewicht (Versandgewicht, inklusive aller Verpackungen) |  29 kg (64 lbs) |

### <a name="shipping-dimensions"></a>Versandabmessungen

| Packungsgröße | Millimeter | Zoll |
|-------------------|-------------|--------|
| Höhe            | 311,2       | 12,25 Zoll |
| Breite             | 642,8       | 25,31 Zoll |
| Länge            | 1\.051,1     | 41,38 Zoll |

## <a name="power-and-thermal-specifications"></a>Leistungs- und Wärmespezifikationen

Dieser Abschnitt enthält Leistungsdaten und -maße für Azure FXT Edge Filer.

### <a name="nameplate-ratings"></a>Typenschildangaben

| Typenschildangaben für Modelle der FXT 6000-Serie |
|----------------|
| 100–240 V~    |
| 10 A – 5 A (X2)  |
| 50/60 Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Leistungs- und Wärmemessungen

Azure FXT Edge Filer-Knoten verwenden Lüfter mit variabler Geschwindigkeit, sodass die Leistung von Temperatur und Last abhängt. Maximale Lüftergeschwindigkeiten können bei bestimmten Kombinationen aus hoher Last und erhöhten Umgebungstemperaturen erreicht werden.

In den folgenden Tabellen sind Leistungsaufnahme- und Wärmeleistungsmessungen für häufig verwendete Spannung-Frequenz-Kombinationen angegeben.

| Leistung FXT 6600 bei Zimmertemperatur <br />(22 °C, 71,6 °F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spannung (V) | 100 | 120 | 208 | 230 | 240 |
| Frequenz (Hz) | 60 | 60 | 60 | 50 | 50 |
| Stromstärke (A) | 5,02 | 4,16 |2,40 | 2,20 | 2.16 |
| Scheinleistung (VA) | 502 | 499 | 499 | 506 | 518|
| Leistungsfaktor | 0,99 | 0,99 |0,98 | 0,98 | 0,98 |
| Wirkleistung (W) | 497 |494 | 489 | 496 | 508 |
| Verlustleistung (BTU/h) |1696 | 1686 | 1669 | 1692 | 1733 |

| Leistung FXT 6600 bei maximalen Lüftergeschwindigkeiten | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spannung (V) | 100 |120 | 208 | 230 | 240|
| Frequenz (Hz) | 60 | 60 | 60 | 50 | 50 |
| Stromstärke (A) | 5,98 | 5,01 | 2.81 | 2.55 | 2,48 |
| Scheinleistung (VA) | 598 | 601 | 584 | 587 | 595 |
| Leistungsfaktor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Wirkleistung (W) | 592 | 595 | 573 | 575 | 583 |
| Verlustleistung (BTU/h) | 2020 |2031 | 1954 | 1961 | 1990 |

| Leistung FXT 6400 bei Zimmertemperatur <br />(22 °C, 71,6 °F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spannung (V) | 100 | 120 | 208 | 230 | 240 |
| Frequenz (Hz) |60 | 60 | 60 | 50 | 50 |
| Stromstärke (A) | 4.63 | 3,86 | 2,24 | 2,04 | 1,94 |
| Scheinleistung (VA) | 463 | 463 | 466 | 469 | 466 |
| Leistungsfaktor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Wirkleistung (W) | 458 | 459 | 457 | 460 | 456 |
| Verlustleistung (BTU/h) | 1564 | 1565 | 1558 | 1569 | 1557 |

| Leistung FXT 6400 bei maximalen Lüftergeschwindigkeiten | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spannung (V) | 100 | 120 | 208 | 230 | 240 |
| Frequenz (Hz) | 60 | 60 | 60 | 50 | 50 |
| Stromstärke (A) | 5.15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Scheinleistung (VA) | 515 | 514 | 516 | 524 | 511 |
| Leistungsfaktor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Wirkleistung (W) | 510 | 508 | 506 | 514 | 501 |
| Verlustleistung (BTU/h) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Umgebungsanforderungen

Dieser Abschnitt enthält Spezifikationen zu den Umgebungsbedingungen für die Hardware.

### <a name="temperature-and-humidity"></a>Temperatur und Feuchtigkeit

| Umgebungsmerkmal   | Betriebsbereich                   | Außerhalb des Betriebsbereichs         |
|---------------------------|-----------------------------------|-----------------------------|
| Umgebungstemperaturspanne | 10 °C bis 35 °C (50 °F bis 86 °F)          | -40 °C bis 65 °C (-40 °F bis 149 °F) |
| Relative Luftfeuchtigkeit der Umgebung | 10–80 % nicht kondensierend          | 5–95 % nicht kondensierend     |
| Maximaler Kondensationspunkt         | 29 °C                       | 33°C (91°F)                 |
| Höhe                  | bis zu 3.048 m (10.000 Fuß), unterliegt der unten angegebenen Temperaturminderung | bis zu 12.000 m (39.370 Fuß) |

> [!NOTE]
> **Temperaturminderung bei Höhe:** Die maximale Temperatur reduziert sich auf über 950 m (3.117 Fuß) alle 300 m um 1 °C (1 °F alle 547 Fuß).

### <a name="airflow-shock-and-vibration"></a>Luftströmung, Stoßfestigkeit und Schwingungen

| attribute         | Spezifikation |
|-------------------|---------------|
| Luftströmung                    | Die Luftströmung im System verläuft von vorne nach hinten. Das System muss mit einem hinteren Luftstromauslass mit niedrigem Druck betrieben werden. |
| Stoßfestigkeit, bei Betrieb         | 6 G für eine Dauer von 11 Millisekunden (getestet in 6 verschiedenen Ausrichtungen) |
| Stoßfestigkeit, außerhalb des Betriebs     | 71 G für eine Dauer von 2 Millisekunden (getestet in 6 verschiedenen Ausrichtungen) |
| Schwingungen, bei Betrieb     | 0,26 G<sub>QMW</sub> bei 5 Hertz bis 350 Hertz zufälliger Vibration         |
| Schwingungen, außerhalb des Betriebs | 1,88 G<sub>QMW</sub> bei 10 Hertz bis 500 Hertz für die Dauer von 15 Minuten (von allen sechs Seiten getestet)  |

## <a name="safety-regulation-compliance"></a>Einhaltung der Sicherheitsvorschriften

Azure FXT Edge Filer entspricht den aufgeführten Vorschriften.

| Category       | Regulatorische Spezifikationen |
|----------------|--------------------------|
| Allgemeine Sicherheit | EN 60950-1:2006 + A1:2010 + A2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + A1:2009 + A2:2013 <br>EN 62311:2008 |
| EMC            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Klasse D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energieversorgung         | Kommissionsregelung (EU) Nr. 617/2013.  |
| RoHS-Richtlinien           |    EN 50581:2012   |
