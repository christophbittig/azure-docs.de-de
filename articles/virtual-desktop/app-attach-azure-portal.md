---
title: Einrichten des MSIX-Features zum Anfügen von Apps für Azure Virtual Desktop im Portal – Azure
description: Einrichten des MSIX-Features zum Anfügen von Apps für Azure Virtual Desktop über das Azure-Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ec462d04402f20d801c43cebd7f4561eb6177f1c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707119"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Einrichten des MSIX-Features zum Anfügen von Apps mit dem Azure-Portal

In diesem Artikel wird erläutert, wie Sie das MSIX-Feature zum Anfügen von Apps in einer Azure Virtual Desktop-Umgebung einrichten.

## <a name="requirements"></a>Requirements (Anforderungen)

Folgendes benötigen Sie, um das MSIX-Feature zum Anfügen von Apps zu konfigurieren:

- Eine funktionierende Azure Virtual Desktop-Bereitstellung. Informationen zur Bereitstellung von Azure Virtual Desktop (klassisch) finden Sie unter [Erstellen eines Mandanten in Azure Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Informationen zum Bereitstellen von Azure Virtual Desktop mit Azure Resource Manager-Integration finden Sie unter [Erstellen eines Hostpools mit dem Azure-Portal](./create-host-pools-azure-marketplace.md).
- Ein Azure Virtual Desktop-Hostpool mit mindestens einem aktiven Sitzungshost.
- Das MSIX-Pakettool.
- Eine MSIX-gepackte Anwendung, die in ein MSIX-Image erweitert wurde, das in eine Dateifreigabe hochgeladen wird.
- Eine Dateifreigabe in der Azure Virtual Desktop-Bereitstellung, in der das MSIX-Paket gespeichert wird.
- Die Dateifreigabe, in die Sie das MSIX-Image hochgeladen haben, muss auch für alle virtuellen Computer (VMs) im Hostpool zugänglich sein. Benutzer benötigen Leseberechtigungen, um auf das Image zugreifen zu können.
- Wenn das Zertifikat nicht öffentlich vertrauenswürdig ist, befolgen Sie die Anweisungen unter [Installieren von Zertifikaten](app-attach.md#install-certificates).

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Deaktivieren von automatischen Updates für Anwendungen mit dem MSIX-Feature zum Anfügen von Apps

Bevor Sie beginnen, müssen Sie die automatischen Updates für Anwendungen mit dem MSIX-Feature zum Anfügen von Apps deaktivieren. Um automatische Updates zu deaktivieren, müssen Sie an einer Eingabeaufforderung mit erhöhten Rechten die folgenden Befehle ausführen:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

## <a name="configure-the-msix-app-attach-management-interface"></a>Konfigurieren der Verwaltungsschnittstelle für das MSIX-Feature zum Anfügen von Apps

Als nächstes müssen Sie die Verwaltungsschnittstelle für das MSIX-Feature zum Anfügen von Apps für das Azure-Portal herunterladen und konfigurieren.

So richten Sie die Verwaltungsschnittstelle ein

1. [Öffnen Sie das Azure-Portal](https://portal.azure.com).
2. Wenn Sie eine Eingabeaufforderung erhalten, ob Sie die Erweiterung für vertrauenswürdig halten, wählen Sie **Zulassen** aus.

      > [!div class="mx-imgBorder"]
      > ![Ein Screenshot des Fensters für nicht vertrauenswürdige Erweiterungen. „Zulassen“ ist rot hervorgehoben.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Hinzufügen eines MSIX-Images zum Hostpool

Als nächstes müssen Sie das MSIX-Image Ihrem Hostpool hinzufügen.

So fügen Sie das MSIX-Image hinzu

1. Öffnen Sie das Azure-Portal.

2. Geben Sie **Azure Virtual Desktop** in die Suchleiste ein, und wählen Sie dann den Dienstnamen aus.

3. Wählen Sie den Hostpool aus, in dem Sie die MSIX-Apps ablegen möchten.

4. Wählen Sie **MSIX-Pakete** aus, um das Datenraster mit allen **MSIX-Paketen** zu öffnen, die derzeit dem Hostpool hinzugefügt sind.

5. Wählen Sie **+ Hinzufügen** aus, um die Registerkarte **MSIX-Paket hinzufügen** zu öffnen.

6. Geben Sie auf der Registerkarte **MSIX-Paket hinzufügen** die folgenden Werte ein:

      - Geben Sie für **MSIX-Imagepfad** einen gültigen UNC-Pfad ein, der auf das MSIX-Image auf der Dateifreigabe verweist. (Beispiel: `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd`.) Wenn Sie fertig sind, wählen Sie **Hinzufügen** aus, um den MSIX-Container abzufragen und die Gültigkeit des Pfads zu prüfen.

      - Wählen Sie für **MSIX-Paket** den entsprechenden MSIX-Paketnamen in der Dropdownliste aus. Dieses Menü wird nur ausgefüllt, wenn Sie einen gültigen Imagepfad in **MSIX-Imagepfad** eingegeben haben.

      - Stellen Sie für **Paketanwendungen** sicher, dass die Liste alle MSIX-Anwendungen enthält, die Sie den Benutzern in Ihrem MSIX-Paket zur Verfügung stellen möchten.

      - Geben Sie optional einen **Anzeigenamen** ein, wenn Sie für Ihr Paket eine benutzerfreundlichere Darstellung in den Benutzerbereitstellungen wünschen.

      - Stellen Sie sicher, dass **Version** die richtige Versionsnummer enthält.

      - Wählen Sie den **Registrierungstyp** aus, den Sie verwenden möchten. Welchen Sie verwenden, hängt von Ihren Anforderungen ab:

    - **Bedarfsgesteuerte Registrierung** verschiebt die vollständige Registrierung der MSIX-Anwendung, bis der Benutzer die Anwendung startet. Wir empfehlen Ihnen, diesen Registrierungstyp zu verwenden.

    - **Anmeldesperre** führt die Registrierung nur durch, während sich der Benutzer anmeldet. Dieser Typ wird nicht empfohlen, da er zu längeren Anmeldezeiten für Benutzer führen kann.

7.  Wählen Sie für **Status** Ihren bevorzugten Status aus.
    -  Der Status **Aktiv** ermöglicht Benutzern die Interaktion mit dem Paket.
    -  Der Status **Inaktiv** veranlasst Azure Virtual Desktop, das Paket zu ignorieren und nicht den Benutzern zuzustellen.

8. Wenn Sie fertig sind, wählen Sie **Hinzufügen** aus.

## <a name="publish-msix-apps-to-an-app-group"></a>Veröffentlichen von MSIX-Apps in einer App-Gruppe

Als nächstes müssen Sie die Apps im Paket veröffentlichen. Dies ist sowohl für Desktop- als auch für Remote-App-Anwendungsgruppen erforderlich.

Wenn Sie bereits über ein MSIX-Image verfügen, fahren Sie mit [Veröffentlichen von MSIX-Apps in einer App-Gruppe](#publish-msix-apps-to-an-app-group) fort. Wenn Sie Legacyanwendungen testen möchten, befolgen Sie die Anweisungen unter [Erstellen eines MSIX-Pakets aus einem Desktopinstaller auf einem virtuellen Computer](/windows/msix/packaging-tool/create-app-package-msi-vm/), um die Legacyanwendung in ein MSIX-Paket zu konvertieren.

So veröffentlichen Sie die Apps

1. Wählen Sie im Azure Virtual Desktop-Ressourcenanbieter die Registerkarte **Anwendungsgruppen** aus.

2. Wählen Sie die Anwendungsgruppe aus, in der Sie die Apps veröffentlichen möchten.

   >[!NOTE]
   >MSIX-Anwendungen können mit dem MSIX-Feature zum Anfügen von Apps sowohl an Remote- als auch an Desktopanwendungsgruppen geliefert werden.

3. Sobald Sie sich in der App-Gruppe befinden, wählen Sie die Registerkarte **Anwendungen** aus. Im Raster **Anwendungen** werden alle vorhandenen Anwendungen innerhalb der App-Gruppe angezeigt.

4. Wählen Sie **+ Hinzufügen** aus, um die Registerkarte **Anwendung hinzufügen** zu öffnen.

      > [!div class="mx-imgBorder"]
      > ![Der Screenshot zeigt, wie der Benutzer „+ Hinzufügen“ auswählt, um die Registerkarte „Anwendung hinzufügen“ zu öffnen.](media/select-add.png)

5. Wählen Sie für **Anwendungsquelle** die Quelle für Ihre Anwendung aus.
    - Wenn Sie eine Desktopanwendungsgruppe verwenden, wählen Sie **MSIX-Paket** aus.
      
      > [!div class="mx-imgBorder"]
      > ![Der Screenshot eines Kunden, der das MSIX-Paket aus dem Dropdownmenü für die Anwendungsquelle auswählt. Das MSIX-Paket ist rot hervorgehoben.](media/select-source.png)
    
    - Wenn Sie eine Remoteanwendungsgruppe verwenden, wählen Sie eine der folgenden Optionen aus:
        
        - Startmenü
        - App-Pfad
        - MSIX-Paket

    - Geben Sie für **Anwendungsname** einen beschreibenden Namen für die Anwendung ein.

    Sie können auch die folgenden optionalen Features konfigurieren:
   
    - Geben Sie für **Anzeigename** einen neuen Namen für das Paket ein, der Ihren Benutzer angezeigt wird.

    - Geben Sie für **Beschreibung** eine kurze Beschreibung des App-Pakets ein.

    - Wenn Sie eine Remoteanwendungsgruppe verwenden, können Sie auch die folgenden Optionen konfigurieren:

        - **Symbolpfad**
        - **Symbolindex**
        - **Im Webfeed anzeigen**

6. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

>[!NOTE]
>Wenn ein Benutzer einer Remoteanwendungsgruppe und einer Desktopanwendungsgruppe aus demselben Hostpool zugeordnet ist, wird die Desktopanwendungsgruppe im Feed angezeigt.

## <a name="assign-a-user-to-an-app-group"></a>Zuweisen eines Benutzers zu einer App-Gruppe

Nachdem Sie MSIX-Apps einer App-Gruppe zugewiesen haben, müssen Sie Benutzern den Zugriff auf diese Apps gewähren. Sie können den Zugriff zuweisen, indem Sie Benutzer oder Benutzergruppen zu einer App-Gruppe mit veröffentlichten MSIX-Anwendungen hinzufügen. Folgen Sie den Anweisungen in [Verwalten von App-Gruppen mit dem Azure-Portal](manage-app-groups.md), um Ihre Benutzer einer App-Gruppe zuzuweisen.

>[!NOTE]
>Das MSIX-Feature zum Anfügen von Apps für Remote-Apps verschwindet möglicherweise aus dem Feed, wenn Sie Remote-Apps während der öffentlichen Vorschau testen. Die Apps werden nicht angezeigt, da der Hostpool, den Sie in der Testumgebung verwenden, von einem RD-Broker in der Produktionsumgebung versorgt wird. Da der RD-Broker in der Produktionsumgebung das Vorhandensein des MSIX-Features zum Anfügen von Apps für Remote-Apps nicht registriert, werden die Apps nicht im Feed angezeigt.

## <a name="change-msix-package-state"></a>Ändern des MSIX-Paketstatus

Als nächstes müssen Sie den MSIX-Paketstatus je nach Vorgehensweise mit dem Paket entweder in **Aktiv** oder **Inaktiv** ändern. Aktive Pakete sind Pakete, mit denen Ihre Benutzer interagieren können, sobald sie veröffentlicht sind. Inaktive Pakete werden von Azure Virtual Desktop ignoriert, sodass Ihre Benutzer nicht mit den darin enthaltenen Apps interagieren können.

### <a name="change-state-with-the-applications-list"></a>Ändern des Status mit der Anwendungsliste

So ändern Sie den Paketstatus mit der Anwendungsliste

1. Wechseln Sie zu Ihrem Hostpool, und wählen Sie **MSIX-Pakete** aus. Es sollte eine Liste aller vorhandenen MSIX-Pakete innerhalb des Hostpools angezeigt werden.

2. Wählen Sie die MSIX-Pakete aus, deren Status Sie ändern möchten, und wählen Sie dann **Status ändern** aus.

### <a name="change-state-with-update-package"></a>Ändern des Status mit einem Updatepaket

So ändern Sie den Paketstatus mit einem Updatepaket

1. Wechseln Sie zu Ihrem Hostpool, und wählen Sie **MSIX-Pakete** aus. Es sollte eine Liste aller vorhandenen MSIX-Pakete innerhalb des Hostpools angezeigt werden.

2. Wählen Sie in der MSIX-Paketliste den Namen des Pakets aus, dessen Status Sie ändern möchten. Dadurch wird die Registerkarte **Updatepaket** geöffnet.

3. Schalten Sie den Schalter **Status** entweder auf **Inaktiv** oder **Aktiv** um, und wählen Sie dann **Speichern** aus.

## <a name="change-msix-package-registration-type"></a>Ändern des MSIX-Paketregistrierungstyps

So ändern Sie den Registrierungstyp des Pakets

1. Wählen Sie **MSIX-Pakete** aus. Es sollte eine Liste aller vorhandenen MSIX-Pakete innerhalb des Hostpools angezeigt werden.

2. Wählen Sie **Paketname** im Raster **MSIX-Pakete** aus, um das Blatt zur Aktualisierung des Pakets zu öffnen.

3. Schalten Sie den **Registrierungstyp** über die Schaltfläche **Bedarfsgesteuert/Anmeldesperre** wie gewünscht um, und wählen Sie **Speichern** aus.

## <a name="remove-an-msix-package"></a>Entfernen eines MSIX-Pakets

So entfernen Sie ein MSIX-Paket aus Ihrem Hostpool

1. Wählen Sie **MSIX-Pakete** aus.  Es sollte eine Liste aller vorhandenen MSIX-Pakete innerhalb des Hostpools angezeigt werden.

2. Wählen Sie die Auslassungspunkte auf der rechten Seite des Namens des zu entfernenden Pakets und dann die Option **Entfernen** aus.

## <a name="remove-msix-apps"></a>Entfernen von MSIX-Apps

So entfernen Sie einzelne MSIX-Apps aus dem Paket

1. Wechseln Sie zum Hostpool, und wählen Sie **Anwendungsgruppen** aus.

2. Wählen Sie die Anwendungsgruppe aus, aus der Sie MSIX-Apps entfernen möchten.

3. Öffnen Sie die Registerkarte **Anwendungen**.

4. Wählen Sie die zu entfernende App und dann **Entfernen** aus.

## <a name="next-steps"></a>Nächste Schritte

Stellen Sie unserer [Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) Fragen zu dieser Funktion.

Sie können auch Feedback zu Azure Virtual Desktop im [Azure Virtual Desktop-Feedback-Hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) bereitstellen.

Folgende Artikel sind eventuell hilfreich:

- [Glossar des MSIX-Features zum Anfügen von Apps](app-attach-glossary.md)
- [Häufig gestellte Fragen zum MSIX-Feature zum Anfügen von Apps](app-attach-faq.yml)
