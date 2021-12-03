---
title: Azure Service Fabric-Versionen
description: Erfahren Sie mehr über Clusterversionen in Azure Service Fabric und aktiv unterstützte Plattformversionen.
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 2d662ec817ed1fffed56836feb7a8e73f279408b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460373"
---
# <a name="service-fabric-supported-versions"></a>Unterstützte Service Fabric-Versionen
In den Tabellen in diesem Artikel werden die Service Fabric- und Plattformversionen beschrieben, die aktiv unterstützt werden.

## <a name="windows"></a>Windows

| Service Fabric-Runtime |Direktes Upgrade möglich von|Kann heruntergestuft werden auf*|Kompatible SDK- oder NuGet-Paket-Version|Unterstützte .NET-Runtimes** |Betriebssystemversion |Ende des Supports |
| --- | --- | --- | --- | --- | --- | --- |
| 8.2 RTO<br>8.2.1235.9590 | 8.0 CU3<br>8.0.536.9590 | 8.0 | Alle Versionen bis einschließlich Version 5.2 | .NET 5.0 (GA), >= .NET Core 3.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | Aktuelle Version |
| 8.1 CU3.1<br>8.1.337.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | .NET 5.0 (GA), >= .NET Core 3.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 30. Juni 2022 |
| 8.1 CU3<br>8.1.335.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | .NET 5.0 (GA), >= .NET Core 3.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 30. Juni 2022 |
| 8.1 CU2<br>8.1.329.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | .NET 5.0 (GA), >= .NET Core 3.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 30. Juni 2022 |
| 8.1 CU1<br>8.1.321.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | .NET 5.0 (GA), >= .NET Core 2.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 30. Juni 2022 |
| 8.1 RTO<br>8.1.316.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | .NET 5.0 (GA), >= .NET Core 2.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 30. Juni 2022 |
| 8.0 CU3<br>8.0.536.9590 | 7.1 CU10<br>7.1.510.9590 | 7.2 | Alle Versionen bis einschließlich Version 5.0 | .NET 5.0 (GA), >= .NET Core 2.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 28. Februar 2022 |
| 8.0 CU2<br>8.0.521.9590 | 7.1 CU10<br>7.1.510.9590 | 7.2 | Alle Versionen bis einschließlich Version 5.0 | .NET 5.0 (GA), >= .NET Core 2.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 28. Februar 2022 |
| 8.0 CU1<br>8.0.516.9590 | 7.1 CU10<br>7.1.510.9590 | 7.2 | Alle Versionen bis einschließlich Version 5.0 | .NET 5.0 (GA), >= .NET Core 2.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 28. Februar 2022 |
| 8.0 RTO<br>8.0.514.9590 | 7.1 CU10<br>7.1.510.9590 | 7.2 | Alle Versionen bis einschließlich Version 5.0 | .NET 5.0 (GA), >= .NET Core 2.1, <br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 28. Februar 2022 |
| 7.2 CU7<br>7.2.477.9590 | 7.0 CU9<br>7.0.478.9590 | 7.1 | Alle Versionen bis einschließlich Version 4.2 | .NET 5.0 (Vorschauunterstützung), >= .NET Core 2.1,<br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 30. November 2021 |
| 7.2 CU6<br>7.2.457.9590 | 7.0 CU4<br>7.0.470.9590 |7.1 | Alle Versionen bis einschließlich Version 4.2 | .NET 5.0 (Vorschauunterstützung), >= .NET Core 2.1,<br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date)| 30. November 2021 |
| 7.2 RTO-CU5<br>7.2.413.9590-7.2.452.9590 | 7.0 CU4<br>7.0.470.9590 | 7.1 |Alle Versionen bis einschließlich Version 4.2 | >= .NET Core 2.1,<br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date)| 30. November 2021 |
| 7.1<br>7.1.510.9590 |7.0 CU3<br>7.0.466.9590 |– | Alle Versionen bis einschließlich Version 4.1 | >= .NET Core 2.1,<br>Alle >= .NET Framework 4.5 | [Unterstützte Betriebssystemversionen](#supported-windows-versions-and-support-end-date) | 31. Juli 2021 |

\* Die Version sollte nicht mehr unterstützt werden.<br/>
** Service Fabric stellt keine .NET Core-Runtime zur Verfügung. Der Autor des Diensts ist dafür verantwortlich, die <a href="/dotnet/core/deploying/">Verfügbarkeit</a> sicherzustellen.

## <a name="supported-windows-versions-and-support-end-date"></a>Unterstützte Windows-Versionen und Supportenddatum
Die Unterstützung für Service Fabric auf bestimmten Betriebssystemen endet, wenn die Unterstützung für die Betriebssystemversion abläuft.


### <a name="windows-server"></a>Windows Server

| Betriebssystemversion | Enddatum für die Service Fabric-Unterstützung | Link zum Betriebssystemlebenszyklus |
|---|---|---|
|Windows Server 2019|09.01.2029|<a href="/lifecycle/products/windows-server-2019">Windows Server 2019 – Microsoft-Lebenszyklus</a>|
|Windows Server 2016 |12.01.2027|<a href="/lifecycle/products/windows-server-2016">Windows Server 2016 – Microsoft-Lebenszyklus</a>|
|Windows Server 2012 R2 |10.10.2023|<a href="/lifecycle/products/windows-server-2012-r2">Windows Server 2012 R2 – Microsoft-Lebenszyklus</a>|
|Version 20H2 |10.05.2022|<a href="/lifecycle/products/windows-server">Windows Server – Microsoft-Lebenszyklus</a>|
|Version 2004 |14.12.2021|<a href="/lifecycle/products/windows-server">Windows Server – Microsoft-Lebenszyklus</a>|
|Version 1909 |11.05.2021|<a href="/lifecycle/products/windows-server">Windows Server – Microsoft-Lebenszyklus</a>|

<br>

### <a name="windows-10"></a>Windows 10

| Betriebssystemversion | Enddatum für die Service Fabric-Unterstützung | Link zum Betriebssystemlebenszyklus |
| --- | --- | --- |
| Windows 10 2019 LTSC | 09.01.2029 | <a href="/lifecycle/products/windows-10-ltsc-2019">Windows 10 2019 LTSC – Microsoft-Lebenszyklus</a> |
| Version 20H2 | 09.05.2023 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise und Education – Microsoft-Lebenszyklus</a> |
| Version 2004 | 14.12.2021| <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise und Education – Microsoft-Lebenszyklus</a> |
| Version 1909 | 10.05.2022 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise und Education – Microsoft-Lebenszyklus</a> |
| Version 1809 | 11.05.2021 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise und Education – Microsoft-Lebenszyklus</a> |
| Version 1803 | 11.05.2021 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise und Education – Microsoft-Lebenszyklus</a> |

## <a name="linux"></a>Linux

| Service Fabric-Runtime | Direktes Upgrade möglich von |Kann heruntergestuft werden auf*|Kompatible SDK- oder NuGet-Paket-Version | Unterstützte .NET-Runtimes** | Betriebssystemversion | Ende des Supports |
| --- | --- | --- | --- | --- | --- | --- |
| 8.2 RTO<br>8.2.1124.1 | 8.0 CU3<br>8.0.527.1 | 8.0 | Alle Versionen bis einschließlich Version 5.2 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | Aktuelle Version |
| 8.1 CU3.1<br>8.1.340.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 30. Juni 2022 |
| 8.1 CU3<br>8.1.334.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 30. Juni 2022 |
| 8.1 CU2<br>8.1.328.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 30. Juni 2022 |
| 8.1 CU1<br>8.1.323.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 30. Juni 2022 |
| 8.1 RTO<br>8.1.320.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | Alle Versionen bis einschließlich Version 5.1 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 30. Juni 2022 |
| 8.0 CU3<br>8.0.527.1 | 7.1 CU8<br>7.1.508.1 | 7.2 | Alle Versionen bis einschließlich Version 5.0 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 28. Februar 2022 |
| 8.0 CU1<br>8.0.515.1 | 7.1 CU8<br>7.1.508.1 | 7.2 | Alle Versionen bis einschließlich Version 5.0 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 28. Februar 2022 |
| 8.0 RTO<br>8.0.513.1 | 7.1 CU8<br>7.1.508.1 | 7.2 | Alle Versionen bis einschließlich Version 5.0 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 28. Februar 2022 |
| 7.2 CU7<br>7.2.476.1 | 7.0 CU9<br>7.0.472.1 | 7.1 | Alle Versionen bis einschließlich Version 4.2 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 30. November 2021 |
| 7.2 RTO-CU6<br>7.2.431.1-7.2.456.1 | 7.0 CU4<br>7.0.469.1 | 7.1 | Alle Versionen bis einschließlich Version 4.2 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 30. November 2021 |
| 7.1<br>7.1.508.1| 7.0 CU3<br>7.0.465.1 | – | Alle Versionen bis einschließlich Version 4.1 | >= .NET Core 2.1 | [Unterstützte Betriebssystemversionen](#supported-linux-versions-and-support-end-date) | 31. Juli 2021 |

\* Die Version sollte nicht mehr unterstützt werden.<br/>
** Service Fabric stellt keine .NET Core-Runtime bereit. Der Autor des Diensts ist dafür verantwortlich, die <a href="/dotnet/core/deploying/">Verfügbarkeit</a> sicherzustellen.

## <a name="supported-linux-versions-and-support-end-date"></a>Unterstützte Linux-Versionen und Supportenddatum
Die Unterstützung für Service Fabric auf bestimmten Betriebssystemen endet, wenn die Unterstützung für die Betriebssystemversion abläuft.

#### <a name="ubuntu"></a>Ubuntu
| Betriebssystemversion | Enddatum für die Service Fabric-Unterstützung| Link zum Betriebssystemlebenszyklus |
| --- | --- | --- |
| Ubuntu 18.04 | April 2028 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu-Lebenszyklus</a>|
| Ubuntu 16.04 | April 2024 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu-Lebenszyklus</a>|

## <a name="service-fabric-version-name-and-number-reference"></a>Referenz zu Service Fabric-Versionsnamen und -nummern
In der folgende Tabelle werden die Versionsnamen von Service Fabric und die zugehörigen Versionsnummern aufgeführt.

| Versionsname | Windows-Versionsnummer | Linux-Versionsnummer |
| --- | --- | --- |
| 8.2 RTO | 8.2.1235.9590 | 8.2.1124.1 |
| 8.1 CU3.1 | 8.1.337.9590 | 8.1.340.1 |
| 8.1 CU3 | 8.1.335.9590 | 8.1.334.1 |
| 8.1 CU2 | 8.1.329.9590 | 8.1.328.1 |
| 8.1 CU1 | 8.1.321.9590 | 8.1.323.1 |
| 8.1 RTO | 8.1.316.9590 | 8.1.320.1 |
| 8.0 CU3 | 8.0.536.9590 | 8.0.527.1 |
| 8.0 CU2 | 8.0.521.9590 | Nicht verfügbar |
| 8.0 CU1 | 8.0.516.9590 | 8.0.515.1 | 
| 8.0 RTO | 8.0.514.9590 | 8.0.513.1 | 
| 7.2 CU7 | 7.2.477.9590 | 7.2.476.1 |
| 7.2 CU6 | 7.2.457.9590 | 7.2.456.1 |
| 7.2 CU5 | 7.2.452.9590 | 7.2.454.1 |
| 7.2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7.2 CU3 | 7.2.433.9590 | Nicht verfügbar |
| 7.2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7.2 RTO | 7.2.413.9590 | Nicht verfügbar |
| 7.1 CU10 | 7.1.510.9590 | Nicht verfügbar |
| 7.1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7.0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7.0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.4 CU8 | 6.4.670.9590 | Nicht verfügbar|
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU6 | 6.4.658.9590 | Nicht verfügbar|
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU2 | 6.4.622.9590 | Nicht verfügbar|
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.1 CU3 | 6.1.472.9494 | Nicht verfügbar|
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 5.7 CU4 | 5.7.221.9494 | Nicht verfügbar|
| 5.7 RTO | 5.7.198.9494 | Nicht verfügbar|
| 5.6 CU3 | 5.6.220.9494 | Nicht verfügbar|
| 5.6 CU2 | 5.6.210.9494 | Nicht verfügbar|
| 5.6 RTO | 5.6.204.9494 | Nicht verfügbar|
| 5.5 CU4 | 5.5.232.0 | Nicht verfügbar|
| 5.5 CU3 | 5.5.227.0 | Nicht verfügbar|
| 5.5 CU2 | 5.5.219.0 | Nicht verfügbar|
| 5.5 CU1 | 5.5.216.0    | Nicht verfügbar|
| 5.4 CU2 | 5.4.164.9494 | Nicht verfügbar|
| 5.3 CU3 | 5.3.311.9590 | Nicht verfügbar|
| 5.3 CU2 | 5.3.301.9590 | Nicht verfügbar|
| 5.3 CU1 | 5.3.204.9494 | Nicht verfügbar|
| 5.3 RTO | 5.3.121.9494 | Nicht verfügbar|
