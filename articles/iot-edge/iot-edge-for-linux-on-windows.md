---
title: Was ist Azure IoT Edge für Linux unter Windows? | Microsoft-Dokumentation
description: Übersicht über das Ausführen von Linux-IoT Edge-Modulen auf Windows 10-Geräten
author: kgremban
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/18/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 881d0fe19339976342db866072dcf86009b1bbd5
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578859"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows"></a>Was ist Azure IoT Edge für Linux unter Windows?

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge für Linux unter Windows ermöglicht Ihnen das Ausführen von Linux-Workloads in Containern zusammen mit Windows-Anwendungen in Windows-IoT-Bereitstellungen. Unternehmen, die für die Unterstützung ihrer Edgegeräte Windows-IoT nutzen, können jetzt von den cloudnativen Analyselösungen profitieren, die in Linux erstellt werden.

Für IoT Edge für Linux unter Windows wird eine Linux-VM auf einem Windows-Gerät ausgeführt. Die Linux-VM wird mit der IoT Edge-Runtime vorinstalliert. Alle auf dem Gerät bereitgestellten IoT Edge-Module werden auf der VM ausgeführt. Gleichzeitig können Windows-Anwendungen, die auf dem Windows-Hostgerät ausgeführt werden, mit den auf der Linux-VM ausgeführten Modulen kommunizieren.

[Legen Sie noch heute los](how-to-provision-single-device-linux-on-windows-symmetric.md).

## <a name="components"></a>Komponenten

IoT Edge für Linux unter Windows nutzt die folgenden Komponenten für die parallele Ausführung von Linux- und Windows-Workloads und deren nahtlose Kommunikation:

* **Eine Linux-VM, auf der Azure IoT Edge ausgeführt wird**: Eine Linux-VM auf der Grundlage des Microsoft-eigenen [CBL-Mariner](https://github.com/microsoft/CBL-Mariner)-Betriebssystems wird mit der IoT Edge-Runtime erstellt und als Umgebung für IoT Edge-Workloads der 1. Ebene validiert.

* **Windows Admin Center:** Eine IoT Edge-Erweiterung für Windows Admin Center ermöglicht die Installation, Konfiguration und Diagnose von IoT Edge auf der Linux-VM. Mit Windows Admin Center kann IoT Edge für Linux unter Windows auf dem lokalen Gerät bereitgestellt werden. Außerdem können Sie damit eine Verbindung mit Zielgeräten herstellen und diese remote verwalten.

* **Microsoft Update**: Durch die Integration in Microsoft Update werden die Komponenten der Windows-Runtime, die CBL-Mariner-Linux-VM und IoT Edge auf dem neuesten Stand gehalten.

![Windows und die Linux-VM werden parallel ausgeführt, während Windows Admin Center beide Komponenten steuert.](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Aufgrund der bidirektionalen Kommunikation zwischen Windows-Prozess und Linux-VM können Windows-Prozesse Benutzeroberflächen oder Hardwareproxys für Workloads bereitstellen, die in Linux-Containern ausgeführt werden.


## <a name="prerequisites"></a>Voraussetzungen

Ein Windows-Gerät mit den folgenden Mindestanforderungen:

* Systemanforderungen
   * Windows 10¹/11 (Pro, Enterprise, IoT Enterprise)
   * Windows Server 2019¹/2022  
   <sub>¹ Windows 10 und Windows Server 2019, mindestens Build 17763, alle aktuellen kumulativen Updates installiert.</sub>

* Hardwareanforderungen
  * Mindestens erforderlicher freier Arbeitsspeicher: 1 GB
  * Mindestens erforderlicher freier Speicherplatz: 10 GB


## <a name="samples"></a>Beispiele

Der Fokus von IoT Edge für Linux unter Windows liegt ganz klar auf der Interoperabilität zwischen den Linux- und den Windows-Komponenten.

Beispiele für die Kommunikation zwischen Windows-Anwendungen und IoT Edge-Modulen finden Sie in den [EFLOW- und Windows 10 IoT-Beispielen](https://aka.ms/AzEFLOW-Samples).

## <a name="support"></a>Support

Nutzen Sie die Kanäle des IoT Edge-Supports und für Feedback, um Unterstützung bei IoT Edge für Linux unter Windows zu erhalten.

**Melden von Fehlern**: Fehler im Zusammenhang mit Azure IoT Edge für Linux unter Windows können auf der Seite [iotedge-eflow-Probleme](https://aka.ms/AzEFLOW-Issues) gemeldet werden. Fehler zu IoT Edge können auf der Seite [Probleme](https://github.com/azure/iotedge/issues) des Open-Source-Projekts für IoT Edge gemeldet werden.

**Microsoft-Kundendienstteam:** Benutzer mit einem [Supportplan](https://azure.microsoft.com/support/plans/) können direkt über das [Azure-Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) ein Supportticket erstellen, um sich an das Microsoft-Kundendienstteam zu wenden.

**Anfragen von Features:** Angefragte Features werden von Azure IoT Edge über die [User Voice-Seite](https://feedback.azure.com/d365community/forum/0e2fff5d-f524-ec11-b6e6-000d3a4f0da0) des Produkts verfolgt.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich für weitere Informationen und ein Beispiel aus der Praxis das Video [IoT Edge for Linux on Windows 10 IoT Enterprise](https://aka.ms/azeflow-show) (IoT Edge für Linux unter Windows 10 IoT Enterprise).

Führen Sie die Schritte unter [Manuelles Bereitstellen eines einzelnen Azure IoT Edge für Linux unter Windows-Geräts](how-to-provision-single-device-linux-on-windows-symmetric.md) aus, um ein Gerät mit IoT Edge für Linux unter Windows einzurichten.
