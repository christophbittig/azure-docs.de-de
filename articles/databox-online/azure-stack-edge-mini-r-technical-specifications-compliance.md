---
title: 'Microsoft Azure Stack Edge Mini R: Technische Spezifikationen und Compliance | Microsoft-Dokumentation'
description: Hier erhalten Sie Informationen zu technischen Spezifikationen und Compliance für Ihr Azure Stack Edge Mini R-Gerät.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: alkohli
ms.openlocfilehash: 8b2da6b75ec495fe96b0c65e312a91d0dd30a2d2
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091499"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini R: Technische Spezifikationen

Die Hardwarekomponenten Ihres Microsoft Azure Stack Edge Mini R-Geräts entsprechen den in diesem Artikel beschriebenen technischen Spezifikationen sowie den aufgeführten gesetzlichen Richtlinien. In den technischen Spezifikationen werden CPU, Arbeitsspeicher, Netzteile, Speicherkapazität, Gehäuseabmessungen und Gewicht beschrieben.


## <a name="compute-memory"></a>Compute, Arbeitsspeicher

Das Azure Stack Edge Mini R-Gerät verfügt über die folgenden Spezifikationen für Compute- und Arbeitsspeicherressourcen:

| Spezifikation           | Wert                           |
|-------------------------|---------------------------------|
| CPU-Typ                | Intel Xeon-D 1577               |
| CPU: unformatiert                | 16 Kerne insgesamt, 32 vCPUs insgesamt  |
| CPU: nutzbar             | 24 vCPUs                        |
| Arbeitsspeichertyp             | SODIMM, 16 GB, 2400 MT/s          |
| Arbeitsspeicher: unformatiert             | 48 GB RAM (3 x 16 GB)           |
| Arbeitsspeicher: nutzbar          | 32 GB RAM                       |


## <a name="compute-acceleration"></a>Computebeschleunigung

Eine integrierte Bildverarbeitungseinheit (Vision Processing Unit, VPU) ist in jedem Azure Stack Edge Mini R-Gerät enthalten, das auf Kubernetes, Deep Neural Network und maschinellem Sehen basierende Anwendungen ermöglicht.

| Spezifikation             | Wert                  |
|---------------------------|------------------------|
| Karte für die Computebeschleunigung | Intel Movidius Myriad X VPU <br> Weitere Informationen finden Sie unter [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX). |


## <a name="storage"></a>Storage

Das Azure Stack Edge Mini R-Gerät verfügt über einen Datenträger für Daten und einen Startdatenträger (der als Speicher für das Betriebssystem dient). In der folgenden Tabelle sind die Details zur Speicherkapazität des Geräts aufgeführt.

|     Spezifikation                          |     Wert                                              |
|--------------------------------------------|--------------------------------------------------------|
|    Anzahl von Solid-State-Laufwerken (SSDs)     |    2 x 1 TB Datenträger <br> Ein Datenträger für Daten und ein Startdatenträger |
|    Kapazität eines SSD-Laufwerks                     |    1 TB                                                |
|    Gesamtkapazität (nur Daten)              |    1 TB                                                |
|    Insgesamt nutzbare Kapazität*                  |    ~ 750 GB                                            |

*Etwas Speicherplatz ist für die interne Verwendung reserviert.*

## <a name="network"></a>Netzwerk

Das Azure Stack Edge Mini R-Gerät verfügt über die folgenden Spezifikationen für das Netzwerk:

|Spezifikation         |Wert                                                               |
|----------------------|--------------------------------------------------------------------|
|Netzwerkschnittstellen    |2 x 10 GBit/s SFP+ <br> Wird als ANSCHLUSS 3 und ANSCHLUSS 4 auf der lokalen Benutzeroberfläche angezeigt.    |
|Netzwerkschnittstellen    |2 x 1 GBit/s RJ45 <br> Wird als ANSCHLUSS 1 und ANSCHLUSS 2 auf der lokalen Benutzeroberfläche angezeigt.     |
|WLAN                 |802.11ac                                                            |

## <a name="routers-and-switches"></a>Router und Switches

Die folgenden Router und Switches sind mit den SPF+-Netzwerkschnittstellen mit 10 GBit/s (Port 3 und Port 4) auf Ihren Azure Stack Edge Mini R-Geräten kompatibel:

|Router/Switch     |Notizen                         |
|------------------|------------------------------|
|[VoyagerESR 2.0](https://klastelecom.com/products/voyageresr2-0/)    |Cisco ESS3300 Switch-Komponente   |
|[VoyagerSW26G](https://klastelecom.com/products/voyagersw26g/)       |                                 |
|[VoyagerVM 3.0](https://klastelecom.com/products/voyager-vm-3-0/)    |                                 |
|[TDC Switch](https://klastelecom.com/voyager-tdc/)                   |                                 |
|[TRX R2](https://klastelecom.com/products/trx-r2/) (acht Kerne)  <!--Better link: https://www.klasgroup.com/products/voyagersw12gg/? On current link target, an "R6" link opens this page.-->        |                              |
|[SW12GG](https://www.klasgroup.com/products/voyagersw12gg/)          |                                 |

## <a name="transceivers-cables"></a>Transceiver, Kabel

Die folgenden kupferbasierten SFP+-Transceiver und -Kabel (10 GBit/s) werden dringend für die Verwendung mit Azure Stack Edge Mini R-Geräten empfohlen. Kompatible Glasfaserkabel können mit SFP+-Netzwerkschnittstellen (Port 3 und Port 4) verwendet werden, wurden jedoch nicht getestet.

|SFP+-Transceivertyp |Unterstützte Kabel    | Notizen |
|----------------------|--------------------|-------|
|SFP+ Direktanschluss-Kupferkabel (10GSFP+Cu)| <ul><li>[FS SFP-10G-DAC](https://www.fs.com/c/fs-10g-sfp-dac-1115) (Lieferbar für Industrietemperaturen von -40º C bis +85º C als Sonderbestellung)</li><br><li>[10Gtek CAB-10GSFP-P0.5M](http://www.10gtek.com/10G-SFP+-182)</li><br><li>[Cisco SFP-H10GB-CU1M](https://www.cisco.com/c/en/us/products/collateral/interfaces-modules/transceiver-modules/data_sheet_c78-455693.html)</li></ul> |<ul><li>Wird auch als SFP+-Twinax-DAC-Kabel bezeichnet.</li><br><li>Empfohlene Option, da sie den geringsten Stromverbrauch hat und am einfachsten ist.</li><br><li>Die automatische Aushandlung wird nicht unterstützt.</li><br><li>Das Verbinden eines SFP-Geräts mit einem SFP+-Gerät wird nicht unterstützt.</li></ul>|

## <a name="power-supply-unit"></a>Stromversorgungseinheit

Das Azure Stack Edge Mini R-Gerät enthält einen externen 85 W Wechselstromadapter für die Stromversorgung und zum Laden des eingebauten Akkus.

Die folgende Tabelle zeigt die Netzteilspezifikationen:

| Spezifikation           | Wert                      |
|-------------------------|----------------------------|
| Maximale Ausgangsleistung    | 85 W                       |
| Häufigkeit               | 50/60 Hz                   |
| Spannungsbereichsauswahl | Automatischer Spannungsbereich: 100-240 V AC |

## <a name="included-battery"></a>Eingebauter Akku

Das Azure Stack Edge Mini R-Gerät enthält auch einen eingebauten Akku, der durch das Netzteil aufgeladen wird.

Ein zusätzlicher [Akku des Typs 2590](https://www.bren-tronics.com/bt-70791ck.html) kann in Verbindung mit dem eingebauten Akku verwendet werden, um den Einsatz des Geräts zwischen den Ladevorgängen zu verlängern. Dieser Akku sollte allen Sicherheits-, Transport- und Umweltvorschriften des Landes entsprechen, in dem er verwendet wird.

| Spezifikation            | Wert      |
|--------------------------|------------|
| Kapazität des eingebauten Akkus | 73 Wh      |

## <a name="enclosure-dimensions-and-weight"></a>Abmessungen und Gewicht des Gehäuses

In den folgenden Tabellen sind die verschiedenen Gehäusespezifikationen (Abmessungen und Gewicht) aufgeführt.

### <a name="enclosure-dimensions"></a>Gehäuseabmessungen

In der folgenden Tabelle sind die Abmessungen des Geräts und der USV im robusten Gehäuse in Millimetern und Zoll aufgeführt.

|     Gehäuse     |     Millimeter     |     Zoll     |
|-------------------|---------------------|----------------|
|    Höhe         |    68               |    2.68        |
|    Breite          |    208              |    8,19        |
|    Länge         |    259              |    10,20       |


### <a name="enclosure-weight"></a>Gehäusegewicht

In der folgenden Tabelle ist das Gewicht des Geräts einschließlich Akku aufgeführt.

|     Gehäuse                     |     Weight          |
|-----------------------------------|---------------------|
|    Gesamtgewicht des Geräts     |     3,2 kg (7 lbs)           |

## <a name="enclosure-environment"></a>Gehäuseumgebung

Dieser Abschnitt enthält die Spezifikationen im Zusammenhang mit der Gehäuseumgebung, z. B. Temperatur, Luftfeuchtigkeit und Höhenlage.

|     Spezifikationen             |     BESCHREIBUNG                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Temperaturbereich          |     0–40° C (Betrieb)                                              |
|     Vibration                  |     MIL-STD-810-Methode 514.7*<br> Prozedur I CAT 4, 20                  |
|     Erschütterung                      |     MIL-STD-810-Methode 516.7*<br> Prozedur IV, Logistic                 |
|     Höhe                   |     Betrieb:   3.048 m (15.000 Fuß)<br> Außerhalb des Betriebs: 12.192 m (40.000 Fuß)          |

**Alle Verweise beziehen sich auf MIL-STD-810G-Änderung 1 (2014)*

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen Ihrer Azure Stack Edge Mini R-Instanz](azure-stack-edge-placeholder.md)
