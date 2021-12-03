---
title: Bereitstellen von in Azure AD eingebundenen VMs in Azure Virtual Desktop – Azure
description: Vorgehensweise zum Konfigurieren und Bereitstellen von in Azure AD eingebundenen VMs in Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
ms.openlocfilehash: 7b4ec084b39d7efde884f0cee0235f6fd3653538
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987098"
---
# <a name="deploy-azure-ad-joined-virtual-machines-in-azure-virtual-desktop"></a>Bereitstellung von Azure AD-gekoppelten virtuellen Maschinen in Azure Virtual Desktop

Dieser Artikel führt Sie durch den Prozess für die Bereitstellung von und für den Zugriff auf in Azure AD eingebundene VMs in Azure Virtual Desktop. Mit Azure AD-joined VMs entfällt die Notwendigkeit, eine Sichtverbindung von der VM zu einem lokalen oder virtualisierten Active Directory-Domänen-Controller (DC) herzustellen oder Azure AD-Domänendienste (Azure AD DS) bereitzustellen. In einigen Fällen sind sogar überhaupt keine Domänencontroller mehr erforderlich, wodurch die Bereitstellung und Verwaltung der Umgebung deutlich vereinfacht wird. Diese VMs können zur Vereinfachung der Verwaltung auch automatisch bei Intune registriert werden.

## <a name="supported-configurations"></a>Unterstützte Konfigurationen

Die folgenden Konfigurationen werden derzeit für in Azure AD eingebundene VMs unterstützt:

- Persönliche Desktops mit lokalen Benutzerprofilen.
- Pool-Desktops, die als Jumpbox verwendet werden. Bei dieser Konfiguration greifen Benutzer zuerst auf eine Azure Virtual Desktop-VM zu, bevor sie eine Verbindung mit einem anderen PC im Netzwerk herstellen. Benutzer sollten keine Daten auf der VM speichern.
- Pool-Desktops oder -Anwendungen, bei denen Benutzer keine Daten auf der VM speichern müssen. Zum Beispiel bei Anwendungen, die Daten online speichern oder eine Verbindung mit einer Remotedatenbank herstellen.

Bei den Benutzerkonten kann es sich um ausschließlich cloudbasierte oder um hybride Benutzer desselben Azure AD-Mandanten handeln.

## <a name="known-limitations"></a>Bekannte Einschränkungen

Die folgenden bekannten Einschränkungen können den Zugriff auf Ihre lokalen oder Active Directory-Domänen-gekoppelten Ressourcen beeinträchtigen und sollten bei der Entscheidung, ob Azure AD-gekoppelte VMs für Ihre Umgebung geeignet sind, berücksichtigt werden. Wir empfehlen Azure AD-joined VMs derzeit für Szenarien, in denen Anwender nur Zugriff auf Cloud-basierte Ressourcen oder Azure AD-basierte Authentifizierung benötigen.

- Azure Virtual Desktop (classic) unterstützt keine Azure AD-joined VMs.
- Azure AD-joined VMs unterstützen derzeit keine externen Benutzer.
- Azure AD-joined VMs unterstützt derzeit nur lokale Benutzerprofile.
- Azure AD-joined VMs können nicht auf Azure Files-Dateifreigaben für FSLogix oder MSIX App Attach zugreifen. Sie benötigen eine Kerberos-Authentifizierung, um auf eine dieser Funktionen zuzugreifen.
- Der Windows Store-Client unterstützt derzeit keine Azure AD-joined VMs.
- Das einmalige Anmelden für in Azure AD eingebundene VMs wird derzeit von Azure Virtual Desktop nicht unterstützt.

## <a name="deploy-azure-ad-joined-vms"></a>Bereitstellen von in Azure AD eingebundenen VMs

Sie können in Azure AD eingebundene VMs direkt über das Azure-Portal bereitstellen, wenn Sie [einen neuen Hostpool erstellen](create-host-pools-azure-marketplace.md) oder [einen vorhandenen Hostpool erweitern](expand-existing-host-pool.md). Wählen Sie auf der Registerkarte „Virtual Machines“ aus, ob der virtuelle Computer in Active Directory oder in Azure Active Directory eingebunden werden soll. Wenn Sie **Azure Active Directory** auswählen, können Sie die **VM automatisch bei Intune registrieren**, damit Sie VMs mit [Windows 10 Enterprise](/mem/intune/fundamentals/windows-virtual-desktop) und [Windows 10 Enterprise (mehrere Sitzungen)](/mem/intune/fundamentals/windows-virtual-desktop-multi-session) problemlos verwalten können. Beachten Sie, dass die VMs mit der Option „Azure Active Directory“ in denselben Azure AD-Mandanten eingebunden werden wie Ihr Abonnement.

> [!NOTE]
> - Hostpools sollten nur VMs enthalten, die in dieselbe Domäne eingebunden sind. So sollten in AD eingebundene VMs nur mit anderen AD-VMs in einem Hostpool sein, und umgekehrt.
> - Auf den Hostpool-VMs muss Windows 10 (mit einzelnen oder mehreren Sitzungen), Version 2004 oder höher, ausgeführt werden.
> - Die Verwaltung von Azure Virtual Desktop-Sitzungshosts mit Microsoft Endpoint Manager (Intune) wird derzeit nur in der öffentlichen Azure-Cloud unterstützt.

### <a name="assign-user-access-to-host-pools"></a>Benutzerzugriff auf Hostpools zuweisen

Nachdem Sie Ihren Host-Pool erstellt haben, müssen Sie den Benutzern Zugriff auf die Ressourcen gewähren. Um den Zugriff auf die Ressourcen zu gewähren, fügen Sie jeden Benutzer zur Anwendungsgruppe hinzu. Befolgen Sie die Anweisungen unter [Anwendungsgruppen verwalten](manage-app-groups.md), um Benutzern Zugriffsberechtigungen für Anwendungen und Desktops zu erteilen. Es wird empfohlen, dafür nach Möglichkeit Benutzergruppen anstelle einzelner Benutzer zu verwenden.

Für Azure AD-verbundene VMs müssen Sie zusätzlich zu den Anforderungen für Active Directory oder Azure Active Directory Domain Services-basierte Bereitstellungen zwei weitere Dinge tun:  

- Weisen Sie Ihren Anwendern die Rolle **Virtual Machine User Login** zu, damit sie sich bei den VMs anmelden können.
- Weisen Sie Administratoren, die lokale administrative Rechte benötigen, die Rolle **Virtual Machine Administrator Login** zu.

Um Benutzern Zugriff auf in Azure AD eingebundene VMs zu gewähren, müssen Sie die [Rollenzuweisungen für die VM konfigurieren](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#configure-role-assignments-for-the-vm). Sie können die Rollen **VM-Benutzeranmeldung** oder **VM-Administratoranmeldung** entweder auf den VMs, in der Ressourcengruppe, die die VMs enthält, oder im Abonnement zuweisen. Es wird empfohlen, die Rolle „VM-Benutzeranmeldung“ derselben Benutzergruppe zuzuweisen, die Sie für die Anwendungsgruppe auf Ressourcengruppenebene verwendet haben, damit sie auf alle VMs im Hostpool angewendet wird.

## <a name="access-azure-ad-joined-vms"></a>Zugriff auf in Azure AD eingebundene VMs

In diesem Abschnitt wird erläutert, wie Sie von verschiedenen Azure Virtual Desktop-Clients auf in Azure AD eingebundene VMs zugreifen.

### <a name="connect-using-the-windows-desktop-client"></a>Herstellen einer Verbindung mit dem Windows-Desktopclient

Die Standardkonfiguration unterstützt Verbindungen von Windows 10 mithilfe des [Windows-Desktopclients](user-documentation/connect-windows-7-10.md). Sie können Anmeldeinformationen, die Smartcard, [Windows Hello for Business mit zertifikatbasierter Vertrauensstellung](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust) oder [Windows Hello for Business mit schlüsselbasierter Vertrauensstellung und Zertifikaten](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs) verwenden, um sich beim Sitzungshost anzumelden. Um auf den Sitzungshost zugreifen zu können, muss Ihr lokaler PC jedoch eine der folgenden Bedingungen erfüllen:

- der lokale PC ist in denselben Azure AD-Mandanten eingebunden wie der Sitzungshost
- der lokale PC ist als Hybridgerät in denselben Azure AD-Mandanten eingebunden wie der Sitzungshost
- Auf dem lokalen PC wird Windows 10 (Version 2004 und höher) ausgeführt und er ist über Azure AD beim gleichen Azure AD-Mandanten registriert wie der Sitzungshost.

Um den Zugriff von Windows-Geräten zu ermöglichen, die nicht in Azure AD eingebunden sind, fügen Sie **targetisaadjoined:i:1** als eine [benutzerdefinierte RDP-Eigenschaft](customize-rdp-properties.md) zum Hostpool hinzu. Bei diesen Verbindungen müssen Benutzername und Kennwortanmeldeinformationen für die Anmeldung beim Sitzungshost eingegeben werden.

### <a name="connect-using-the-other-clients"></a>Herstellen einer Verbindung mit anderen Clients

Um mit Web-, Android-, macOS- und iOS-Clients auf in Azure AD eingebundene VMs zuzugreifen, müssen Sie **targetisaadjoined:i:1** als eine [benutzerdefinierte RDP-Eigenschaft](customize-rdp-properties.md) zum Hostpool hinzufügen. Bei diesen Verbindungen müssen Benutzername und Kennwortanmeldeinformationen für die Anmeldung beim Sitzungshost eingegeben werden.

### <a name="enabling-mfa-for-azure-ad-joined-vms"></a>Aktivieren der mehrstufigen Authentifizierung für in Azure AD eingebundene VMs

Sie können die [Multifaktorauthentifizierung](set-up-mfa.md) für Azure AD-gekoppelte VMs aktivieren, indem Sie eine Richtlinie für bedingten Zugriff in der Azure Virtual Desktop-App festlegen. Damit Verbindungen erfolgreich sind, müssen Sie [die herkömmliche Mehrfaktor-Authentifizierung pro Benutzer deaktivieren](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required). Wenn Sie die Anmeldung nicht auf sichere Authentifizierungsmethoden wie Windows Hello for Business beschränken möchten, müssen Sie die [Azure Windows VM-Anmelde-App](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#mfa-sign-in-method-required) aus Ihrer Richtlinie für bedingten Zugriff ausschließen.

## <a name="user-profiles"></a>Benutzerprofile

Azure Virtual Desktop unterstützt derzeit nur lokale Profile für in Azure AD eingebundene VMs.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einige in Azure AD eingebundene VMs bereitgestellt haben, können Sie sich an einem unterstützten Azure Virtual Desktop-Client anmelden, um ihn als Teil einer Benutzersitzung zu testen. Weitere Informationen darüber, wie Sie eine Verbindung mit einer Sitzung herstellen können, finden Sie in den folgenden Artikeln:

- [Herstellen einer Verbindung mit dem Windows-Desktopclient](user-documentation/connect-windows-7-10.md)
- [Herstellen einer Verbindung mit dem Webclient](user-documentation/connect-web.md)
- [Problembehandlung bei Verbindungen mit in Azure AD eingebundenen VMs](troubleshoot-azure-ad-connections.md)
