---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3fae7fc37b010a754b7fe1dd9b6e7b092ae534e8
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576861"
---
### <a name="iot-edge-for-linux-on-windows-installation"></a>Installation von IoT Edge für Linux unter Windows

Ein Windows-Gerät mit den folgenden Mindestanforderungen:

* Systemanforderungen
   * Windows 10¹/11 (Pro, Enterprise, IoT Enterprise)
   * Windows Server 2019¹/2022  
   <sub>¹ Windows 10 und Windows Server 2019, Build 17763 und höher, mit Installation aller aktuellen kumulativen Updates.</sub>

* Hardwareanforderungen
  * Mindestens erforderlicher freier Arbeitsspeicher: 1 GB
  * Mindestens erforderlicher freier Speicherplatz: 10 GB

* Virtualisierungsunterstützung
  * Aktivieren Sie unter Windows 10 Hyper-V. Weitere Informationen finden Sie unter [Installieren von Hyper-V unter Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
  * Installieren Sie unter Windows Server die Hyper-V-Rolle, und erstellen Sie einen standardmäßigen Netzwerkswitch. Weitere Informationen finden Sie unter [Geschachtelte Virtualisierung für Azure IoT Edge für Linux unter Windows](../articles/iot-edge/nested-virtualization.md).
  * Konfigurieren Sie auf einer VM die geschachtelte Virtualisierung. Weitere Informationen finden Sie unter [Geschachtelte Virtualisierung](../articles/iot-edge/nested-virtualization.md).

* Netzwerkunterstützung
  * Windows Server umfasst keinen Standardswitch. Bevor Sie EFLOW auf einem Windows Server-Gerät bereitstellen können, müssen Sie einen virtuellen Switch erstellen.  Weitere Informationen finden Sie unter [Erstellen eines virtuellen Switches](../articles/iot-edge/how-to-create-virtual-switch.md).
  * Windows Desktop-Versionen umfassen einen Standardswitch, der für die EFLOW-Installation verwendet werden kann. Bei Bedarf können Sie einen benutzerdefinierten virtuellen Switch erstellen.

> [!TIP]
> Wenn Sie **GPU-beschleunigte Linux-Module** in Ihrer Azure IoT Edge für Linux unter Windows-Bereitstellung verwenden möchten, gibt es mehrere Konfigurationsoptionen zu berücksichtigen.
>
> Abhängig von Ihrer GPU-Architektur müssen Sie die richtigen Treiber installieren, und Sie benötigen möglicherweise Zugriff auf einen Windows-Insider-Build. Informationen zum Ermitteln Ihrer Konfigurationsanforderungen und zum Erfüllen dieser Voraussetzungen finden Sie unter [GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows](../articles/iot-edge/gpu-acceleration.md).
>
> Nehmen Sie sich jetzt die Zeit, die Voraussetzungen für die GPU-Beschleunigung zu erfüllen. Sie müssen den Installationsvorgang neu starten, wenn Sie sich während der Installation für die Verwendung der GPU-Beschleunigung entscheiden.

Sie können Ihre IoT Edge-Geräte mit **PowerShell** oder dem **Windows Admin Center** verwalten. Bei jedem Hilfsprogramm gibt es eigene Voraussetzungen:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie PowerShell verwenden möchten, führen Sie die folgenden Schritte aus, um Ihr Zielgerät für die Installation von Azure IoT Edge für Linux unter Windows und die Bereitstellung des virtuellen Linux-Computers vorzubereiten:

1. Legen Sie die Ausführungsrichtlinie auf dem Zielgerät auf `AllSigned` fest. Sie können die aktuelle Ausführungsrichtlinie in einer PowerShell-Eingabeaufforderung mit erhöhten Rechten mit dem folgenden Befehl überprüfen:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Wenn die Ausführungsrichtlinie von `local machine` nicht `AllSigned` ist, können Sie die Ausführungsrichtlinie wie folgt festlegen:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Weitere Informationen zum PowerShell-Modul „Azure IoT Edge für Linux unter Windows“ finden Sie in der [PowerShell-Funktionsreferenz](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Wenn Sie Windows Admin Center verwenden möchten, führen Sie die folgenden Schritte zum Herunterladen und Installieren von Windows Admin Center sowie zum Installieren der Azure IoT Edge-Erweiterung für Windows Admin Center aus:

   1. Laden Sie das [Windows Admin Center-Installationsprogramm](https://aka.ms/wacdownload) herunter, und führen Sie es aus. Befolgen Sie die Anweisungen im Installations-Assistenten, um Windows Admin Center zu installieren.

   1. Verwenden Sie nach der Installation einen unterstützten Browser, um das Windows Admin Center zu öffnen. Zu den unterstützten Browsern gehören Microsoft Edge (Windows 10, Version 1709 oder höher), Google Chrome und Microsoft Edge Insider.

   1. Bei der ersten Verwendung von Windows Admin Center werden Sie aufgefordert, ein Zertifikat auszuwählen. Wählen Sie als Zertifikat **Windows Admin Center-Client** aus.

   1. Installieren Sie die Azure IoT Edge-Erweiterung. Wählen Sie das Zahnradsymbol in der rechten oberen Ecke des Windows Admin Center-Dashboards aus.

      ![Auswählen des Zahnradsymbols in der rechten oberen Ecke des Dashboards zum Öffnen der Einstellungen (PNG).](../articles/iot-edge/media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. Wählen Sie im Menü **Einstellungen** unter **Gateway** die Option **Erweiterungen** aus.

   1. Suchen Sie auf der Registerkarte **Verfügbare Erweiterungen** in der Liste der Erweiterungen nach **Azure IoT Edge**. Markieren Sie diese Option, und wählen Sie über der Liste der Erweiterungen die Aufforderung **Installieren** aus.

   1. Nach Abschluss der Installation sollte Azure IoT Edge in der Liste der installierten Erweiterungen auf der Registerkarte **Installierte Erweiterungen** angezeigt werden.

---
