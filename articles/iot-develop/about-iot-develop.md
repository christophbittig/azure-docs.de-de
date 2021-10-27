---
title: Einführung in die Entwicklung von Azure IoT-Geräten und Anwendungen
description: Hier erfahren Sie, wie Sie mit Azure IoT eingebettete Geräte entwickeln und gerätefähige Cloudanwendungen erstellen.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: 128bea34a2e391a44b4dba68d844c68f7331cb06
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994535"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Worum handelt es sich bei der Entwicklung von Azure IoT-Geräten und -Anwendungen?

Azure IoT ist eine Sammlung von verwalteten Plattformdiensten, mit denen Sie Ihre IoT-Geräte verbinden, überwachen und steuern. Azure IoT bietet Entwicklern eine Vielzahl von Optionen. Hierzu gehören Geräteplattformen, die Unterstützung von Clouddiensten, SDKs und Tools für die Entwicklung von auf Geräten nutzbaren Cloudanwendungen.

Dieser Artikel zeigt einige wichtige Überlegungen auf, die Entwickler anstellen sollten, wenn sie mit der Arbeit mit Azure IoT beginnen. Diese Konzepte dienen Ihnen als Entwickler für IoT-Geräte als Orientierungshilfe für Azure IoT-Optionen und den Einstieg. Insbesondere wird auf die folgenden Konzepte eingegangen:
- [Verstehen von Rollen für die Geräteentwicklung](#device-development-roles)
- [Auswählen der Hardware](#choosing-your-hardware)
- [Auswählen eines SDK](#choosing-an-sdk)
- [Auswählen von Verbindungsoptionen](#selecting-connection-options)

## <a name="device-development-roles"></a>Rollen für die Geräteentwicklung
Im vorliegenden Artikel werden zwei gängige Rollen beschrieben, die auf viele Geräteentwickler zutreffen. Eine Rolle ist hierbei eine Sammlung aus zusammengehörigen Entwicklungsaufgaben. Sie sollten wissen, in welcher Art von Entwicklungsrolle Sie gerade arbeiten. Die Rolle wirkt sich auf viele Entwicklungsentscheidungen aus.

* **Entwicklung von Geräteanwendungen**: Entspricht modernen Entwicklungsmethoden, zielt auf viele Sprachen höherer Ordnung ab und funktioniert unter universellen Betriebssystemen wie Windows oder Linux.

* **Entwicklung von eingebetteten Geräten**: Beschreibt die Entwicklung von Geräten mit eingeschränkten Ressourcen. Ein Gerät mit eingeschränkten Ressourcen wird häufig eingesetzt, um die Kosten pro Einheit, den Energieverbrauch oder die Größe des Geräts zu reduzieren. Diese Geräte haben direkte Kontrolle über die Hardwareplattform, auf der sie ausgeführt werden.

### <a name="device-application-development"></a>Entwicklung von Geräteanwendungen
Entwickler von Geräteanwendungen passen vorhandene Geräte an, sodass diese eine Verbindung mit der Cloud herstellen und in IoT-Lösungen integriert werden können. Diese Geräte können Sprachen höherer Ordnung unterstützen – wie z. B. C# oder Python – und unterstützen häufig ein stabiles universelles Betriebssystem wie Windows oder Linux. Gängige Zielgeräte sind beispielsweise PCs, Container, Raspberry Pis und mobile Geräte. 

Anstatt im großen Stil Anwendungen für eingeschränkte Geräte zu entwickeln, konzentrieren sich diese Entwickler darauf, ein bestimmtes IoT-Szenario zu ermöglichen, das für ihre Cloudlösung erforderlich ist. Einige dieser Entwickler arbeiten auch an eingeschränkten Geräten für ihre Cloudlösung. Informationen zur Entwicklung von Geräten mit Ressourceneinschränkungen finden Sie weiter unten im Abschnitt [Entwicklung von eingebetteten Geräten](#embedded-device-development).

> [!IMPORTANT]
> Informationen zu den ersten Schritten finden Sie unter [Entwicklung von eingebetteten Geräten](about-iot-sdks.md#device-sdks).

### <a name="embedded-device-development"></a>Entwicklung von eingebetteten Geräten
Die Entwicklung von eingebetteten Geräten wird für Geräte genutzt, die nur über eingeschränkte Arbeitsspeicher- und Verarbeitungskapazitäten verfügen. Bei diesen Geräten sind die Möglichkeiten im Vergleich zu einer herkömmlichen Entwicklungsplattform eingeschränkt.

Eingebettete Geräte verwenden in der Regel ein Echtzeit-Betriebssystem (Real-Time Operating System, RTOS) oder gar kein Betriebssystem. Eingebettete Geräte haben vollständige Kontrolle über die Hardware, da kein universelles Betriebssystem vorhanden ist. Aus diesem Grund sind eingebettete Geräte eine gute Wahl für Echtzeitsysteme.

Die aktuellen SDKs für eingebettete Geräte zielen auf die Programmiersprache **C** ab. Die SDKs für eingebettete Geräte stellen entweder kein Betriebssystem bereit oder bieten Azure RTOS-Unterstützung. Sie sind speziell für eingebettete Ziele konzipiert. Bei den Entwurfsüberlegungen muss berücksichtigt werden, dass der Speicherbedarf möglichst gering sein muss und keine Arbeitsspeicherzuweisung erfolgen darf.

Wenn Ihr Gerät ein universelles Betriebssystem ausführen kann, sollten Sie den Abschnitt [Entwicklung von Geräteanwendungen](#device-application-development) lesen. Damit erhalten Sie umfassendere Entwicklungsoptionen.

> [!IMPORTANT]
> Informationen zu den ersten Schritten finden Sie unter [SDKs für eingebettete Geräte](about-iot-sdks.md#embedded-device-sdks).

## <a name="choosing-your-hardware"></a>Auswählen der Hardware
Azure IoT-Geräte sind die grundlegenden Bausteine einer IoT-Lösung und sind dafür zuständig, ihre Umgebung zu beobachten und mit ihr zu interagieren. Es gibt viele verschiedene Arten von IoT-Geräten. Es ist nützlich, diese Arten zu kennen und zu verstehen, wie sie sich auf den Entwicklungsprozess auswirken.

Mehr über die Unterschiede zwischen den in diesem Artikel behandelten Gerätetypen erfahren Sie unter [Informationen zu IoT-Gerätetypen](concepts-iot-device-types.md).

## <a name="choosing-an-sdk"></a>Auswählen eines SDK
Azure IoT-Geräteentwicklern steht eine Vielzahl verschiedener Geräte-SDKs und Azure-Dienst-SDKs zur Verfügung, die Sie beim Erstellen von auf Geräten nutzbaren Cloudanwendungen unterstützen. Die SDKs optimieren die Entwicklung und vereinfachen die komplexe Aufgabe, Geräte miteinander zu verbinden und zu verwalten. 

Wie bereits im Abschnitt [Rollen für die Geräteentwicklung](#device-development-roles) erwähnt, gibt es drei Arten von IoT-SDKs für die Geräteentwicklung:
- SDKs für eingebettete Geräte (für eingeschränkte Geräte)
- Geräte-SDKs (für die Verwendung von Sprachen höherer Ordnung, um Geräte mit IoT-Anwendungen zu verbinden)
- Dienst-SDKs (zum Erstellen von Azure IoT-Lösungen, die Geräte mit Diensten verbinden)

Weitere Informationen zur Auswahl eines Azure IoT-Geräte- oder Dienst-SDK finden Sie unter [Übersicht über Azure IoT-Geräte-SDKs](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Auswählen von Verbindungsoptionen
Ein wichtiger Schritt im Entwicklungsprozess ist die Auswahl der Optionen, die Sie zur Verbindungsherstellung und Verwaltung Ihrer Geräte verwenden werden. Hier sind zwei wichtige Aspekte zu beachten:
- Die Auswahl einer IoT-Anwendungsplattform zum Hosten der Geräte. Bei Azure IoT bedeutet dies die Auswahl von IoT Hub oder IoT Central.
- Die Auswahl von Entwicklertools, mit denen Sie Geräte verbinden, verwalten und überwachen.

Weitere Informationen zum Auswählen von Anwendungsplattform und Tools finden Sie unter [Übersicht: Verbindungsoptionen für Azure IoT-Geräteentwickler](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Nächste Schritte
Wählen Sie die Reihe mit Schnellstartanleitungen zur eingebetteten Entwicklung oder Anwendungsentwicklung aus, die für Ihre Entwicklungsrolle am relevantesten ist. Diese Artikel veranschaulichen folgende Grundlagen: Entwickeln einer Azure IoT-Anwendung zum Hosten von Geräten, Verwenden eines SDK, Herstellen einer Verbindung mit einem Gerät und Senden von Telemetriedaten.  
- [Azure IoT-Geräteentwicklung](index.yml)
