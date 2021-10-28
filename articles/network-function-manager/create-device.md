---
title: 'Tutorial: Erstellen einer Geräteressource für Azure Network Function Manager'
description: In diesem Tutorial erfahren Sie, wie Sie eine Geräteressource für Azure Network Function Manager erstellen.
author: prmitt
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: prmitt
ms.openlocfilehash: 41347c5afe8664955fd5b298330f4299244edb6a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263323"
---
# <a name="tutorial-create-a-network-function-manager-device-resource-preview"></a>Tutorial: Erstellen einer Network Function Manager-Geräteressource (Vorschau)

In diesem Tutorial erstellen Sie eine **Geräteressource** für Azure Network Function Manager (NFM). Die Network Function Manager-Geräteressource ist mit der Azure Stack Edge-Ressource verknüpft. Die Geräteressource vereint alle auf dem Azure Stack Edge-Gerät bereitgestellten Netzwerkfunktionen und umfasst allgemeine Dienste für Bereitstellung, Überwachung und Problembehandlung sowie einheitliche Verwaltungsvorgänge für alle in Azure Stack Edge bereitgestellten Netzwerkfunktionen. Sie müssen zunächst die Network Function Manager-Geräteressource erstellen, damit Sie Netzwerkfunktionen auf dem Azure Stack Edge-Gerät bereitstellen können.

In diesem Tutorial:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen
> * Erstellen einer Geräteressource
> * Abrufen eines Registrierungsschlüssels
> * Registrieren des Geräts

## <a name="prerequisites"></a><a name="pre"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind:

* Sie haben alle Voraussetzungen erfüllt, die im Artikel [Übersicht](overview.md#prereq) aufgeführt sind.
* Ihnen wurden die erforderlichen Berechtigungen zugewiesen. Weitere Informationen finden Sie unter [Registrierung und Berechtigungen von Ressourcenanbietern](overview.md#permissions).
* Lesen Sie den Abschnitt  [Regionale Verfügbarkeit](overview.md#regions) , bevor Sie eine Geräteressource erstellen.
* Überprüfen Sie, ob Sie von einem Windows-Client eine Remoteverbindung mit dem Azure Stack Edge Pro-GPU-Gerät über PowerShell herstellen können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="create-a-device-resource"></a><a name="create"></a>Erstellen einer Geräteressource

Wenn bereits eine Azure Network Function Manager-Geräteressource vorhanden ist, müssen Sie keine erstellen. Sie können diesen Abschnitt überspringen und mit dem Abschnitt [Registrierungsschlüssel](#key) fortfahren.

Führen Sie zum Erstellen einer **Geräteressource** die folgenden Schritte aus.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen beim Azure-[Vorschauportal](https://aka.ms/AzureNetworkFunctionManager) an.

1. Konfigurieren Sie auf der Registerkarte **Grundlagen** unter **Details zum Projekt** und **Details zur Instanz** die Geräteeinstellungen.
   :::image type="content" source="./media/create-device/device-settings.png" alt-text="Screenshot der Geräteeinstellungen":::

   Beim Ausfüllen der Felder wird ein grünes Häkchen angezeigt, nachdem die hinzugefügten Zeichen überprüft wurden. Einige Details werden automatisch ausgefüllt, bei anderen handelt es sich um anpassbare Felder:

   * **Abonnement:** Vergewissern Sie sich, dass das richtige Abonnement angegeben ist. Das Abonnement kann über die Dropdownliste geändert werden.
   * **Ressourcengruppe:** Wählen Sie eine vorhandene Ressourcengruppe aus, oder klicken Sie auf **Neu erstellen**, um eine zu erstellen.
   * **Region:** Wählen Sie den Standort für das Gerät aus. Dabei muss es sich um eine unterstützte Region handeln. Weitere Informationen finden Sie unter [Regionale Verfügbarkeit](overview.md#regions).
   * **Name:** Geben Sie den Namen für das Gerät ein.
   * **Azure Stack Edge:** Wählen Sie die Azure Stack Edge-Ressource aus, die Sie als Gerät für Azure Network Function Manager registrieren möchten. Die ASE-Ressource muss den Status **Online** haben, damit erfolgreich eine Geräteressource erstellt werden kann. Den Status der ASE-Ressource können Sie im Bereich **Eigenschaften** auf der Seite der Azure Stack Edge-Ressource überprüfen.
1. Wählen Sie zum Überprüfen der Geräteeinstellungen die Option **Überprüfen und erstellen** aus.
1. Wählen Sie nach dem Überprüfen aller Einstellungen die Option **Erstellen** aus.
   
   >[!NOTE]
   >Die Network Function Manager-Geräteressource kann nur mit einer Azure Stack Edge-Ressource verknüpft werden. Für jede Azure Stack Edge-Ressource muss eine separate NFM-Geräteressource erstellt werden.
   >

## <a name="get-the-registration-key"></a><a name="key"></a>Abrufen des Registrierungsschlüssels

1. Navigieren Sie nach der erfolgreichen Bereitstellung des Geräts zu der Ressourcengruppe, in der die Geräteressource bereitgestellt wird.
1. Klicken Sie auf die **Geräteressource**. Klicken Sie zum Abrufen des Registrierungsschlüssels auf **Registrierungsschlüssel abrufen**. Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen zum Generieren eines Registrierungsschlüssels verfügen. Weitere Informationen finden Sie unter [Berechtigungen](overview.md#permissions).

   :::image type="content" source="./media/create-device/register-device.png" alt-text="Screenshot des Registrierungsschlüssels" lightbox="./media/create-device/register-device.png":::
1. Notieren Sie sich den Registrierungsschlüssel des Geräts, der in den nächsten Schritten benötigt wird.

   > [!IMPORTANT]
   > Der Registrierungsschlüssel läuft 24 Stunden nach seiner Generierung ab. Wenn der Registrierungsschlüssel nicht mehr aktiv ist, können Sie durch Wiederholen der Schritte in diesem Abschnitt einen neuen Registrierungsschlüssel generieren.
   >

## <a name="register-the-device"></a><a name="registration"></a>Registrieren des Geräts

Führen Sie die folgenden Schritte aus, um die Geräteressource bei Azure Stack Edge zu registrieren.

1. Um das Gerät mit dem im vorherigen Schritt abgerufenen Registrierungsschlüssel zu registrieren, [stellen Sie über Windows PowerShell eine Verbindung mit dem Azure Stack Edge-Gerät her](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Nachdem eine PowerShell-Verbindung (minishell-Verbindung) mit dem Gerät hergestellt wurde, geben Sie den folgenden Befehl mit dem im vorherigen Schritt abgerufenen Registrierungsschlüssel des Geräts als Parameter ein:
   ```powershell
   Invoke-MecRegister <device-registration-key>
   ```

1. Vergewissern Sie sich, dass für die Geräteressource der Gerätestatus **Registriert** festgelegt ist.

   :::image type="content" source="./media/create-device/device-registered.png" alt-text="Screenshot des registrierten Geräts" lightbox="./media/create-device/device-registered.png":::
 
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen einer Netzwerkfunktion](deploy-functions.md)
