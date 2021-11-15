---
title: Konfigurationstabellen für Dedicated Host-SKUs
description: Hier finden Sie die Spezifikationen für die VM-Zusammenstellungen von ADH-SKUs.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 67b17324b550196728da62cb0e5b306d6387fa25
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478861"
---
# <a name="azure-dedicated-host-sku-configuration-tables"></a>Konfigurationstabellen für Azure Dedicated Host-SKUs
Azure Dedicated Host-SKUs sind die Kombination aus einer VM-Familie und einer bestimmten Hardwarespezifikation. Sie können nur VMs der VM-Serie bereitstellen, die von der Dedicated Host-SKU angegeben werden. Für Dsv3-Type3 können beispielsweise nur VMs der [Dsv3-Serie](dv3-dsv3-series.md#dsv3-series) bereitgestellt werden. 

In diesem Dokument werden die Hardwarespezifikationen und VM-Zusammenstellungen für alle Dedicated Host-SKUs erläutert.

## <a name="dasv4"></a>Dasv4
### <a name="dasv4-type1"></a>Dasv4-Type1
Dasv4-Type1 ist eine Dedicated Host-SKU, die den AMD EPYC™ 7452-Prozessor mit 2,35 GHz nutzt. Sie hat 64 physische Kerne, 96 vCPUs und 672 GiB RAM. Auf Dasv4-Type1 werden VMs der [Dasv4-Serie](dav4-dasv4-series.md#dasv4-series) ausgeführt. In der Dokumentation zur jeweiligen VM-Größe wird die Leistung näher erläutert.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Dasv4-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers  | VM-Anzahl |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672 GiB       | D2as v4  | 32    |
|                |                 |               | D4as v4  | 24    |
|                |                 |               | D8as v4  | 12    |
|                |                 |               | D16as v4 | 6     |
|                |                 |               | D32as v4 | 3     |
|                |                 |               | D48as v4 | 2     |
|                |                 |               | D64as v4 | 1     |
|                |                 |               | D96as v4 | 1     |

Auf Dasv4-Type1 können auch mehrere VM-Größen kombiniert werden. Im Folgenden sehen Sie Beispielkombinationen für VM-Zusammenstellungen auf Dasv4-Type1:
- 1 D48asv4 + 3 D16asv4
- 1 D32asv4 + 2 D16asv4 + 8 D4asv4
- 20 D4asv4 + 8 D2asv4

## <a name="ddsv4"></a>Ddsv4
### <a name="ddsv4-type1"></a>Ddsv4-Type1
Ddsv4-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8272CL) nutzt. Sie hat 52 physische Kerne, 68 vCPUs und 504 GiB RAM. Auf Ddsv4-Type1 werden VMs der [Ddsv4-Serie](ddv4-ddsv4-series.md#ddsv4-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Ddsv4-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers  | VM-Anzahl |
|----------------|-----------------|---------------|----------|-------|
| 52             | 68              | 504 GiB       | D2ds v4  | 32    |
|                |                 |               | D4ds v4  | 17    |
|                |                 |               | D8ds v4  | 8     |
|                |                 |               | D16ds v4 | 4     |
|                |                 |               | D32ds v4 | 1     |
|                |                 |               | D48ds v4 | 1     |
|                |                 |               | D64ds v4 | 1     |

Auf Ddsv4-Type1 können auch mehrere VM-Größen kombiniert werden. Im Folgenden sehen Sie Beispielkombinationen für VM-Zusammenstellungen auf Ddsv4-Type1:
- 1 D48dsv4 + 4 D4dsv4 + 2 D2dsv4
- 1 D32dsv4 + 2 D16dsv4 + 1 D4dsv4
- 10 D4dsv4 + 14 D2dsv4

## <a name="dsv4"></a>Dsv4
### <a name="dsv4-type1"></a>Dsv4-Type1

Dsv4-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8272CL) nutzt. Sie hat 52 physische Kerne, 80 vCPUs und 504 GiB RAM. Auf Dsv4-Type1 werden VMs der [Dsv4-Serie](dv4-dsv4-series.md#dsv4-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Dsv4-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | D2s v4  | 32    |
|                |                 |               | D4s v4  | 20    |
|                |                 |               | D8s v4  | 10    |
|                |                 |               | D16s v4 | 5     |
|                |                 |               | D32s v4 | 2     |
|                |                 |               | D48s v4 | 1     |
|                |                 |               | D64s v4 | 1     |

Auf Dsv4-Type1 können auch mehrere VM-Größen kombiniert werden. Im Folgenden sehen Sie Beispielkombinationen für VM-Zusammenstellungen auf Dsv4-Type1:
- 1 D64sv4 + 1 D16sv4
- 1 D32sv4 + 2 D16dsv4 + 2 D8sv4
- 10 D4sv4 + 20 D2sv4

## <a name="dsv3"></a>Dsv3
### <a name="dsv3-type1"></a>Dsv3-Type1

Dsv3-Type1 ist eine Dedicated Host-SKU, die den Intel® Broadwell-Prozessor (Xeon® E5-2673 v4) mit 2,3 GHz nutzt. Sie hat 40 physische Kerne, 64 vCPUs und 256 GiB RAM. Auf Dsv3-Type1 werden VMs der [Dsv3-Serie](dv3-dsv3-series.md#dsv3-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Dsv3-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 256 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 16    |
|                |                 |               | D8s v3  | 8     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Auf Dsv3-Type1 können auch mehrere VM-Größen kombiniert werden. Im Folgenden sehen Sie Beispielkombinationen für VM-Zusammenstellungen auf Dsv3-Type1:
- 1 D32sv3 + 1 D16sv3 + 1 D8sv3
- 1 D48sv3 + 3 D4sv3 + 2 D2sv3
- 10 D4sv3 + 12 D2sv3

### <a name="dsv3-type2"></a>Dsv3-Type2

Dsv3-Type2 ist eine Dedicated Host-SKU, die den Intel® Skylake-Prozessor (Xeon® Platinum 8171M) mit 2,1 GHz nutzt. Sie hat 48 physische Kerne, 76 vCPUs und 504 GiB RAM. Auf Dsv3-Type2 werden VMs der [Dsv3-Serie](dv3-dsv3-series.md#dsv3-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Dsv3-Type2-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 18    |
|                |                 |               | D8s v3  | 9     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Auf Dsv3-Type2 können auch mehrere VM-Größen kombiniert werden. Im Folgenden sehen Sie Beispielkombinationen für VM-Zusammenstellungen auf Dsv3-Type2:
- 1 D64sv3 + 2 D4vs3 + 2 D2sv3
- 1 D48sv3 + 4 D4sv3 + 6 D2sv3
- 12 D4sv3 + 14 D2sv3

### <a name="dsv3-type3"></a>Dsv3-Type3

Dsv3-Type3 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8272CL) nutzt. Sie hat 52 physische Kerne, 80 vCPUs und 504 GiB RAM. Auf Dsv3-Type3 werden VMs der [Dsv3-Serie](dv3-dsv3-series.md#dsv3-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Dsv3-Type3-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 20    |
|                |                 |               | D8s v3  | 10    |
|                |                 |               | D16s v3 | 5     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Auf Dsv3-Type3 können auch mehrere VM-Größen kombiniert werden. Im Folgenden sehen Sie Beispielkombinationen für VM-Zusammenstellungen auf Dsv3-Type3:
- 1 D64sv3 + 1 D8sv3 + 2 D4sv3
- 1 D48sv3 + 1 D16sv3 + 4 D4sv3
- 15 D4sv3 +  10 D2sv3

## <a name="dcsv2"></a>DCsv2
### <a name="dcsv2-type1"></a>DCsv2-Type1

DCsv2-Type1 ist eine Dedicated Host-SKU, die den Intel® Coffee Lake-Prozessor (Xeon® E-2288G mit SGX-Technologie) nutzt. Sie hat 8 physische Kerne, 8 vCPUs und 64 GiB RAM. Auf DCsv2-Type1 werden VMs der [DCsv2-Serie](dcv2-series.md) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem DCsv2-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 8              | 8               | 64GiB        | DC8s v2 | 1     |

## <a name="easv4"></a>Easv4
### <a name="easv4-type1"></a>Easv4-Type1

Easv4-Type1 ist eine Dedicated Host-SKU, die den AMD EPYC™ 7452-Prozessor mit 2,35 GHz nutzt. Sie hat 64 physische Kerne, 96 vCPUs und 672 GiB RAM. Auf Easv4-Type1 werden VMs der [Easv4-Serie](eav4-easv4-series.md#easv4-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Easv4-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers  | VM-Anzahl |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672 GiB       | E2as v4  | 32    |
|                |                 |               | E4as v4  | 21    |
|                |                 |               | E8as v4  | 10    |
|                |                 |               | E16as v4 | 5     |
|                |                 |               | E20as v4 | 4     |
|                |                 |               | E32as v4 | 2     |
|                |                 |               | E48as v4 | 1     |
|                |                 |               | E64as v4 | 1     |
|                |                 |               | E96as v4 | 1     |

## <a name="edsv4"></a>Edsv4
### <a name="edsv4-type1"></a>Edsv4-Type1

Edsv4-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8272CL) nutzt. Sie hat 52 physische Kerne, 64 vCPUs und 504 GiB RAM. Auf Edsv4-Type1 werden VMs der [Edsv4-Serie](edv4-edsv4-series.md#edsv4-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Edsv4-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers  | VM-Anzahl |
|----------------|-----------------|---------------|----------|-------|
| 52             | 64              | 504 GiB       | E2ds v4  | 31    |
|                |                 |               | E4ds v4  | 15    |
|                |                 |               | E8ds v4  | 7     |
|                |                 |               | E16ds v4 | 3     |
|                |                 |               | E20ds v4 | 3     |
|                |                 |               | E32ds v4 | 1     |
|                |                 |               | E48ds v4 | 1     |
|                |                 |               | E64ds v4 | 1     |

## <a name="esv4"></a>Esv4
### <a name="esv4-type1"></a>Esv4-Type1

Esv4-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8272CL) nutzt. Sie hat 52 physische Kerne, 80 vCPUs und 504 GiB RAM. Auf Esv4-Type1 werden VMs der [Esv4-Serie](ev4-esv4-series.md#esv4-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Esv4-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | E2s v4  | 31    |
|                |                 |               | E4s v4  | 15    |
|                |                 |               | E8s v4  | 7     |
|                |                 |               | E16s v4 | 3     |
|                |                 |               | E20s v4 | 3     |
|                |                 |               | E32s v4 | 1     |
|                |                 |               | E48s v4 | 1     |
|                |                 |               | E64s v4 | 1     |

## <a name="esv3"></a>Esv3
### <a name="esv3-type1"></a>Esv3-Type1

Esv3-Type1 ist eine Dedicated Host-SKU, die den Intel® Broadwell-Prozessor (Xeon® E5-2673 v4) mit 2,3 GHz nutzt. Sie hat 40 physische Kerne, 64 vCPUs und 448 GiB RAM. Auf Esv3-Type1 werden VMs der [Esv3-Serie](ev3-esv3-series.md#ev3-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Esv3-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 448 GiB       | E2s v3  | 28    |
|                |                 |               | E4s v3  | 14    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 2     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type2"></a>Esv3-Type2

Esv3-Type2 ist eine Dedicated Host-SKU, die den Intel® Skylake-Prozessor (Xeon® 8171M) nutzt. Sie hat 48 physische Kerne, 78 vCPUs und 504 GiB RAM. Auf Esv3-Type2 werden VMs der [Esv3-Serie](ev3-esv3-series.md#ev3-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Esv3-Type2-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504 GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type3"></a>Esv3-Type3

Esv3-Type3 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8272CL) nutzt. Sie hat 52 physische Kerne, 80 vCPUs und 504 GiB RAM. Auf Esv3-Type3 werden VMs der [Esv3-Serie](ev3-esv3-series.md#ev3-series) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Esv3-Type3-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

## <a name="fsv2"></a>Fsv2
### <a name="fsv2-type2"></a>Fsv2-Type2

Fsv2-Type2 ist eine Dedicated Host-SKU, die den Intel® Skylake-Prozessor (Xeon® Platinum 8168) nutzt. Sie hat 48 physische Kerne, 72 vCPUs und 144 GiB RAM. Auf Fsv2-Type2 werden VMs der [Fsv2-Serie](fsv2-series.md) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Fsv2-Type2-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 48             | 72              | 144 GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 18    |
|                |                 |               | F8s v2  | 9     |
|                |                 |               | F16s v2 | 4     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

### <a name="fsv2-type3"></a>Fsv2-Type3

Fsv2-Type3 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8272CL) nutzt. Sie hat 52 physische Kerne, 86 vCPUs und 504 GiB RAM. Auf Fsv2-Type3 werden VMs der [Fsv2-Serie](fsv2-series.md) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Fsv2-Type3-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 52             | 86              | 504 GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 21    |
|                |                 |               | F8s v2  | 10    |
|                |                 |               | F16s v2 | 5     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

## <a name="fxmds"></a>FXmds
### <a name="fxmds-type1"></a>FXmds-Type1

FXmds-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Gold 6246R) nutzt. Sie hat 32 physische Kerne, 48 vCPUs und 1.152 GiB RAM. Auf FXmds-Type1 werden VMs der [FX-Serie](fx-series.md) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem FXmds-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 32             | 48              | 1\.152 GiB     | FX4mds  | 12    |
|                |                 |               | FX12mds | 4     |
|                |                 |               | FX24mds | 2     |
|                |                 |               | FX36mds | 1     |
|                |                 |               | FX48mds | 1     |

## <a name="lsv2"></a>Lsv2
### <a name="lsv2-type1"></a>Lsv2-Type1

Lsv2-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Gold 6246R) nutzt. Sie hat 64 physische Kerne, 80 vCPUs und 640 GiB RAM. Auf Lsv2-Type1 werden VMs der [Lsv2-Serie](lsv2-series.md) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Lsv2-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 64             | 80              | 640 GiB       | L8s v2  | 10    |
|                |                 |               | L16s v2 | 5     |
|                |                 |               | L32s v2 | 2     |
|                |                 |               | L48s v2 | 1     |
|                |                 |               | L64s v2 | 1     |
|                |                 |               | L80s v2 | 1     |

## <a name="m"></a>M
### <a name="ms-type1"></a>Ms-Type1

Ms-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8280) nutzt. Sie hat 112 physische Kerne, 128 vCPUs und 2.048 GiB RAM. Auf Ms-Type1 werden VMs der [M-Serie](m-series.md) ausgeführt, darunter M-, Mls-, Ms- und Mts-VMs.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Ms-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers | VM-Anzahl |
|----------------|-----------------|---------------|---------|-------|
| 112            | 128             | 2.048 GiB     | M32ls   | 4     |
|                |                 |               | M32ts   | 4     |
|                |                 |               | M64     | 2     | 
|                |                 |               | M64s    | 2     |
|                |                 |               | M64ls   | 2     |
|                |                 |               | M128    | 1     | 
|                |                 |               | M128s   | 1     |

### <a name="msm-type1"></a>Msm-Type1

Msm-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8280) nutzt. Sie hat 112 physische Kerne, 128 vCPUs und 3.892 GiB RAM. Auf Msm-Type1 werden VMs der [M-Serie](m-series.md) ausgeführt, darunter Ms-, Mms-, Mts- und Mls-VMs.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Msm-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers   | VM-Anzahl |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 128             | 3\.892 GiB     | M8ms      | 16    |
|                |                 |               | M8-2ms    | 16    |
|                |                 |               | M8-4ms    | 16    |
|                |                 |               | M16ms     | 8     |
|                |                 |               | M16-4ms   | 8     |
|                |                 |               | M16-8ms   | 8     |
|                |                 |               | M32ts     | 4     |
|                |                 |               | M32ls     | 4     |
|                |                 |               | M32ms     | 4     |
|                |                 |               | M32-8ms   | 4     |
|                |                 |               | M32-16ms  | 4     |
|                |                 |               | M64ms     | 2     |
|                |                 |               | M64       | 2     | 
|                |                 |               | M64s      | 2     |
|                |                 |               | M64m      | 2     |
|                |                 |               | M64ls     | 2     |
|                |                 |               | M64-16ms  | 2     |
|                |                 |               | M64-32ms  | 2     |
|                |                 |               | M128ms    | 1     |
|                |                 |               | M128s     | 1     |
|                |                 |               | M128m     | 1     |
|                |                 |               | M128      | 1     | 
|                |                 |               | M128-32ms | 1     |
|                |                 |               | M128-64ms | 1     |

## <a name="mv2"></a>Mv2
### <a name="msmv2-type1"></a>Msmv2-Type1

Msm-Type1 ist eine Dedicated Host-SKU, die den Intel® Skylake-Prozessor (Xeon® Platinum 8180M) nutzt. Sie hat 224 physische Kerne, 416 vCPUs und 11.400 GiB RAM. Auf Msmv2-Type1 werden VMs der [Mv2-Serie](mv2-series.md) ausgeführt, darunter Msv2- und Mmsv2-VMs.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Msm-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers       | VM-Anzahl |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 11.400 GiB    | M208ms v2     | 2     |
|                |                 |               | M208s v2     | 2     |
|                |                 |               | M416-208ms v2 | 1     | 
|                |                 |               | M416-208s v2  | 1     | 
|                |                 |               | M416ms v2     | 1     |  
|                |                 |               | M416s v2      | 1     |  

### <a name="msv2-type1"></a>Msv2-Type1

Msv2-Type1 ist eine Dedicated Host-SKU, die den Intel® Skylake-Prozessor (Xeon® Platinum 8180M) nutzt. Sie hat 224 physische Kerne, 416 vCPUs und 5.700 GiB RAM. Auf Msv2-Type1 werden VMs der [Mv2-Serie](mv2-series.md) ausgeführt, darunter Msv2- und Mmsv2-VMs.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem Msv2-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers       | VM-Anzahl |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 5\.700 GiB     | M208ms v2     | 2     |
|                |                 |               | M208s v2      | 1     |
|                |                 |               | M416-208s v2  | 1     |
|                |                 |               | M416s v2      | 1     |

## <a name="msv2"></a>Msv2
### <a name="mmsv2medmem-type1"></a>Mmsv2MedMem-Type1
Mmsv2MedMem-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8280) nutzt. Sie hat 112 physische Kerne, 192 vCPUs und 4.096 GiB RAM. Auf Mmsv2MedMem-Type1 werden VMs der [Msv2-Serie](msv2-mdsv2-series.md) ausgeführt, darunter Msv2- und Mmsv2-VMs.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers   | VM-Anzahl |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 4\.096 GiB     | M32ms v2  | 4     |
|                |                 |               | M64s v2   | 3     |
|                |                 |               | M64ms v2  | 2     |
|                |                 |               | M128ms v2 | 1     |
|                |                 |               | M128s v2  | 1     |

### <a name="msv2medmem-type1"></a>Msv2MedMem-Type1
Msv2MedMem-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8280) nutzt. Sie hat 112 physische Kerne, 192 vCPUs und 2.048 GiB RAM. Auf Msv2MedMem-Type1 werden VMs der [Msv2-Serie](msv2-mdsv2-series.md) ausgeführt, darunter Msv2- und Mmsv2-VMs.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers    | VM-Anzahl |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 2.048 GiB     | M32ms v2   | 2     |
|                |                 |               | M64s v2    | 2     |
|                |                 |               | M64ms v2   | 1     |
|                |                 |               | M128s v2   | 1     |

## <a name="mdsv2"></a>Mdsv2
### <a name="mdmsv2medmem-type1"></a>Mdmsv2MedMem-Type1
Mdmsv2MedMem-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8280) nutzt. Sie hat 112 physische Kerne, 192 vCPUs und 4.096 GiB RAM. Auf Mdmsv2MedMem-Type1 werden VMs der [Msv2-Serie](msv2-mdsv2-series.md) ausgeführt, darunter Mdsv2- und Mdmsv2-VMs.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers    | VM-Anzahl |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 4\.096 GiB     | M32dms v2  | 4     |
|                |                 |               | M64ds v2   | 2     |
|                |                 |               | M64dms v2  | 2     |
|                |                 |               | M128ds v2  | 1     |
|                |                 |               | M128dms v2 | 1     |

### <a name="mdsv2medmem-type1"></a>Mdsv2MedMem-Type1
Mdsv2MedMem-Type1 ist eine Dedicated Host-SKU, die den Intel® Cascade Lake-Prozessor (Xeon® Platinum 8280) nutzt. Sie hat 112 physische Kerne, 192 vCPUs und 2.048 GiB RAM. Auf Mdsv2MedMem-Type1 werden VMs der [Msv2-Serie](msv2-mdsv2-series.md) ausgeführt, darunter Mdsv2- und Mdmsv2-VMs.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers   | VM-Anzahl |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 2.048 GiB     | M32dms v2 | 2     |
|                |                 |               | M64ds v2  | 2     |
|                |                 |               | M64dms v2 | 1     |
|                |                 |               | M128ds v2 | 1     |

## <a name="nvasv4"></a>NVasv4
### <a name="nvasv4-type1"></a>NVasv4-Type1

NVasv4-Type1 ist eine Dedicated Host-SKU, die den AMD® Rome-Prozessor (EPYC™ 7V12) mit AMD Radeon Instinct MI25-GPUs nutzt. Sie hat 128 physische Kerne, 128 vCPUs und 448 GiB RAM. Auf NVasv4-Type1 werden VMs der [NVsv4-Serie](nvv4-series.md) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem NVasv4-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers   | VM-Anzahl |
|----------------|-----------------|---------------|-----------|-------|
| 128            | 128             | 448 GiB       | NV4as v4  | 30    |
|                |                 |               | NV8as v4  | 15    |
|                |                 |               | NV16as v4 | 7     |
|                |                 |               | NV32as v4 | 3     |

## <a name="nvsv3"></a>NVsv3
### <a name="nvsv3-type1"></a>NVsv3-Type1

NVsv3-Type1 ist eine Dedicated Host-SKU, die den Intel® Broadwell-Prozessor (E5-2690 v4) mit NVDIDIA Tesla M60-GPUs und NVIDIA GRID-Technologie nutzt. Sie hat 28 physische Kerne, 48 vCPUs und 448 GiB RAM. Auf NVsv3-Type1 werden VMs der [NVv3-Serie](nvv3-series.md) ausgeführt.

Im Folgenden sehen Sie die Konfiguration für die maximale Zusammenstellung gleicher VMs, die auf einem NVsv3-Type1-Host bereitgestellt werden können.

| Physische Kerne | Verfügbare vCPUs | Verfügbarer RAM | Größe des virtuellen Computers  | VM-Anzahl |
|----------------|-----------------|---------------|----------|-------|
| 28             | 48              | 448 GiB       | NV12s v3 | 4     |
|                |                 |               | NV24s v3 | 2     |
|                |                 |               | NV48s v3 | 1     | 


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der Übersicht zu [dedizierten Hosts](dedicated-hosts.md).

- Bei den [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md) finden Sie eine Beispielvorlage, die sowohl Zonen als auch Fehlerdomänen für maximale Resilienz in einer Region verwendet.