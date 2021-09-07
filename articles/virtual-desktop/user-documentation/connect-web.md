---
title: Herstellen einer Verbindung mit Azure Virtual Desktop über den Webclient – Azure
description: Informationen zum Herstellen einer Verbindung mit Azure Virtual Desktop mithilfe des Webclients.
author: Heidilohr
ms.topic: how-to
ms.date: 07/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0c6e616b63587f46d9dfe73066147de428d0ee0c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469187"
---
# <a name="connect-to-azure-virtual-desktop-with-the-web-client"></a>Herstellen einer Verbindung mit Azure Virtual Desktop über den Webclient

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](../virtual-desktop-fall-2019/connect-web-2019.md).

Mit dem Webclient können Sie über einen Webbrowser auf Ihre Ressourcen von Azure Virtual Desktop zugreifen, ohne den langwierigen Installationsvorgang durchführen zu müssen.

>[!NOTE]
>Der Webclient verfügt derzeit nicht über Unterstützung für Mobilgerät-Betriebssysteme.

## <a name="supported-operating-systems-and-browsers"></a>Unterstützte Betriebssysteme und Browser
>[!IMPORTANT]
>Ab dem 30. September 2021 wird Internet Explorer 11 vom Azure Virtual Desktop-Webclient nicht mehr unterstützt. Es wird empfohlen, anstelle von Internet Explorer 11 auf die Verwendung des Microsoft Edge-Browsers umzusteigen. Weitere Informationen finden Sie in unserem [Tech Community-Blogbeitrag](https://aka.ms/WVDSupportIE11).

Es sollten alle HTML5-fähigen Browser funktionieren, aber hier sind die Betriebssysteme und Browser angegeben, die offiziell unterstützt werden.

| Browser           | Unterstütztes Betriebssystem                     | Notizen               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Version 11 oder höher |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 oder höher |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Zugreifen auf den Feed für Remoteressourcen

Navigieren Sie in einem Browser unter <https://rdweb.wvd.microsoft.com/arm/webclient> zu der mit dem Azure Resource Manager integrierten Version des Azure Virtual Desktop-Webclients, und melden Sie sich mit Ihrem Benutzerkonto an.

>[!NOTE]
>Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Integration verwenden, stellen Sie stattdessen eine Verbindung mit Ihren Ressourcen unter <https://rdweb.wvd.microsoft.com/webclient> her.
>
> Verwenden Sie bei Nutzung des US Gov-Portals <https://rdweb.wvd.azure.us/arm/webclient/index.html>.
> 
> Wenn Sie eine Verbindung mit dem Azure China-Portal herstellen möchten, verwenden Sie <https://rdweb.wvd.azure.cn/arm/webclient/index.html>.

>[!NOTE]
>Wenn Sie sich bereits mit einem anderen Azure Active Directory-Konto angemeldet haben als dem, das Sie für Azure Virtual Desktop verwenden möchten, sollten Sie sich entweder abmelden oder ein privates Browserfenster verwenden.

Nach dem Anmelden sollte eine Liste mit Ressourcen angezeigt werden. Sie können Ressourcen starten, indem Sie sie auf der Registerkarte **Alle Ressourcen** wie eine normale App auswählen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung des Webclients finden Sie unter [Erste Schritte mit dem Webclient](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
