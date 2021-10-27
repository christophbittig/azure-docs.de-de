---
title: Datei einfügen
description: Datei einfügen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0e0d3c131f5cd53684580d0035c8836a2537d780
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070818"
---
Die einzigen Optionen für die Infrastrukturredundanz, die derzeit für Disk Ultra-Datenträger zur Verfügung stehen, sind Verfügbarkeitszonen. Virtuelle Computer, die andere Redundanzoptionen verwenden, können keinen Disk Ultra-Datenträger anfügen.

In der folgenden Tabelle sind die Regionen, in denen Disk Ultra-Datenträger verfügbar sind, sowie die entsprechenden Verfügbarkeitsoptionen aufgeführt.

> [!NOTE]
> Wenn es in einer Region in der folgenden Liste keine Verfügbarkeitszonen für Ultradatenträger gibt, muss eine VM in dieser Region ohne Infrastrukturredundanz bereitgestellt werden, um einen Ultradatenträger anzufügen.

| Redundanzoptionen | Regions |
|--------------------|---------|
| **Einzelne VMs** | Australien, Mitte<br/>Brasilien Süd<br/>Indien, Mitte<br/>Asien, Osten<br/>Deutschland, Westen-Mitte<br/>Korea, Mitte<br/>„USA, Norden-Mitte“, „USA, Süden-Mitte“, „USA, Westen“<br/>US Gov Arizona, US Gov Texas, US Gov Virginia<br/> |
| **Zwei Verfügbarkeitszonen** | Australien (Osten)<br/>Kanada, Mitte<br/>„Europa, Norden“, „Europa, Westen“<br/>Japan, Osten<br/>Asien, Südosten<br/>UK, Süden<br/>„USA, Mitte“, „USA, Osten“, „USA, Osten 2“, „USA, Westen 2“|
| **Drei Verfügbarkeitszonen** | Frankreich, Mitte |

Nicht jede VM-Größe ist in allen unterstützten Regionen mit Ultra-Datenträgern verfügbar. In der folgenden Tabelle sind VM-Serien aufgeführt, die mit Ultra-Datenträgern kompatibel sind.

|VM-Typ     |Größen    |BESCHREIBUNG  |
|------------|---------|-------------|
| Allgemein|[DSv3-Serie](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series), [Ddsv4-Serie](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series), [Dsv4-Serie](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series), [Dasv4-Serie](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)| Ausgewogenes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Tests und Entwicklung, kleine bis mittlere Datenbanken sowie Webserver mit geringer bis mittlerer Auslastung.|
| Computeoptimiert|[FSv2-Serie](../articles/virtual-machines/fsv2-series.md)| Hohes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Webserver, Network Appliances, Stapelverarbeitungsvorgänge und Anwendungsserver mit mittlerer Auslastung.|
| Arbeitsspeicheroptimiert|[ESv3-Serie](../articles/virtual-machines/ev3-esv3-series.md#esv3-series), [Easv4-Serie](../articles/virtual-machines/eav4-easv4-series.md#easv4-series), [Edsv4-Serie](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series), [Esv4-Serie](../articles/virtual-machines/ev4-esv4-series.md#esv4-series), [M-Serie](../articles/virtual-machines/m-series.md), [Mv2-Serie](../articles/virtual-machines/mv2-series.md)|Hohes Verhältnis von Speicher zu CPU. Hervorragend geeignet für relationale Datenbankserver, mittlere bis große Caches und In-Memory-Analysen.
| Speicheroptimiert|[LSv2-Serie](../articles/virtual-machines/lsv2-series.md)|Hoher Datenträgerdurchsatz und E/A, ideal für Big Data, SQL, NoSQL-Datenbanken, Datawarehousing und große transaktionale Datenbanken.|
| GPU-optimiert|[NCv2-Serie,](../articles/virtual-machines/ncv2-series.md)[NCv3-Serie,](../articles/virtual-machines/ncv3-series.md)[NCasT4_v3-Serie,](../articles/virtual-machines/nct4-v3-series.md)[ND-Serie,](../articles/virtual-machines/nd-series.md)[NDv2-Serie,](../articles/virtual-machines/ndv2-series.md)[NVv3-Serie,](../articles/virtual-machines/nvv3-series.md)[NVv4-Serie](../articles/virtual-machines/nvv4-series.md)| Spezielle virtuelle Computer als Ziel für aufwendiges Grafikrendering und Videobearbeitung sowie für Modelltraining und Rückschlüsse (ND) mit Deep Learning. Mit einem oder mehreren GPUs verfügbar.|
| <nobr>Leistungsoptimiert</nobr> |[HB-Serie,](../articles/virtual-machines/hb-series.md)[HC-Serie,](../articles/virtual-machines/hc-series.md)[HBv2-Serie](../articles/virtual-machines/hbv2-series.md)|Der virtuelle Computer mit den schnellsten und leistungsfähigsten CPUs, die optional über Netzwerkschnittstellen mit hohem Durchsatz (RDMA) verfügen.|

Ultra-Datenträger können nicht mit einigen Features und Funktionen verwendet werden, einschließlich Datenträgermomentaufnahmen, Datenträgerexport, Ändern des Datenträgertyps, VM-Images, Verfügbarkeitsgruppen, Azure Dedicated Hosts oder Azure Disk Encryption. Azure Backup und Azure Site Recovery unterstützen keine Ultra-Datenträger. Darüber hinaus werden nur nicht zwischengespeicherte Lesevorgänge und nicht zwischengespeicherte Schreibvorgänge unterstützt.

Ultra-Datenträger unterstützen standardmäßig eine physische Sektorgröße von 4.000. Eine Sektorgröße 512E steht als allgemein verfügbares Angebot zur Verfügung. Es ist keine Registrierung erforderlich. Die meisten Anwendungen sind mit der Sektorgröße 4K kompatibel, einige benötigen jedoch eine Sektorgröße von 512 Byte. Für Oracle Database ist beispielsweise die Version 12.2 oder höher erforderlich, um native Datenträger mit 4K zu unterstützen. Ältere Oracle DB-Versionen benötigen eine Sektorgröße von 512 Byte.
