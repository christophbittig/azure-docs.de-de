---
title: Prüfliste vor der Zertifizierung für IoT Edge-Modulangebote auf dem Azure Marketplace
description: Erfahren Sie mehr über die spezifischen Zertifizierungsanforderungen für die Veröffentlichung von IoT Edge-Modulangeboten auf dem Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 1f9ad2fd05e11d981dd374919e2cc43d1b8d1f97
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232307"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Prüfliste vor der Zertifizierung für IoT Edge-Module

> [!NOTE]
> Herausgebern wird dringend empfohlen, vor der Übermittlung zur Zertifizierung diese Prüfliste durchzuarbeiten und die Modulfunktionalität zu überprüfen. Dadurch wird der Zertifizierungsprozess beschleunigt, da weniger Änderungen und erneuten Übermittlungen erforderlich sind.

## <a name="validation-of-image"></a>Überprüfung des Images

Sobald das Edge-Modulimage zur Übermittlung bereit ist, führen Sie die folgenden Schritte aus, um sicherzustellen, dass das Image auf die von Microsoft erwartete Weise funktioniert.

### <a name="steps-to-perform-in-the-azure-portal"></a>Im Azure-Portal auszuführende Schritte

1. Öffnen Sie das [Azure-Portal](https://partner.microsoft.com/).
1. Erstellen Sie eine Ressourcengruppe.
1. Erstellen Sie einen IoT Hub.
1. Erstellen Sie ein IoT Edge-Gerät.
1. Kopieren Sie die Verbindungszeichenfolge, und speichern Sie sie im Editor.
1. Wählen Sie die festgelegten **Module auf dem erstellten Edge-Gerät** aus.
1. Fügen Sie die Details der ACR hinzu, in der sich die neueste Version des Images befindet.
1. Wählen Sie **IoT Edge-Modul hinzufügen** aus, und geben Sie Folgendes an:
    - Image-URI in der Moduleinstellung
    - Umgebungsvariable (die auch im Partner Center hinzugefügt wird)
    - Optionen für die Containererstellung (die auch im Partner Center hinzugefügt werden)
    - Einstellung für den Modulzwilling (die auch im Partner Center hinzugefügt wird)
1. Fügen Sie Routen hinzu (die auch im Partner Center hinzugefügt werden).
1. Klicken Sie auf **Überprüfen + erstellen**.

Edge-Module werden auf dem in Azure erstellten Edge-Gerät bereitgestellt.

### <a name="steps-to-perform-on-the-device"></a>Auf dem Gerät auszuführende Schritte

#### <a name="device-details"></a>Gerätedetails

Das Zertifizierungsteam verwendet die folgende Hardware zum Überprüfen von Images auf verschiedenen Architekturen:

- Bei X64-Images wird eine Azure-VM mit einer Konfigurationsgröße gemäß Standard D2s v3 verwendet, auf der Ubuntu Server 18.04/Ubuntu Server 16.04 ausgeführt wird.
- Bei Azure Resource Manager 32-Images wird ein Raspberry Pi 3 Model B verwendet.
- Bei Azure Resource Manager 64-Images wird ein NVIDIA Jetson Nano mit 4 GB verwendet.

#### <a name="steps"></a>Schritte

1. Stellen Sie sicher, dass über Putty auf erstelle Geräte/VM zugegriffen werden kann.
1. Laden Sie die [IoT Edge-Runtime](../iot-edge/how-to-provision-single-device-linux-symmetric.md) auf das Gerät herunter.
1. Aktualisieren Sie die in Schritt 5 kopierte Verbindungszeichenfolge in der Datei „config.yaml“.
1. Starten Sie das Edge-Modul mit `sudo systemctl restart iotedge` neu.
1. Überprüfen Sie, ob das Modul auf dem Gerät mit `sudo iotedge list` bereitgestellt wird. Es sollte sich im Ausführungsstatus befinden.
1. Stellen Sie sicher, dass die Protokolle des mit `sudo iotedge logs “Module Name“ -f` bereitgestellten Moduls keine Fehler enthalten. Falls es bekannte Fehler gibt, beschreiben Sie diese in den **Hinweisen für den Prüfer** im Partner Center, bevor Sie das Angebot übermitteln.

## <a name="metadata-validation"></a>Metadatenüberprüfung

Überprüfen Sie Folgendes:

- Das neueste Tag ist sowohl im Partner Center als auch der Azure Container Registry aufgeführt.
- Die Mindestanforderungen an die Hardware sind der Angebotsbeschreibung hinzugefügt.
- Der Benutzername und das Kennwort der Azure Container Registry sind aktualisiert und im Partner Center hinzugefügt.
- Die Richtigkeit der gewünschten **Zwillingseigenschaft** (*falls zutreffend*).
- Die Richtigkeit der gewünschten **Umgebungsvariablen** (*falls zutreffend*).
- Die Richtigkeit der gewünschten **Erstellungsoptionen** (*falls zutreffend*).
- Die Verbindungszeichenfolge für die Leadverwaltung ist vorhanden.
- Die Datenschutzrichtlinie ist vorhanden.
- Die Nutzungsbedingungen sind vorhanden.
- IoT Edge-Gerätelink wurde vom [Azure IoT-Gerätekatalog](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) hinzugefügt 

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Modulen aus dem kommerziellen Marketplace](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [Veröffentlichen des Edge-Moduls im Partner Center](./iot-edge-offer-setup.md)
- [Bereitstellen eines IoT Edge-Moduls](../iot-edge/quickstart-linux.md)