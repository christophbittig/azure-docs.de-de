---
title: Unterstützte Betriebssysteme, Container-Engines (Azure IoT Edge)
description: Erfahren Sie, welche Betriebssysteme zur Ausführung von Azure IoT Edge-Daemon und -Runtime verwendet werden können, und welche Containerengines für Ihre Produktionsgeräte unterstützt werden.
author: kgremban
ms.author: kgremban
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 88944f1762c217d717ff88290dd9d9996b733508
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423012"
---
# <a name="azure-iot-edge-supported-systems"></a>Von Azure IoT Edge unterstützte Systeme

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

In diesem Artikel erhalten Sie Informationen darüber, welche Systeme und Komponenten (offiziell oder als Vorschauversion) von IoT Edge unterstützt werden.

## <a name="get-support"></a>Support

Wenn bei der Verwendung von Azure IoT Edge Probleme auftreten, gibt es mehrere Möglichkeiten, um Unterstützung zu erhalten. Versuchen Sie es mit einer der folgenden Supportoptionen:

**Melden von Fehlern:** Der Großteil der Entwicklungsarbeiten für Azure IoT Edge findet im Rahmen des entsprechenden Open-Source-Projekts statt. Fehler können auf der [Problemseite](https://github.com/azure/iotedge/issues) des Projekts gemeldet werden. Fehler im Zusammenhang mit Azure IoT Edge für Linux unter Windows können auf der Seite für [iotedge-eflow-Probleme](https://github.com/azure/iotedge-eflow/issues) gemeldet werden. Fehlerbehebungen aus den Projekten werden schnell in Produktupdates integriert.

**Microsoft-Kundendienstteam:** Benutzer mit einem [Supportplan](https://azure.microsoft.com/support/plans/) können direkt über das [Azure-Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) ein Supportticket erstellen, um sich an das Microsoft-Kundendienstteam zu wenden.

**Anfragen von Features:** Angefragte Features werden von Azure IoT Edge über die [User Voice-Seite](https://feedback.azure.com/d365community/forum/0e2fff5d-f524-ec11-b6e6-000d3a4f0da0) des Produkts verfolgt.

## <a name="container-engines"></a>Container-Engines

Azure IoT Edge-Module werden als Container implementiert, für deren Start IoT Edge eine Containerengine benötigt. Mit „moby-engine“ stellt Microsoft eine Container-Engine bereit, um diese Anforderung zu erfüllen. Diese Containerengine basiert auf dem Open-Source-Projekt „Moby“. Weitere gängige Container-Engines sind Docker CE und Docker EE. Diese basieren ebenfalls auf dem Open-Source-Projekt „Moby“ und sind mit Azure IoT Edge kompatibel. Microsoft bietet bestmöglichen Support für Systeme mit diesen Containerengines, kann aber keine Fehlerbehebungen für diese bereitstellen. Aus diesem Grund empfiehlt Microsoft, für Produktionssysteme die Moby-Engine zu verwenden.

<br>
<center>

![Die Moby-Engine als Containerruntime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Betriebssysteme

Azure IoT Edge ist für die meisten containerfähigen Betriebssysteme geeignet. Allerdings werden nicht alle diese Systeme gleich unterstützt. Betriebssysteme sind in Ebenen unterteilt, die jeweils den Grad der zu erwartenden Unterstützung angeben.

* Systeme der Ebene 1 werden unterstützt. Bei Systemen der Ebene 1, Microsoft:
  * Microsoft führt für dieses Betriebssystem automatisierte Tests durch.
  * Microsoft stellt Installationspakete für sie bereit.
* Systeme der Ebene 2 sind mit Azure IoT Edge kompatibel und relativ einfach zu verwenden. Bei Systemen der Ebene 2:
  * Microsoft hat informelle Tests auf den Plattformen durchgeführt oder weiß, dass ein Partner Azure IoT Edge auf der Plattform erfolgreich ausführt.
  * Installationspakete für andere Plattformen können ggf. auch für diese Plattformen verwendet werden.

Die Familie des Hostbetriebssystems muss mit der Familie des Gastbetriebssystems, das im Container eines Moduls verwendet wird, immer übereinstimmen.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Mit anderen Worten: Sie können unter Linux nur Linux-Container und unter Windows nur Windows-Container verwenden. Bei Verwendung von Windows-Containern werden nur Container mit Prozessisolation, aber keine Container mit Hyper-V-Isolation unterstützt.  

IoT Edge für Linux unter Windows verwendet IoT Edge auf einer Linux-VM, die auf einem Windows-Host ausgeführt wird. Auf diese Weise können Sie Linux-Module auf einem Windows-Gerät ausführen.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Ebene 1

Die in der folgenden Tabelle aufgeführten Systeme (entweder allgemein verfügbare Versionen oder Vorschauversionen) werden von Microsoft unterstützt und mit jedem neuen Release getestet.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge unterstützt Module, die entweder als Linux- oder Windows-Container erstellt wurden. Linux-Container können auf Linux-Geräten bereitgestellt oder mithilfe von IoT Edge für Linux unter Windows auf Windows-Geräten bereitgestellt werden. Windows-Container können nur auf Windows-Geräten bereitgestellt werden.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Von der Azure IoT Edge-Version 1.2 werden nur Module unterstützt, die als Linux-Container erstellt wurden.

Die Ausführung von IoT Edge-Version 1.2 auf Windows-Geräten wird derzeit nicht unterstützt. Für die Ausführung von IoT Edge auf Windows-Geräten wird die Verwendung von [IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) empfohlen. Dabei wird allerdings nur IoT Edge 1.1 ausgeführt. Weitere Informationen finden Sie in der Version [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) dieses Artikels.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Linux-Container

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Module, die als Linux-Container erstellt wurden, können auf Linux- oder Windows-Geräten bereitgestellt werden. Bei Linux-Geräten wird die IoT Edge-Runtime direkt auf dem Hostgerät installiert. Bei Windows-Geräten wird ein virtueller, mit der IoT Edge-Runtime vordefinierter Linux-Computer auf dem Hostgerät ausgeführt.

Für die Ausführung von IoT Edge auf Windows-Geräten wird die Verwendung von [IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) empfohlen.

| Betriebssystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/support/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/support/green-check.png) |  | Public Preview |
| Windows 10 Pro | ![Windows 10 Pro + AMD64](./media/support/green-check.png) |  |  |
| Windows 10 Enterprise | ![Windows 10 Enterprise + AMD64](./media/support/green-check.png) |  |  |
| Windows 10 IoT Enterprise | ![Windows 10 IoT Enterprise + AMD64](./media/support/green-check.png) |  |  |
| Windows Server 2019 | ![Windows Server 2019 + AMD64](./media/support/green-check.png) |  |  |

Es kommen nur Windows-Betriebssysteme ab Version 1809 (Build 17763) infrage.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Betriebssystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/support/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/support/green-check.png) |  | Public Preview |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Der Support für Ubuntu Server 16.04 endete mit dem Release von IoT Edge, Version 1.1.

#### <a name="windows-containers"></a>Windows-Container

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS ist der letzte Releasekanal, der Windows-Container unterstützt. Ab Version 1.2 werden Windows-Container nicht mehr unterstützt. Wenn Sie IoT Edge auf Windows-Geräten ausführen möchten, sollten Sie [IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) verwenden oder dorthin wechseln.

Als Windows-Container entwickelte Module können nur auf Windows-Geräten bereitgestellt werden.

| Betriebssystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/support/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/support/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/support/green-check.png) |  |  |

Es kommen nur Windows-Betriebssysteme mit Version 1809 (Build 17763) infrage. Der spezifische Build von Windows ist für IoT Edge unter Windows erforderlich, da die Version der Windows-Container genau mit der Version des Windows-Hostgeräts übereinstimmen muss. Windows-Container verwenden zurzeit nur Build 17763.

>[!NOTE]
>Der Support für Windows 10 IoT Core endete mit dem Release von IoT Edge, Version 1.1.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS ist der letzte Releasekanal, der Windows-Container unterstützt. Ab Version 1.2 werden Windows-Container nicht mehr unterstützt.

Informationen zu unterstützten Betriebssystemen für Windows-Container finden Sie in der Version [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) dieses Artikels.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Ebene 2

Die in der folgenden Tabelle aufgeführten Systeme gelten als kompatibel mit Azure IoT Edge, werden aber nicht aktiv getestet oder von Microsoft verwaltet.

| Betriebssystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS-7](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7) | ![CentOS + AMD64](./media/support/green-check.png) | ![CentOS + ARM32v7](./media/support/green-check.png) | ![CentOS + ARM64](./media/support/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/support/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/support/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/support/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/support/green-check.png) | ![Debian 9 + ARM32v7](./media/support/green-check.png) | ![Debian 9 + ARM64](./media/support/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/support/green-check.png) | ![Debian 10 + ARM32v7](./media/support/green-check.png) | ![Debian 10 + ARM64](./media/support/green-check.png) |
| [Debian 11](https://www.debian.org/releases/bullseye/) | ![Debian 11 + AMD64](./media/support/green-check.png) | ![Debian 11 + ARM32v7](./media/support/green-check.png) | ![Debian 11 + ARM64](./media/support/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/support/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/support/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/support/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/support/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/support/green-check.png) |
| [RHEL 7](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7) | ![RHEL 7 + AMD64](./media/support/green-check.png) | ![RHEL 7 + ARM32v7](./media/support/green-check.png) | ![RHEL 7 + ARM64](./media/support/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/support/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/support/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/support/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/support/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/support/green-check.png) | ![Yocto + ARM32v7](./media/support/green-check.png) | ![Yocto + ARM64](./media/support/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/support/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/support/green-check.png) |

<sup>1</sup> Die Installationsschritte für Ubuntu Server 18.04 in [Installieren oder Deinstallieren von Azure IoT Edge für Linux](how-to-provision-single-device-linux-symmetric.md) sollten ohne irgendwelche Änderungen unter Ubuntu 20.04 funktionieren.

## <a name="releases"></a>Releases

IoT Edge-Releaseressourcen und -Hinweise sind auf der Seite [azure-iotedge releases](https://github.com/Azure/azure-iotedge/releases) verfügbar. Dieser Abschnitt spiegelt Informationen aus diesen Versionshinweisen wider, um Ihnen die Visualisierung der Komponenten der einzelnen Versionen zu erleichtern.

In der folgenden Tabelle sind die in den einzelnen Releases enthaltenen Komponenten aufgeführt (ab 1.2.0): Die in dieser Tabelle angegebenen Komponenten können einzeln installiert oder aktualisiert werden und sind mit älteren Versionen kompatibel.

| Freigabe | aziot-edge | edgeHub<br>edgeAgent | aziot-identity-service |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0<br>1.2.1<br>1.2.2<br>1.2.3 | 1.2.0<br>1.2.1<br>1.2.2<br> 1.2.3  | 1.2.0<br>1.2.1<br>1.2.2<br><br> |

In der folgenden Tabelle sind die in den einzelnen Releases enthaltenen Komponenten aufgeführt (bis 1.1 LTS): Die in dieser Tabelle angegebenen Komponenten können einzeln installiert oder aktualisiert werden und sind mit älteren Versionen kompatibel.

IoT Edge 1.1 ist der erste Releasekanal mit langfristigem Support (Long-Term Support, LTS). In dieser Version wurden keine neuen Features eingeführt, allerdings enthält sie Sicherheitsupdates und Fixes für Regressionen. IoT Edge 1.1 LTS verwendet .NET Core 3.1 und wird bis zum 3. December 2022 unterstützt, um dem [Releaselebenszyklus für .NET Core und .NET 5](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) gerecht zu werden.

| Release | iotedge | edgeHub<br>edgeAgent | libiothsm | moby |
|--|--|--|--|--|
| **1.1 LTS** | 1.1.0<br>1.1.1<br><br>1.1.3<br>1.1.4<br>1.1.5<br>1.1.6 | 1.1.0<br>1.1.1<br>1.1.2<br>1.1.3<br>1.1.4<br>1.1.5<br><br> | 1.1.0<br>1.1.1<br><br>1.1.3<br>1.1.4<br>1.1.5<br>1.1.6 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

>[!IMPORTANT]
>Beim Release eines langfristigen Supportkanals empfehlen wir, dass alle aktuellen Kunden, die zurzeit 1.0.x ausführen, ihre Geräte auf 1.1.x aktualisieren, um fortlaufenden Support zu erhalten.

IoT Edge verwendet das Microsoft.Azure.Device.Client SDK. Weitere Informationen finden Sie im [GitHub-Repository „Azure IoT C# SDK“](https://github.com/Azure/azure-iot-sdk-csharp) oder im [Inhalt der Referenz zum Azure SDK für .NET](/dotnet/api/overview/azure/iot/client). Die folgende Liste zeigt die Version des Client SDK, mit dem die einzelnen Releases getestet wurden:

| IoT Edge-Version | Microsoft.Azure.Devices.Client-SDK-Version |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1.1 (LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge kann auf virtuellen Computern ausgeführt werden. Das Verwenden eines virtuellen Computers als IoT Edge-Gerät ist eine verbreitete Vorgehensweise, wenn Kunden eine vorhandene Infrastruktur um Edgeintelligenz erweitern möchten. Die Familie des Host-VM-Betriebssystems muss mit der Familie des Gastbetriebssystems übereinstimmen, das im Container eines Moduls verwendet wird. Diese Anforderung gilt auch, wenn Azure IoT Edge direkt auf einem Gerät ausgeführt wird. Azure IoT Edge ist unabhängig von der zugrunde liegenden Virtualisierungstechnologie und funktioniert auf virtuellen Computern, die von Plattformen wie Hyper-V und vSphere unterstützt werden.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Azure IoT Edge auf einem virtuellen Computer](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Azure IoT Edge auf einem virtuellen Computer](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>Mindestsystemanforderungen

Azure IoT Edge funktioniert einwandfrei auf Geräten, die klein wie ein Raspberry Pi3 sind, bis hin zur Größe von Serverhardware. Die Auswahl der richtigen Hardware für Ihr Szenario hängt von den Workloads ab, die Sie ausführen möchten. Die endgültige Entscheidung für ein Gerät zu treffen, kann kompliziert sein; allerdings können Sie mit der Prototyperstellung einer Lösung auf herkömmlichen Laptops oder Desktops problemlos beginnen.

Das Sammeln von Erfahrungen während der Prototyperstellung wird Sie bei Ihrer endgültigen Geräteauswahl leiten. Zu den Fragen, die Sie berücksichtigen sollten, gehören:

* Wie viele Module umfasst Ihre Workload?
* Wie viele Schichten werden von den Containern in Ihren Modulen gemeinsam genutzt?
* In welcher Sprache werden Ihre Module geschrieben?
* Wie viele Daten sollen Ihre Module verarbeiten?
* Benötigen Ihre Module spezielle Hardware zum Beschleunigen ihrer Workloads?
* Was sind die gewünschten Leistungsmerkmale Ihrer Lösung?
* Wie hoch ist Ihr Hardwarebudget?
