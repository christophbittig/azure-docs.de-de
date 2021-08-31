---
title: Verbindungen mit in Azure AD eingebundenen VMs in Azure Virtual Desktop – Azure
description: Beheben von Problemen beim Herstellen einer Verbindung mit in Azure AD eingebundenen VMs in Azure Virtual Desktop
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.author: helohr
ms.openlocfilehash: 97c81eadf7091f04b77b094a4622c1731d19c537
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122356460"
---
# <a name="connections-to-azure-ad-joined-vms"></a>Verbindungen mit in Azure AD eingebundenen virtuellen Computern

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager.

Verwenden Sie diesen Artikel, um in Azure Virtual Desktop Probleme bezüglich Verbindungen mit in Azure AD eingebundenen VMs zu beheben.

## <a name="provide-feedback"></a>Feedback geben

In der [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Azure Virtual Desktop-Dienst austauschen.

## <a name="all-clients"></a>Alle Clients

### <a name="your-account-is-configured-to-prevent-you-from-using-this-device"></a>Die Konfiguration Ihres Kontos lässt die Verwendung dieses Geräts nicht zu

Wenn ein Fehler mit dem Hinweis **Ihr Konto ist so konfiguriert, dass Sie dieses Gerät nicht verwenden können. Wenden Sie sich an Ihren Systemadministrator um weitere Informationen zu erhalten.** angezeigt wird, stellen Sie sicher, dass dem Benutzerkonto auf den VMs die Rolle [VM-Benutzeranmeldung](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#azure-role-not-assigned) erteilt wurde. 

## <a name="windows-desktop-client"></a>Windows-Desktop-Client

### <a name="the-logon-attempt-failed"></a>Der Anmeldeversuch ist fehlgeschlagen

Wenn bei der Aufforderung zur Eingabe von Anmeldeinformationen bei Windows Security ein Fehler mit dem Hinweis auftritt, dass der **Anmeldeversuch fehlgeschlagen** ist, überprüfen Sie Folgendes:

- Sie befinden sich auf einem Gerät, das in Azure AD eingebunden oder hybrid in Azure AD mit demselben Azure AD-Mandanten wie der Sitzungshost eingebunden ist ODER
- Sie nutzen ein Gerät mit Windows 10 2004 oder höher, das in Azure AD beim gleichen Azure AD-Mandanten wie der Sitzungshost registriert ist.
- Sowohl auf dem lokalen PC als auch auf dem Sitzungshost [ist das PKU2U-Protokoll aktiviert](/windows/security/threat-protection/security-policy-settings/network-security-allow-pku2u-authentication-requests-to-this-computer-to-use-online-identities).

### <a name="the-sign-in-method-youre-trying-to-use-isnt-allowed"></a>Die Anmeldemethode, die Sie verwenden möchten, ist nicht zulässig

Wenn ein Fehler mit der Meldung **Die Anmeldemethode, die Sie verwenden möchten, ist nicht zulässig. Probieren Sie eine andere Anmeldemethode aus, oder wenden Sie sich an Ihren Systemadministrator** auftritt, bestehen Richtlinien für bedingten Zugriff, die den Zugriff einschränken. Befolgen Sie die Anweisungen unter [Aktivieren der mehrstufigen Authentifizierung](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms), um die mehrstufige Authentifizierung für Ihre in Azure AD eingebundenen VMs zu aktivieren.

## <a name="web-client"></a>Webclient

### <a name="sign-in-failed-please-check-your-username-and-password-and-try-again"></a>Fehler bei der Anmeldung. Überprüfen Sie Ihren Benutzernamen und Ihr Kennwort, und versuchen Sie es erneut

Wenn Sie auf einen Fehler mit der Meldung **Ups, wir konnten keine Verbindung mit NAME herstellen. Anmeldung fehlgeschlagen. Überprüfen Sie Ihren Benutzernamen und Ihr Kennwort, und versuchen Sie es erneut.** stoßen, während Sie den Webclient verwenden, stellen Sie sicher, dass Sie [Verbindungen von anderen Clients](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients) aktiviert haben.

### <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Aufgrund eines Sicherheitsfehlers konnte keine Verbindung mit dem Remotecomputer hergestellt werden.

Wenn Sie auf einen Fehler mit der Meldung **Ups, wir konnten keine Verbindung mit NAME herstellen. Aufgrund eines Sicherheitsfehlers konnte keine Verbindung mit dem Remote-PC hergestellt werden. Wenn dies weiterhin geschieht, bitten Sie Ihren Administrator oder technischen Support um Hilfe** stoßen, bestehen Richtlinien für bedingten Zugriff, die den Zugriff einschränken. Befolgen Sie die Anweisungen unter [Aktivieren der mehrstufigen Authentifizierung](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms), um die mehrstufige Authentifizierung für Ihre in Azure AD eingebundenen VMs zu aktivieren.

## <a name="android-client"></a>Android-Client

### <a name="error-code-2607---we-couldnt-connect-to-the-remote-pc-because-your-credentials-did-not-work"></a>Fehlercode 2607: Wir konnten keine Verbindung mit dem Remote-PC herstellen, da Ihre Anmeldeinformationen nicht funktioniert haben.

Wenn ein Fehler mit der Meldung **Wir konnten keine Verbindung mit dem Remote-PC herstellen, da Ihre Anmeldeinformationen nicht funktioniert haben. Der Remotecomputer ist in AADJ eingebunden.** mit dem Fehlercode 2607 auftritt, während Sie den Android-Client verwenden, stellen Sie sicher, dass Sie [Verbindungen von anderen Clients](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients) aktiviert haben.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Azure Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview.md).
- Informationen zur Problembehandlung beim Erstellen einer Azure Virtual Desktop-Umgebung und eines Hostpools in einer Azure Virtual Desktop-Umgebung finden Sie unter [Umgebungs- und Hostpoolerstellung](troubleshoot-set-up-issues.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Azure Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Problembehandlung beim Azure Virtual Desktop-Agent oder der Sitzungskonnektivität finden Sie unter [Beheben häufiger Probleme mit dem Azure Virtual Desktop-Agent](troubleshoot-agent.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Azure Virtual Desktop finden Sie unter [Azure Virtual Desktop – PowerShell](troubleshoot-powershell.md).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
