---
title: Anpassen von RDP-Eigenschaften mit PowerShell (Azure)
description: Vorgehensweise zum Anpassen von RDP-Eigenschaften für Azure Virtual Desktop mit PowerShell-Cmdlets
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 282044d0ee3d07ae4eaa2c63d8d0bcebae0251aa
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544487"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Anpassen der RDP-Eigenschaften (Remotedesktopprotokoll) für einen Hostpool

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).

Durch Anpassen von RDP-Eigenschaften (Remotedesktopprotokoll) eines Hostpools, z.B. Unterstützung mehrerer Monitore und Audioumleitung, können Sie für Benutzer eine optimale Bedienumgebung bereitstellen, die deren Anforderungen entspricht. Wenn Sie die Standardeigenschaften der RDP-Datei ändern möchten, können Sie die RDP-Eigenschaften in Azure Virtual Desktop anpassen, indem Sie entweder das Azure-Portal oder den Parameter *-CustomRdpProperty* im Cmdlet **Update-AzWvdHostPool** verwenden.

Eine vollständige Liste der unterstützten Eigenschaften samt deren Standardwerten finden Sie unter [Unterstützte RDP-Dateieinstellungen](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="default-rdp-file-properties"></a>Standardeigenschaften für RDP-Dateien

RDP-Dateien haben standardmäßig die folgenden Eigenschaften:

|RDP-Eigenschaft|Sowohl für Desktop als auch für RemoteApp|
|---|---|
|Mehrfachmonitor-Modus|Aktiviert|
|Aktivierte Laufwerksumleitungen|Laufwerke, Zwischenablage, Drucker, COM-Anschlüsse, Smartcards, Geräte und USB-Geräte-Store|
|Remoteaudio-Modus|Lokale Wiedergabe|
|VideoPlayback|Aktiviert|
|EnableCredssp|Aktiviert|

>[!NOTE]
>- Der Modus mit mehreren Monitoren wird nur für Desktop-App-Gruppen aktiviert und für RemoteApp-App-Gruppen ignoriert.
>- Alle Standardeigenschaften von RDP-Dateien werden im Azure-Portal verfügbar gemacht.
>- Standardmäßig ist das CustomRdpProperty-Feld im Azure-Portal auf Null festgelegt. Wenn das CustomRdpProperty-Feld auf Null festgelegt ist, werden alle standardmäßigen RDP-Eigenschaften auf Ihren Hostpool angewendet. Wenn das CustomRdpProperty-Feld leer ist, werden keine standardmäßigen RDP-Eigenschaften auf Ihren Hostpool angewendet.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, befolgen Sie die Anweisungen unter [Einrichten des Azure Virtual Desktop PowerShell-Moduls](powershell-module.md), um Ihr PowerShell-Modul einzurichten und sich bei Azure anzumelden.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Konfigurieren von RDP-Eigenschaften im Azure-Portal

So konfigurieren Sie RDP-Eigenschaften im Azure-Portal:

1. Melden Sie sich unter <https://portal.azure.com> bei Azure an.
2. Geben Sie **Azure Virtual Desktop** in die Suchleiste ein.
3. Wählen Sie unter „Dienste“ die Option **Azure Virtual Desktop** aus.
4. Wählen Sie auf der Seite „Azure Virtual Desktop“ im Menü auf der linken Seite **Hostpools** aus.
5. Wählen Sie **den Namen des Hostpools** aus, den Sie aktualisieren möchten.
6. Wählen Sie im Menü auf der linken Seite des Bildschirms **RDP-Eigenschaften** aus.
7. Legen Sie die gewünschte Eigenschaft fest.
   - Alternativ dazu können Sie die Registerkarte **Erweitert** öffnen und die RDP-Eigenschaften in einem durch Semikolon getrennten Format hinzufügen, wie in den PowerShell-Beispielen in den folgenden Abschnitten zu sehen.
8. Wenn Sie fertig sind, wählen Sie **Speichern** aus, um Ihre Änderungen zu speichern.

In den nächsten Abschnitten erfahren Sie, wie Sie benutzerdefinierte RDP-Eigenschaften manuell in PowerShell bearbeiten.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Hinzufügen oder Bearbeiten einer einzelnen benutzerdefinierten RDP-Eigenschaft

Wenn Sie eine einzelne benutzerdefinierte RDP-Eigenschaft hinzufügen oder bearbeiten möchten, führen Sie das folgende PowerShell-Cmdlet aus:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Führen Sie dieses Cmdlet aus, um zu überprüfen, ob das soeben ausgeführte Cmdlet die Eigenschaft aktualisiert hat:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Wenn Sie z. B. die Eigenschaft „audiocapturemode“ für einen Hostpool mit dem Namen 0301HP überprüfen möchten, geben Sie dieses Cmdlet ein:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Hinzufügen oder Bearbeiten von mehreren benutzerdefinierten RDP-Eigenschaften

Wenn Sie mehrere benutzerdefinierte RDP-Eigenschaften hinzufügen oder bearbeiten möchten, führen Sie die folgenden PowerShell-Cmdlets aus, wobei Sie die benutzerdefinierten RDP-Eigenschaften als eine Zeichenfolge mit Semikolons als Trennzeichen angeben:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

Sie können überprüfen, ob die RDP-Eigenschaft hinzugefügt wurde, indem Sie das folgende Cmdlet ausführen:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Wenn Sie basierend auf unserem vorherigen Cmdlet-Beispiel mehrere RDP-Eigenschaften für den Hostpool 0301HP einrichten, sieht Ihr Cmdlet wie folgt aus:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Zurücksetzen aller benutzerdefinierten RDP-Eigenschaften

Sie können eine einzelne benutzerdefinierte RDP-Eigenschaft auf deren Standardwert zurücksetzen, indem Sie den Anweisungen unter [Hinzufügen oder Bearbeiten einer einzelnen benutzerdefinierten RDP-Eigenschaft](#add-or-edit-a-single-custom-rdp-property) folgen, oder Sie können alle benutzerdefinierten RDP-Eigenschaften für einen Hostpool zurücksetzen, indem Sie das folgende PowerShell-Cmdlet ausführen:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Um sicherzustellen, dass Sie die Einstellung erfolgreich entfernt haben, geben Sie dieses Cmdlet ein:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die RDP-Eigenschaften eines bestimmten Hostpools angepasst haben, können Sie sich bei einem Azure Virtual Desktop-Client anmelden, um die Einstellungen als Teil einer Benutzersitzung zu testen. In den nächsten Vorgehensweisen erfahren Sie, wie Sie mit dem Client Ihrer Wahl eine Verbindung mit einer Sitzung herstellen:

- [Herstellen einer Verbindung mit dem Windows-Desktopclient](./user-documentation/connect-windows-7-10.md)
- [Herstellen einer Verbindung mit dem Webclient](./user-documentation/connect-web.md)
- [Herstellen einer Verbindung mit dem Android-Client](./user-documentation/connect-android.md)
- [Herstellen einer Verbindung mit dem macOS-Client](./user-documentation/connect-macos.md)
- [Herstellen einer Verbindung mit dem iOS-Client](./user-documentation/connect-ios.md)
