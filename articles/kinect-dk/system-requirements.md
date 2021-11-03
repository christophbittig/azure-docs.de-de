---
title: Systemanforderungen für das Azure Kinect Sensor SDK
description: Informieren Sie sich über die Systemanforderungen für das Azure Kinect Sensor SDK unter Windows und Linux.
author: qm13
ms.author: quentinm
ms.custom: CI 115266, CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: Azure, Kinect, Systemanforderungen, CPU, GPU, USP, Setup, Einrichten, mindestens, Anforderungen
ms.openlocfilehash: ba6782ad0a0278f1f66266c8abbe91d33cb5bf68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085846"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Systemanforderungen für das Azure Kinect Sensor SDK

Dieses Dokument enthält Informationen zu den Systemanforderungen für die Installation des Sensor SDK und für die erfolgreiche Bereitstellung Ihres Azure Kinect DK.

## <a name="supported-operating-systems-and-architectures"></a>Unterstützte Betriebssysteme und Architekturen

- Windows 10 April 2018-Release (Version 1803, Betriebssystembuild 17134, x64) oder eine höhere Version
- Linux Ubuntu 18.04 (x64) mit einem GPU-Treiber, der OpenGLv4.4 oder höher verwendet

Das Sensor SDK ist für die Windows-API (Win32) für native Windows-Anwendungen in C/C++ verfügbar. Das SDK ist zurzeit für UWP-Anwendungen nicht verfügbar. Azure Kinect DK wird unter Windows 10 im S-Modus nicht unterstützt.

## <a name="development-environment-requirements"></a>Anforderungen an die Entwicklungsumgebung

Wenn Sie zur Entwicklung des Sensor SDK beitragen möchten, besuchen Sie [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK).

## <a name="minimum-host-pc-hardware-requirements"></a>Mindestanforderungen an die Hardware des Hostcomputers

Die Hardwareanforderungen des Hostcomputers richten sich nach der Anwendung, dem Algorithmus, der Sensorbildfrequenz und der Auflösung auf dem Host-PC. Für Windows wird folgende Mindestkonfiguration des Sensor SDK empfohlen:

- Intel&reg; CoreTM i3-Prozessor der 7. Generation (zwei Kerne, 2,4 GHz mit HD620-GPU oder schneller)
- 4 GB Arbeitsspeicher
- Dedizierter USB 3-Anschluss
- Grafiktreiberunterstützung für OpenGL 4.4 oder DirectX 11.0

Ältere oder weniger leistungsstarke CPUs können je nach Anwendungsfall auch funktionieren.

Die Leistung richtet sich auch nach dem verwendeten Windows- oder Linux-Betriebssystem und den Grafiktreibern.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Anforderungen an die Hardware des Hostcomputers für das Body Tracking

Die Anforderungen an den Hostcomputer für das Body Tracking sind höher als die Anforderungen an den allgemeinen Hostcomputer. Für Windows wird folgende Mindestkonfiguration des Body Tracking SDK empfohlen:

- Intel&reg; CoreTM i5-Prozessor der 7. Generation (vier Kerne, 2,4 GHz oder schneller)
- 4 GB Arbeitsspeicher
- NVIDIA GEFORCE GTX 1050 oder entsprechend
- Dedizierter USB 3-Anschluss

Die empfohlene Mindestkonfiguration setzt einen K4A_DEPTH_MODE_NFOV_UNBINNED-Tiefenmodus von 30 FPS für die Nachverfolgung von 5 Personen voraus. Ältere oder weniger leistungsstarke CPUs und NVIDIA-GPUs können je nach Anwendungsfall auch funktionieren.

## <a name="usb3"></a>USB 3

Es gibt bekannte Kompatibilitätsprobleme mit USB-Hostcontrollern. Weitere Informationen finden Sie auf der [Seite zur Problembehandlung](troubleshooting.md#usb3-host-controller-compatibility).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Kinect DK: Übersicht](about-azure-kinect-dk.md)

- [Schnellstart: Einrichten Ihres Azure Kinect DK-Geräts](set-up-azure-kinect-dk.md)

- [Einrichten von Azure Kinect Body Tracking](body-sdk-setup.md)
