---
title: Bereitstellen von in Azure AD eingebundenen VMs in Azure Virtual Desktop – Azure
description: Vorgehensweise zum Konfigurieren und Bereitstellen von in Azure AD eingebundenen VMs in Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 08/11/2021
ms.author: helohr
ms.openlocfilehash: c7767ad85fabf748a442644f6c7c6701375d58c0
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122350879"
---
# <a name="deploy-azure-ad-joined-virtual-machines-in-azure-virtual-desktop"></a>Bereitstellen von in Azure AD eingebundenen VMs in Azure Virtual Desktop

> [!IMPORTANT]
> Die Unterstützung für in Azure AD eingebunden VMs befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dieser Artikel führt Sie durch den Prozess für die Bereitstellung von und für den Zugriff auf in Azure AD eingebundene VMs in Azure Virtual Desktop. In Azure AD eingebundene VMs müssen nicht mehr über eine Sichtverbindung zwischen der VM und einem lokalen oder virtualisierten Active Directory-Domänencontroller (DC) verfügen oder Azure AD Domain Services (Azure AD DS) bereitstellen. In einigen Fällen sind sogar überhaupt keine Domänencontroller mehr erforderlich, wodurch die Bereitstellung und Verwaltung der Umgebung deutlich vereinfacht wird. Diese VMs können zur Vereinfachung der Verwaltung auch automatisch bei Intune registriert werden.

> [!NOTE]
> Dieses Feature wird von Azure Virtual Desktop (Classic) nicht unterstützt.

## <a name="supported-configurations"></a>Unterstützte Konfigurationen

Die folgenden Konfigurationen werden derzeit für in Azure AD eingebundene VMs unterstützt:

- Persönliche Desktops mit lokalen Benutzerprofilen.
- Pool-Desktops, die als Jumpbox verwendet werden. Bei dieser Konfiguration greifen Benutzer zuerst auf eine Azure Virtual Desktop-VM zu, bevor sie eine Verbindung mit einem anderen PC im Netzwerk herstellen. Benutzer sollten keine Daten auf der VM speichern.
- Pool-Desktops oder -Anwendungen, bei denen Benutzer keine Daten auf der VM speichern müssen. Zum Beispiel bei Anwendungen, die Daten online speichern oder eine Verbindung mit einer Remotedatenbank herstellen.

Bei den Benutzerkonten kann es sich um ausschließlich cloudbasierte oder um hybride Benutzer desselben Azure AD-Mandanten handeln. Externe Benutzer werden derzeit nicht unterstützt.

## <a name="deploy-azure-ad-joined-vms"></a>Bereitstellen von in Azure AD eingebundenen VMs

> [!IMPORTANT]
> In der öffentlichen Vorschauversion müssen Sie Ihren Hostpool so konfigurieren, dass er sich in der [Validierungsumgebung](create-validation-host-pool.md) befindet.

Sie können in Azure AD eingebundene VMs direkt über das Azure-Portal bereitstellen, wenn Sie [einen neuen Hostpool erstellen](create-host-pools-azure-marketplace.md) oder [einen vorhandenen Hostpool erweitern](expand-existing-host-pool.md). Wählen Sie auf der Registerkarte „Virtual Machines“ aus, ob der virtuelle Computer in Active Directory oder in Azure Active Directory eingebunden werden soll. Wenn Sie **Azure Active Directory** auswählen, können Sie die **VM automatisch bei Intune registrieren**, damit Sie VMs mit [Windows 10 Enterprise](/mem/intune/fundamentals/windows-virtual-desktop) und [Windows 10 Enterprise (mehrere Sitzungen)](/mem/intune/fundamentals/windows-virtual-desktop-multi-session) problemlos verwalten können. Beachten Sie, dass die VMs mit der Option „Azure Active Directory“ in denselben Azure AD-Mandanten eingebunden werden wie Ihr Abonnement.

> [!NOTE]
> - Hostpools sollten nur VMs enthalten, die in dieselbe Domäne eingebunden sind. So sollten in AD eingebundene VMs nur mit anderen AD-VMs in einem Hostpool sein, und umgekehrt.
> - Auf den Hostpool-VMs muss Windows 10 (mit einzelnen oder mehreren Sitzungen), Version 2004 oder höher, ausgeführt werden.

Nachdem Sie den Hostpool erstellt haben, müssen Sie den Benutzerzugriff zuweisen. Bei in Azure AD eingebundenen VMs müssen Sie die beiden folgenden Schritte befolgen:

- Fügen Sie der Anwendungsgruppe Benutzer hinzu, um ihnen Zugriff auf die Ressourcen zu gewähren.
- Weisen Sie den Benutzern die Rolle „VM-Benutzeranmeldung“ zu, damit sie sich bei den VMs anmelden können.

Befolgen Sie die Anweisungen unter [Anwendungsgruppen verwalten](manage-app-groups.md), um Benutzern Zugriffsberechtigungen für Anwendungen und Desktops zu erteilen. Es wird empfohlen, dafür nach Möglichkeit Benutzergruppen anstelle einzelner Benutzer zu verwenden.

Um Benutzern Zugriff auf in Azure AD eingebundene VMs zu gewähren, müssen Sie die [Rollenzuweisungen für die VM konfigurieren](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#configure-role-assignments-for-the-vm). Sie können die Rollen **VM-Benutzeranmeldung** oder **VM-Administratoranmeldung** entweder auf den VMs, in der Ressourcengruppe, die die VMs enthält, oder im Abonnement zuweisen. Es wird empfohlen, die Rolle „VM-Benutzeranmeldung“ derselben Benutzergruppe zuzuweisen, die Sie für die Anwendungsgruppe auf Ressourcengruppenebene verwendet haben, damit sie auf alle VMs im Hostpool angewendet wird.

## <a name="access-azure-ad-joined-vms"></a>Zugriff auf in Azure AD eingebundene VMs

In diesem Abschnitt wird erläutert, wie Sie von verschiedenen Azure Virtual Desktop-Clients auf in Azure AD eingebundene VMs zugreifen.

> [!NOTE]
> Es ist derzeit nicht möglich, eine Verbindung mit in Azure AD eingebundenen VMs über den Windows Store-Client herzustellen.

> [!NOTE]
> Das einmalige Anmelden für in Azure AD eingebundene VMs wird derzeit von Azure Virtual Desktop nicht unterstützt.

### <a name="connect-using-the-windows-desktop-client"></a>Herstellen einer Verbindung mit dem Windows-Desktopclient

Die Standardkonfiguration unterstützt Verbindungen von Windows 10 mithilfe des [Windows-Desktopclients](user-documentation/connect-windows-7-10.md). Sie können Anmeldeinformationen, die Smartcard, [Windows Hello for Business mit zertifikatbasierter Vertrauensstellung](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust) oder [Windows Hello for Business mit schlüsselbasierter Vertrauensstellung und Zertifikaten](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs) verwenden, um sich beim Sitzungshost anzumelden. Um auf den Sitzungshost zugreifen zu können, muss Ihr lokaler PC jedoch eine der folgenden Bedingungen erfüllen:

- der lokale PC ist in denselben Azure AD-Mandanten eingebunden wie der Sitzungshost
- der lokale PC ist als Hybridgerät in denselben Azure AD-Mandanten eingebunden wie der Sitzungshost
- Auf dem lokalen PC wird Windows 10 (Version 2004 und höher) ausgeführt und er ist über Azure AD beim gleichen Azure AD-Mandanten registriert wie der Sitzungshost.

Um den Zugriff von Windows-Geräten zu ermöglichen, die nicht in Azure AD eingebunden sind, fügen Sie **targetisaadjoined:i:1** als eine [benutzerdefinierte RDP-Eigenschaft](customize-rdp-properties.md) zum Hostpool hinzu. Bei diesen Verbindungen müssen Benutzername und Kennwortanmeldeinformationen für die Anmeldung beim Sitzungshost eingegeben werden.

### <a name="connect-using-the-other-clients"></a>Herstellen einer Verbindung mit anderen Clients

Um mit Web-, Android-, macOS- und iOS-Clients auf in Azure AD eingebundene VMs zuzugreifen, müssen Sie **targetisaadjoined:i:1** als eine [benutzerdefinierte RDP-Eigenschaft](customize-rdp-properties.md) zum Hostpool hinzufügen. Bei diesen Verbindungen müssen Benutzername und Kennwortanmeldeinformationen für die Anmeldung beim Sitzungshost eingegeben werden.

### <a name="enabling-mfa-for-azure-ad-joined-vms"></a>Aktivieren der mehrstufigen Authentifizierung für in Azure AD eingebundene VMs

Sie können die [mehrstufige Authentifizierung](set-up-mfa.md) für in Azure AD eingebundene VMs aktivieren, indem Sie eine Richtlinie für bedingten Zugriff in der Azure Virtual Desktop-App festlegen. Damit Verbindungen erfolgreich hergestellt werden können, müssen Sie die [mehrstufige Legacyauthentifizierung pro Benutzer deaktivieren](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#using-conditional-access). Wenn Sie die Anmeldung nicht auf sichere Authentifizierungsmethoden wie Windows Hello for Business beschränken möchten, müssen Sie die [Azure Windows VM-Anmelde-App](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required) aus Ihrer Richtlinie für bedingten Zugriff ausschließen.

## <a name="user-profiles"></a>Benutzerprofile

Azure Virtual Desktop unterstützt derzeit nur lokale Profile für in Azure AD eingebundene VMs.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einige in Azure AD eingebundene VMs bereitgestellt haben, können Sie sich an einem unterstützten Azure Virtual Desktop-Client anmelden, um ihn als Teil einer Benutzersitzung zu testen. Weitere Informationen darüber, wie Sie eine Verbindung mit einer Sitzung herstellen können, finden Sie in den folgenden Artikeln:

- [Herstellen einer Verbindung mit dem Windows-Desktopclient](user-documentation/connect-windows-7-10.md)
- [Herstellen einer Verbindung mit dem Webclient](user-documentation/connect-web.md)
- [Problembehandlung bei Verbindungen mit in Azure AD eingebundenen VMs](troubleshoot-azure-ad-connections.md)
