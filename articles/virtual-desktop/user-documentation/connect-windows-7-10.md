---
title: 'Herstellen einer Verbindung mit Azure Virtual Desktop (Windows 10 oder 7): Azure'
description: Informationen zum Herstellen einer Verbindung mit Azure Virtual Desktop mithilfe des Windows Desktop-Clients
author: Heidilohr
ms.topic: how-to
ms.date: 07/20/2021
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 644572ccfcc8ee0218e472757073588f2b47d4c5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452677"
---
# <a name="connect-with-the-windows-desktop-client"></a>Herstellen einer Verbindung mit dem Windows-Desktopclient

Sie können auf Geräten mit Windows 10, Windows 10 IoT Enterprise und Windows 7 mit dem Windows Desktop-Client auf Azure Virtual Desktop-Ressourcen zugreifen. 

> [!IMPORTANT]
> Windows 8 und Windows 8.1 werden von dieser Methode nicht unterstützt.
> 
> Diese Methode unterstützt nur Azure Resource Manager-Objekte. Informationen zur Unterstützung von Objekten ohne Azure Resource Manager finden Sie unter [Herstellen einer Verbindung mit dem Windows-Desktopclient (klassisch)](../virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Darüber hinaus unterstützt diese Methode weder den RemoteApp-Client noch den Client für Desktopverbindungen (RADC) oder den Client für die Remotedesktopverbindung (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Installieren des Windows Desktop-Clients

Laden Sie den Client auf der Grundlage Ihrer Windows-Version herunter:

- [Windows (64-Bit)](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-Bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Wählen Sie während der Installation, um den Zugriff zu bestimmen, entweder:

- **Nur für Sie installieren**
- **Für alle Benutzer dieses Rechners installieren** (erfordert Administratorenrechte) (erfordert Administratorenrechte)

Um den Client nach der Installation zu starten, verwenden Sie das **Startmenü**, und suchen Sie nach **Remotedesktop**.

## <a name="subscribe-to-a-workspace"></a>Abonnieren eines Arbeitsbereichs

Um einen Arbeitsbereich zu abonnieren, wählen Sie entweder:

- Verwenden Sie ein Arbeits- oder Schulkonto und lassen Sie den Client die für Sie verfügbaren Ressourcen entdecken
- Verwenden Sie die spezifische URL der Ressource

Um die abonnierte Ressource zu starten, gehen Sie zum **Connection Center** und doppelklicken Sie auf die Ressource.

> [!TIP]
> Um eine Ressource aus dem **Startmenü** zu starten, können Sie den Ordner mit dem Namen des Arbeitsbereichs suchen oder den Ressourcennamen in die Suchleiste eingeben.

### <a name="use-a-user-account"></a>Verwenden Sie ein Benutzerkonto

1. Wählen Sie auf der Hauptseite **abonnieren** aus.
2. Melden Sie sich mit Ihrem Benutzerkonto an, wenn Sie dazu aufgefordert werden.

Die Ressourcen werden nach Arbeitsbereichen gruppiert im **Connection Centerr** angezeigt.

   > [!NOTE]
   > Der Windows-Client verwendet standardmäßig automatisch Azure Virtual Desktop (klassisch). 
   > 
   > Wenn der Client jedoch zusätzliche Azure Ressource Manager-Ressourcen erkennt, fügt er diese automatisch hinzu oder benachrichtigt den Benutzer, dass sie verfügbar sind.

### <a name="use-a-specific-url"></a>Verwenden Sie eine bestimmte URL

1. Wählen Sie auf der Hauptseite die Option **abonnieren mit URL** aus.
2. Geben Sie entweder die *Arbeitsbereichs-URL* oder eine *E-Mail-Adresse* ein:
   - Verwenden Sie für die **Arbeitsbereichs-URL** die URL, die von Ihrem Administrator bereitgestellt wird.

   |Verfügbare Ressourcen|URL|
   |-|-|
   |Azure Virtual Desktop (klassisch)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Azure Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Azure Virtual Desktop (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   |Azure Virtual Desktop (China)|`https://rdweb.wvd.azure.cn/api/arm/feeddiscovery`|
   
   - Für **Email**, verwenden Sie Ihre E-Mail-Adresse. 
      
   Der Client findet die URL, die Ihrer e-Mail zugeordnet ist, sofern der Administrator die [E-Mail-](/windows-server/remote/remote-desktop-services/rds-email-discovery)Ermittlung aktiviert hat.

3. Wählen Sie **Weiter** aus.
4. Melden Sie sich mit Ihrem Benutzerkonto an, wenn Sie dazu aufgefordert werden.

Die Ressourcen werden nach Arbeitsbereichen gruppiert im **Connection Centerr** angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über die Verwendung des Clients finden Sie unter [Erste Schritte mit dem Windows Desktop-Client](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

Wenn Sie ein Administrator sind und mehr über die Funktionen des Clients erfahren möchten, lesen Sie [Windows Desktop Client für Administratoren](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
