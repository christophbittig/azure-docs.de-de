---
title: 'Tutorial: Bereitstellen von Geräten für Hubs mit Lastenausgleich mithilfe von Azure IoT Hub Device Provisioning Service'
description: In diesem Tutorial wird gezeigt, wie Device Provisioning Service (DPS) die automatische Gerätebereitstellung für IoT-Hubs mit Lastenausgleich im Azure-Portal ermöglicht.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/18/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: e7e9517daaf8258eb02147f97993e474ffcfc0a8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226146"
---
# <a name="tutorial-provision-devices-across-load-balanced-iot-hubs"></a>Tutorial: Bereitstellen von Geräten für IoT-Hubs mit Lastausgleich

In diesem Tutorial wird gezeigt, wie Sie mit Device Provisioning Service Geräte für mehrere IoT Hubs mit Lastausgleich bereitstellen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen eines zweiten Geräts für eine zweite IoT Hub-Instanz mit dem Azure-Portal 
> * Hinzufügen eines Registrierungslisteneintrags für das zweite Gerät
> * Festlegen der Device Provisioning Service-Zuordnungsrichtlinie zur **gleichmäßigen Verteilung**
> * Verknüpfen der neuen IoT Hub-Instanz mit Device Provisioning Service

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial baut auf dem vorherigen Tutorial [Bereitstellen eines Geräts für einen Hub](tutorial-provision-device-to-hub.md) auf.

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Bereitstellen eines zweiten Geräts für eine zweite IoT Hub-Instanz mit dem Azure-Portal

Um ein zweites Gerät für eine andere IoT Hub-Instanz bereitzustellen, führen Sie die Schritte im Tutorial [Bereitstellen eines Geräts für einen Hub](tutorial-provision-device-to-hub.md) durch.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Hinzufügen eines Registrierungslisteneintrags für das zweite Gerät

Die Registrierungsliste weist Device Provisioning Service an, welche Nachweismethode (die Methode zur Bestätigung einer Geräteidentität) beim Gerät verwendet wird. Der nächste Schritt besteht darin, einen Registrierungslisteneintrag für das zweite Gerät hinzuzufügen.

1. Klicken Sie auf der Seite für Ihre Device Provisioning Service-Instanz auf **Registrierungen verwalten**. Die Seite **Registrierungslisteneintrag hinzufügen** wird angezeigt.
2. Klicken Sie oben auf der Seite auf **Hinzufügen**.
3. Füllen Sie die Felder aus, und klicken Sie dann auf **Speichern**.

## <a name="set-the-device-provisioning-service-allocation-policy"></a>Festlegen der Device Provisioning Service-Zuordnungsrichtlinie

Die Zuordnungsrichtlinie ist eine Device Provisioning Service-Einstellung, die festlegt, wie Geräte einer IoT Hub-Instanz zugewiesen werden. Es gibt drei unterstützte Zuordnungsrichtlinien: 

1. **Niedrigste Latenz**: Geräte werden basierend auf dem Hub mit der geringsten Latenz auf dem Gerät für eine IoT Hub-Instanz bereitgestellt.
2. **Gleichmäßig gewichtete Verteilung** (Standard): Bei verknüpften IoT Hubs ist die Wahrscheinlichkeit gleich hoch, dass ihnen Geräte bereitgestellt werden. Dies ist die Standardeinstellung. Wenn Sie nur für eine IoT Hub-Instanz Geräte bereitstellen, können Sie diese Einstellung beibehalten. Wenn Sie die Verwendung von IoT Hub planen, aber davon ausgehen, dass sich die Anzahl von Hubs mit zunehmender Anzahl von Geräten erhöht, ist zu beachten, dass die Richtlinie bei der Zuweisung zu einem IoT-Hub keine zuvor registrierten Geräte berücksichtigt. Alle verknüpften Hubs haben basierend auf der Gewichtung des verknüpften IoT-Hubs die gleiche Chance, eine Geräteregistrierung zu erhalten. Wenn ein IoT-Hub allerdings sein Gerätekapazitätslimit erreicht hat, erhält er keine weiteren Geräteregistrierungen. Sie können jedoch die Gewichtung der Zuordnung für jeden verknüpften IoT-Hub anpassen.

3. **Statische Konfiguration über die Registrierungsliste**: Die Angabe der gewünschten IoT Hub-Instanz in der Registrierungsliste hat gegenüber der Zuordnungsrichtlinie auf Ebene des Device Provisioning-Diensts Vorrang.

### <a name="how-the-allocation-policy-assigns-devices-to-iot-hubs"></a>Zuweisung von Geräten zu IoT-Hubs durch die Zuordnungsrichtlinie

Es kann wünschenswert sein, nur einen IoT-Hub zu verwenden, bis eine bestimmte Anzahl von Geräten erreicht ist. In diesem Szenario ist Folgendes zu beachten: Sobald ein neuer IoT-Hub hinzugefügt wird, kann ein neues Gerät für jeden der IoT-Hubs bereitgestellt werden. Wenn Sie ein Gleichgewicht zwischen allen Geräten – registriert und nicht registriert – herstellen möchten, müssen Sie alle Geräte neu bereitstellen.

Führen Sie zum Festlegen der Zuordnungsrichtlinie folgende Schritte durch:

1. Klicken Sie zum Festlegen der Zuordnungsrichtlinie auf der Seite „Device Provisioning-Dienst“ auf **Zuordnungsrichtlinie verwalten**.
2. Legen Sie für die Zuordnungsrichtlinie **Gleichmäßig gewichtete Verteilung** fest.
3. Klicken Sie auf **Speichern**.

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>Verknüpfen der neuen IoT Hub-Instanz mit Device Provisioning Service

Verknüpfen Sie Device Provisioning Service und die IoT Hub-Instanz, damit Device Provisioning Service Geräte in diesem Hub registrieren kann.

1. Klicken Sie auf der Seite **Alle Ressourcen** auf die Device Provisioning Service-Instanz, die Sie zuvor erstellt haben.
2. Klicken Sie auf der Seite „Device Provisioning-Dienst“ auf **Verknüpfte IoT Hubs**.
3. Klicken Sie auf **Hinzufügen**.
4. Aktivieren Sie auf der Seite **Verknüpfung zu IoT Hub hinzufügen** die Optionsfelder, um anzugeben, ob sich der verknüpfte IoT Hub im aktuellen Abonnement oder in einem anderen Abonnement befindet. Wählen Sie dann im Feld **IoT Hub** den Namen von IoT Hub.
5. Klicken Sie auf **Speichern**.

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen eines zweiten Geräts für eine zweite IoT Hub-Instanz mit dem Azure-Portal 
> * Hinzufügen eines Registrierungslisteneintrags für das zweite Gerät
> * Festlegen der Device Provisioning Service-Zuordnungsrichtlinie zur **gleichmäßigen Verteilung**
> * Verknüpfen der neuen IoT Hub-Instanz mit Device Provisioning Service

## <a name="next-steps"></a>Nächste Schritte

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps]()
-->
