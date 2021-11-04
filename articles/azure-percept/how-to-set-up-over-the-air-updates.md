---
title: Einrichten von Azure IoT Hub für die Bereitstellung von OTA-Updates
description: Hie erfahren Sie, wie Sie Azure IoT Hub für die Bereitstellung von OTA-Updates (Over the Air; per Funk) für Azure Percept DK konfigurieren.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 19161ea1e89cf4917442e607903a7c8f0b7c2860
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006295"
---
# <a name="set-up-azure-iot-hub-to-deploy-over-the-air-updates"></a>Einrichten von Azure IoT Hub für die Bereitstellung von OTA-Updates

Nutzen Sie Over-the-Air-Updates, um Ihr Azure Percept SK-Gerät zu schützen und auf dem neuesten Stand zu halten. Sie können Ihre Azure-Umgebung in wenigen einfachen Schritten mit Device Update für IoT Hub einrichten und die neuesten Updates für Ihr Azure Percept DK-Gerät bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure Percept DK (DevKit)
- [Azure-Abonnement](https://azure.microsoft.com/free/)
- [Azure Percept DK-Setup:](./quickstart-percept-dk-set-up.md) Sie haben Ihr DevKit mit einem WLAN verbunden, eine IoT Hub-Instanz erstellt und das DevKit mit der IoT Hub-Instanz verbunden.

## <a name="create-a-device-update-account"></a>Erstellen eines Device Update-Kontos

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich mit dem Azure-Konto an, das Sie für Azure Percept verwenden.

1. Geben Sie in der Suchleiste am oberen Rand der Seite **Geräte-Update für IoT Hubs** ein.

1. Wählen Sie **Geräte-Update für IoT Hubs** aus, wenn diese Option in der Suchleiste angezeigt wird.

1. Wählen Sie im oberen linken Bereich der Seite die Schaltfläche **+ Hinzufügen** aus.

1. Wählen Sie das **Azure-Abonnement** und die **Ressourcengruppe** aus, die dem Azure Percept-Gerät und seinem IoT Hub zugeordnet sind.

1. Geben Sie einen **Namen** und einen **Speicherort** für Ihr Device Update-Konto an.

1. Aktivieren Sie das Kontrollkästchen **Geräteupdate-Administratorenrolle zuweisen.** 

1. Überprüfen Sie die Angaben, und wählen Sie **Überprüfen und erstellen** aus.

1. Wählen Sie die Schaltfläche **Erstellen**.

1. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.

## <a name="create-a-device-update-instance"></a>Erstellen einer Device Update-Instanz

1. Klicken Sie in Ihrer Ressource vom Typ „Device Update für IoT Hub“ unter **Instanzverwaltung** auf **Instanzen**.

1. Klicken Sie auf **+ Erstellen**, geben Sie einen Instanznamen an, und wählen Sie die IoT Hub-Instanz aus, die Ihrem Azure Percept-Gerät zugeordnet ist. Dies kann einige Minuten in Anspruch nehmen.

1. Klicken Sie auf **Erstellen**.

## <a name="configure-iot-hub"></a>Konfigurieren von IoT Hub

1. Warten Sie in der Instanzverwaltung auf der Seite **Instanzen**, bis die Device Update-Instanz den Status **Erfolgreich** hat. Klicken Sie auf das Symbol **Aktualisieren**, um den Status zu aktualisieren.

1. Wählen Sie die für Sie erstellte Instanz aus, und klicken Sie auf **IoT Hub konfigurieren**. Wählen Sie im linken Bereich **Ich bin mit der Durchführung dieser Änderungen einverstanden.** aus, und klicken Sie auf **Aktualisieren**.

1. Warten Sie, bis der Prozess erfolgreich beendet wurde.

## <a name="configure-access-control-roles"></a>Konfigurieren von Zugriffssteuerungsrollen

Der letzte Schritt ermöglicht es Ihnen, Benutzern Berechtigungen zum Veröffentlichen und Bereitstellen von Updates zu erteilen.

1. Klicken Sie in Ihrer Ressource vom Typ „Device Update für IoT Hub“ auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf **+Hinzufügen**, und wählen Sie anschließend **Rollenzuweisung hinzufügen** aus.

1. Wählen Sie unter **Rolle** die Option **Administrator für Geräteupdates** aus. Wählen Sie unter **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus. Wählen Sie unter **Auswählen** Ihr Konto oder das Konto der Person aus, von der Updates bereitgestellt werden. Klicken Sie auf **Speichern**.

> [!TIP]
> Wenn Sie mehreren Personen in Ihrer Organisation Zugriff gewähren möchten, können Sie diesen Schritt wiederholen und jeden der gewünschten Benutzer als **Administrator für Geräteupdates** festlegen.

## <a name="next-steps"></a>Nächste Schritte

Nun ist alles bereit, und Sie können mit Device Update für IoT Hub [Over-the-Air-Updates für Ihr Azure Percept Development Kit durchführen](./how-to-update-over-the-air.md).
