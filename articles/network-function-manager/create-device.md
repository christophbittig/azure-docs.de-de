---
title: 'Schnellstart: Erstellen einer Geräteressource für Azure Network Function Manager'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Geräteressource für Azure Network Function Manager erstellen.
author: prmitt
ms.service: network-function-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2ab440166ba7e5b27f07880c5ac0cb7fef27466a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070247"
---
# <a name="quickstart-create-a-network-function-manager-device-resource"></a>Schnellstart: Erstellen einer Network Function Manager-Geräteressource

In diesem Schnellstart erstellen Sie eine **Geräteressource** für Azure Network Function Manager (NFM). Die Network Function Manager-Geräteressource ist mit der Azure Stack Edge-Ressource verknüpft. Die Geräteressource vereint alle auf dem Azure Stack Edge-Gerät bereitgestellten Netzwerkfunktionen und umfasst allgemeine Dienste für Bereitstellung, Überwachung und Problembehandlung sowie einheitliche Verwaltungsvorgänge für alle in Azure Stack Edge bereitgestellten Netzwerkfunktionen. Sie müssen zunächst die Network Function Manager-Geräteressource erstellen, damit Sie Netzwerkfunktionen auf dem Azure Stack Edge-Gerät bereitstellen können.

## <a name="prerequisites"></a><a name="pre"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind:

* Vergewissern Sie sich, dass Sie über ein Azure-Konto mit einem aktiven Abonnement verfügen. Wenn Sie ein Konto benötigen, [erstellen Sie kostenlos ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Sie haben alle Voraussetzungen erfüllt, die im Artikel [Voraussetzungen und Anforderungen](requirements.md) aufgeführt werden.
* Ihnen wurden die erforderlichen Berechtigungen zugewiesen. Weitere Informationen finden Sie unter [Registrierung und Berechtigungen von Ressourcenanbietern](resources-permissions.md).
* Lesen Sie den Abschnitt  [Regionale Verfügbarkeit](overview.md#regions) , bevor Sie eine Geräteressource erstellen.
* Überprüfen Sie, ob Sie von einem Windows-Client eine Remoteverbindung mit dem Azure Stack Edge Pro-GPU-Gerät über PowerShell herstellen können. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="create-a-device-resource"></a><a name="create"></a>Erstellen einer Geräteressource

Wenn bereits eine Azure Network Function Manager-Geräteressource vorhanden ist, müssen Sie keine erstellen. Sie können diesen Abschnitt überspringen und mit dem Abschnitt [Registrierungsschlüssel](#key) fortfahren.

Führen Sie zum Erstellen einer **Geräteressource** die folgenden Schritte aus.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen am [Azure-Portal](https://portal.azure.com) an.

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
1. Klicken Sie auf die **Geräteressource**. Klicken Sie zum Abrufen des Registrierungsschlüssels auf **Registrierungsschlüssel abrufen**. Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen zum Generieren eines Registrierungsschlüssels verfügen. Weitere Informationen finden Sie im Artikel [Registrierung und Berechtigungen von Ressourcenanbietern](resources-permissions.md).

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
