---
title: 'Azure Virtual Desktop-Authentifizierung: Azure'
description: Authentifizierungsmethoden für Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c987ad26d28a6c96d187c420b32bd3103391c62d
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122356455"
---
# <a name="supported-authentication-methods"></a>Unterstützte Authentifizierungsmethoden

In diesem Artikel erhalten Sie einen kurzen Überblick darüber, welche Arten von Authentifizierung Sie in Azure Virtual Desktop verwenden können.

## <a name="identities"></a>Identities

Azure Virtual Desktop unterstützt verschiedene Arten von Identitäten, je nachdem, welche Konfiguration Sie auswählen. In diesem Abschnitt wird erläutert, welche Identitäten Sie für jede Konfiguration verwenden können.

### <a name="on-premise-identity"></a>Lokale Identität

Da Benutzer über Azure Active Directory (Azure AD) auffindbar sein müssen, damit sie auf Azure Virtual Desktop zugreifen können, werden Benutzeridentitäten nicht unterstützt, die nur in Active Directory Domain Services (AD DS) vorhanden sind. Das umfasst auch eigenständige Active Directory-Bereitstellungen mit Active Directory-Verbunddiensten (AD FS).

### <a name="hybrid-identity"></a>Hybrididentität

Azure Virtual Desktop unterstützt [Hybrididentitäten](../active-directory/hybrid/whatis-hybrid-identity.md) über Azure AD, einschließlich solcher, die über AD FS einem Verbund angehören. Sie können diese Benutzeridentitäten in AD DS verwalten und mithilfe von [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md) mit Azure AD synchronisieren. Sie können auch Azure AD verwenden, um diese Identitäten zu verwalten, und sie mit [Azure AD Directory Services (Azure AD DS)](../active-directory-domain-services/overview.md) synchronisieren.

Beim Zugriff auf Azure Virtual Desktop unter Verwendung von Hybrididentitäten stimmen manchmal der Benutzerprinzipalname (User Principal Name, UPN) oder die Sicherheits-ID (SID) für den Benutzer in Active Directory (AD) und Azure AD nicht überein. Beispielsweise kann das AD-Konto user@contoso.local dem Konto user@contoso.com in Azure AD entsprechen. Azure Virtual Desktop unterstützt diese Art Konfiguration nur, wenn entweder der UPN oder die SID für die Konten in AD und Azure AD übereinstimmen. SID bezieht sich auf die Benutzerobjekteigenschaft „ObjectSID“ in AD und „OnPremisesSecurityIdentifier“ in Azure AD.

### <a name="cloud-only-identity"></a>Reine Cloudidentität

Azure Virtual Desktop unterstützt reine Cloudidentitäten bei Verwendung von [in Azure AD eingebundenen VMs](deploy-azure-ad-joined-vm.md).

### <a name="external-identity"></a>Externe Identität

Azure Virtual Desktop unterstützt derzeit keine [externen Identitäten](../active-directory/external-identities/index.yml).

## <a name="service-authentication"></a>Dienstauthentifizierung

Um auf Azure Virtual Desktop-Ressourcen zugreifen zu können, müssen Sie sich zunächst beim Dienst authentifizieren, indem Sie sich bei einem Azure AD-Konto anmelden. Die Authentifizierung erfolgt beim Abonnieren eines Arbeitsbereichs, um Ihre Ressourcen abzurufen, oder bei jeder Verbindungsherstellung mit Apps oder Desktops. Sie können [Identitätsanbieter von Drittherstellern](../active-directory/devices/azureadjoin-plan.md#federated-environment) verwenden, sofern diese sich in einem Verbund mit Azure AD befinden.

### <a name="multifactor-authentication"></a>Mehrstufige Authentifizierung

Befolgen Sie die Anweisungen unter [Aktivieren der mehrstufigen Authentifizierung von Azure für Azure Virtual Desktop](set-up-mfa.md), um zu erfahren, wie Sie die mehrstufige Authentifizierung (Multifactor Authentication, MFA) für Ihre Bereitstellung aktivieren. In diesem Artikel erfahren Sie auch, wie Sie konfigurieren, wie häufig Ihre Benutzer zur Eingabe ihrer Anmeldeinformationen aufgefordert werden. Wenn Sie in Azure AD eingebundene VMs bereitstellen, befolgen Sie den Konfigurationsleitfaden unter [Aktivieren der MFA für in Azure AD eingebundene VMs](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms).

### <a name="smart-card-authentication"></a>Authentifizierung mit Smartcards

Um eine Smartcard zur Authentifizierung bei Azure AD verwenden zu können, müssen Sie zuerst [AD FS für die Benutzerzertifikatauthentifizierung konfigurieren](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication).

## <a name="session-host-authentication"></a>Sitzungshostauthentifizierung

Wenn Sie das [einmalige Anmelden](#single-sign-on-sso) (Single Sign-On, SSO) noch nicht aktiviert oder Ihre Anmeldeinformationen lokal gespeichert haben, müssen Sie sich auch beim Sitzungshost authentifizieren. Folgende Anmeldemethoden für den Sitzungshost werden von den Azure Virtual Desktop-Clients derzeit unterstützt:

- Windows-Desktop-Client
    - Benutzername und Kennwort
    - Smartcard
    - [Windows Hello for Business (Zertifikatvertrauensstellung)](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)
    - [Windows Hello for Business (Schlüsselvertrauensstellung mit Zertifikaten)](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)
- Windows Store-Client
    - Benutzername und Kennwort
- Webclient
    - Benutzername und Kennwort
- Android
    - Benutzername und Kennwort
- iOS
    - Benutzername und Kennwort
- macOS
    - Benutzername und Kennwort

Azure Virtual Desktop unterstützt sowohl NTLM (NT LAN Manager) als auch Kerberos für Sitzungshostauthentifizierung. Smartcards und Windows Hello for Business können nur Kerberos für die Anmeldung verwenden. Zur Verwendung von Kerberos muss der Client Kerberos-Sicherheitstickets von einem KDC-Dienst (Key Distribution Center) abrufen, der auf einem Domänencontroller ausgeführt wird. Zum Abrufen von Tickets benötigt der Client direkte Netzwerksicht auf den Domänencontroller. Eine solche Sicht können Sie erreichen, indem Sie eine direkte Verbindung mit Ihrem Unternehmensnetzwerk herstellen und dabei eine VPN-Verbindung verwenden oder einen [KDC-Proxyserver](key-distribution-center-proxy.md) einrichten.

### <a name="single-sign-on-sso"></a>Einmaliges Anmelden (Single Sign-On, SSO)

Azure Virtual Desktop unterstützt das [einmalige Anmelden mittels Active Directory-Verbunddienste (ADFS)](configure-adfs-sso.md) für Windows- und Webclients. Dank SSO können Sie die Authentifizierung beim Sitzungshost überspringen.

Die einzige Möglichkeit zu vermeiden, dass Sie zur Eingabe Ihrer Anmeldedaten für den Sitzungshost aufgefordert werden, besteht andernfalls darin, diese im Client zu speichern. Wir empfehlen Ihnen, zu diesem Zweck nur sichere Geräte zu verwenden, um zu verhindern, dass andere Benutzer auf Ihre Ressourcen zugreifen.

## <a name="in-session-authentication"></a>Sitzungsinterne Authentifizierung

Nachdem Sie eine Verbindung mit Ihrer Remote-App oder Ihrem Remotedesktop hergestellt haben, werden Sie möglicherweise aufgefordert, sich innerhalb der Sitzung zu authentifizieren. In diesem Abschnitt wird erläutert, wie Sie in diesem Szenario andere Anmeldeinformationen als Benutzername und Kennwort verwenden.

### <a name="smart-cards"></a>Smartcards

Um in Ihrer Sitzung eine Smartcard zu verwenden, vergewissern Sie sich, dass die Smartcardtreiber auf dem Sitzungshost installiert sind und die [Umleitung von Smartcards](configure-device-redirections.md#smart-card-redirection) aktiviert ist. Vergewissern Sie sich im [Vergleichsdiagramm für Clients](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare#other-redirection-devices-etc), dass Ihr Client die Umleitung von Smartcards unterstützt.

### <a name="fido2-and-windows-hello-for-business"></a>FIDO2 und Windows Hello for Business

Azure Virtual Desktop unterstützt derzeit keine sitzungsinterne Authentifizierung mit FIDO2 oder Windows Hello for Business.

## <a name="next-steps"></a>Nächste Schritte

- Sind Sie neugierig auf andere Möglichkeiten, Ihre Bereitstellung sicher zu gestalten? Weitere Informationen finden Sie unter [Bewährte Methoden für Sicherheit](security-guide.md).
- Gibt es Probleme beim Herstellen von Verbindungen mit in Azure AD eingebundenen VMs? [Problembehandlung bei Verbindungen mit in Azure AD eingebundenen VMs](troubleshoot-azure-ad-connections.md).
- Möchten Sie Smartcards von Stellen außerhalb Ihres Unternehmensnetzwerks verwenden? Informieren Sie sich, wie Sie einen [KDC-Proxyserver](key-distribution-center-proxy.md) einrichten.
