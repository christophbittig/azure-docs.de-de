---
title: Überlegungen zur Bereitstellung von Azure AD Multi-Factor Authentication
description: Erfahren Sie mehr über die Überlegungen zur Bereitstellung und über die Strategie für eine erfolgreiche Implementierung von Azure AD Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 120e97e62980427fb83c6bf7884da92dd9c5e1f7
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602312"
---
# <a name="plan-an-azure-active-directory-multi-factor-authentication-deployment"></a>Planen einer Azure Active Directory Multi-Factor Authentication-Bereitstellung 

Mit der mehrstufige Authentifizierung (MFA, Multi-Factor Authentication) von Azure Active Directory (Azure AD) wird der Zugriff auf Daten und Anwendungen durch die Bereitstellung einer zweiten Form der Authentifizierung geschützt. Organisationen können die mehrstufige Authentifizierung mit dem [bedingten Zugriff](../conditional-access/overview.md) aktivieren, um die Lösung an ihre jeweiligen Anforderungen anzupassen.

In diesem Leitfaden zur Bereitstellung erfahren Sie, wie Sie die Einführung der [mehrstufigen Authentifizierung in Azure](concept-mfa-howitworks.md) planen und umsetzen.

## <a name="prerequisites-for-deploying-azure-ad-mfa"></a>Voraussetzungen für die Bereitstellung der mehrstufigen Authentifizierung in Azure AD

Bevor Sie mit der Bereitstellung beginnen, sollten Sie sich vergewissern, dass die folgenden Voraussetzungen für die jeweiligen Szenarios erfüllt sind.

| Szenario | Voraussetzung |
|----------|--------------|
|**Nur Cloud**-Identitätsumgebung mit moderner Authentifizierung | **Keine erforderlichen Aufgaben** |
|**Hybrididentitätsszenarios** | Bereitstellen von [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) und Synchronisieren der Benutzeridentitäten zwischen lokalen Active Directory Domain Services (AD DS) und Azure AD |
| Für den Cloudzugriff veröffentlichte **lokale ältere Anwendungen**| Bereitstellen eines [Azure AD-Anwendungsproxys](../app-proxy/application-proxy-deployment-plan.md) |

## <a name="choose-authentication-methods-for-mfa"></a>Auswählen der Authentifizierungsmethoden für die mehrstufige Authentifizierung

Es gibt viele Methoden, die für eine zweistufige Authentifizierung verwendet werden können. Sie können die in der Liste der verfügbaren Authentifizierungsmethoden auf ihre Sicherheit, Benutzerfreundlichkeit und Verfügbarkeit hin prüfen und eine Methode auswählen.

>[!IMPORTANT]
>Aktivieren Sie mehrere MFA-Methoden, sodass Benutzer eine Methode in der Hinterhand haben, falls deren hauptsächlich verwendete Methode nicht verfügbar ist. Die Methoden umfassen:

- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
- [Microsoft Authenticator-App](concept-authentication-authenticator-app.md)
- [FIDO2-Sicherheitsschlüssel (Vorschau)](concept-authentication-passwordless.md#fido2-security-keys)
- [OATH-Hardwaretoken (Vorschau)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
- [Oath-Softwaretoken](concept-authentication-oath-tokens.md#oath-software-tokens)
- [SMS-Verifizierung](concept-authentication-phone-options.md#mobile-phone-verification)
- [Überprüfung mit Sprachanruf](concept-authentication-phone-options.md)

Berücksichtigen Sie bei der Auswahl der Authentifizierungsmethoden, die in Ihrem Mandanten verwendet werden sollen, die Sicherheit und Benutzerfreundlichkeit dieser Methoden:

![Auswählen der richtigen Authentifizierungsmethode](media/concept-authentication-methods/authentication-methods.png)

Weitere Informationen zur Leistungsfähigkeit und Sicherheit dieser Methoden und ihrer Funktionsweise finden Sie in den folgenden Ressourcen:

- [Welche Authentifizierungs- und Prüfmethoden stehen in Azure Active Directory zur Verfügung?](concept-authentication-methods.md)
- [Video: Choose the right authentication methods to keep your organization safe (Auswählen der geeigneten Authentifizierungsmethoden zum Schutz Ihrer Organisation)](https://youtu.be/LB2yj4HSptc)

Sie können dieses [PowerShell-Skript](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) verwenden, um die MFA-Konfigurationen der Benutzer zu analysieren und eine geeignete Authentifizierungsmethode vorzuschlagen. 

Ein optimales Maß an Flexibilität und Benutzerfreundlichkeit bietet die Microsoft Authenticator-App. Diese Authentifizierungsmethode bietet die bestmögliche Benutzererfahrung und verfügt über mehrere Modi, z. B. kennwortlose Authentifizierung, MFA-Pushbenachrichtigungen und OATH-Codes. Die Microsoft Authenticator-App erfüllt auch die Anforderungen für den [Authenticator-Vertrauensgrad 2 des National Institute of Standards and Technology](../standards/nist-authenticator-assurance-level-2.md).

Sie können die in Ihrem Mandanten verfügbaren Authentifizierungsmethoden steuern. So sollten Sie beispielsweise besonders unsichere Methoden wie SMS blockieren.

| Authentifizierungsmethode | Verwalten über | Bereichsdefinition |
|-----------------------|-------------|---------|
| Microsoft Authenticator (Pushbenachrichtigung und kennwortlose Anmeldung per Telefon)    | MFA-Einstellungen oder
Richtlinien für Authentifizierungsmethoden | Die in Authenticator verfügbare kennwortlose Anmeldung per Telefon kann auf Benutzer und Gruppen beschränkt werden. |
| FIDO2-Sicherheitsschlüssel | Richtlinien für Authentifizierungsmethoden | Kann auf Benutzer und Gruppen beschränkt werden |
| OATH-Token für Software oder Hardware | MFA-Einstellungen |     |
| SMS-Verifizierung | MFA-Einstellungen | Verwalten der SMS-Anmeldung für die primäre Authentifizierung in der Authentifizierungsrichtlinie Die SMS-Anmeldung kann auf Benutzer und Gruppen beschränkt werden. |
| Sprachanrufe | Richtlinien für Authentifizierungsmethoden |       |


## <a name="plan-conditional-access-policies"></a>Planen von Richtlinien für bedingten Zugriff

Die Azure AD MFA wird mit Richtlinien für bedingten Zugriff umgesetzt. Diese Richtlinien ermöglichen es Ihnen, Benutzer zur mehrstufigen Authentifizierung aufzufordern, wenn dies für die Sicherheit erforderlich ist, und sie Benutzern zu ersparen, wenn sie nicht erforderlich ist.

![Prozessfluss für den konzeptionellen bedingten Zugriff](media/howto-mfa-getstarted/conditional-access-overview-how-it-works.png)

Im Azure-Portal konfigurieren Sie Richtlinien für bedingten Zugriff unter **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.

Weitere Informationen zum Erstellen von Richtlinien für bedingten Zugriff finden Sie unter [Richtlinie für bedingten Zugriff, um zur Azure AD MFA aufzufordern, wenn sich ein Benutzer beim Azure-Portal anmeldet](tutorial-enable-azure-mfa.md). Das hilft Ihnen bei Folgendem:

- Vertrautwerden mit der Benutzeroberfläche
- Einführung in die Funktionsweise von bedingtem Zugriff

Einen ausführlichen Leitfaden zur Bereitstellung für bedingten Zugriff in Azure AD finden Sie unter [Planen einer Bereitstellung für bedingten Zugriff](../conditional-access/plan-conditional-access.md).

### <a name="common-policies-for-azure-ad-mfa"></a>Allgemeine Richtlinien für Azure AD MFA

Häufige Anwendungsfälle, bei denen Azure AD MFA vorgeschrieben ist:

- Für [Administratoren:](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- Auf [bestimmte Anwendungen](tutorial-enable-azure-mfa.md)
- Für [alle Benutzer](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- Für die [Azure-Verwaltung](../conditional-access/howto-conditional-access-policy-azure-management.md)
- Zugriff über [nicht vertrauenswürdige Netzwerkadressen](../conditional-access/untrusted-networks.md)

### <a name="named-locations"></a>Benannte Orte

Beim Verwalten der Richtlinien für bedingten Zugriff können Sie mit der Standortbedingung einer Richtlinie für bedingten Zugriff die Einstellungen der Zugriffssteuerung an die Netzwerkstandorte Ihrer Benutzer knüpfen. Es wird empfohlen, [benannte Standorte](../conditional-access/location-condition.md) zu verwenden, sodass logische Gruppierungen von IP-Adressbereichen oder Ländern und Regionen erstellt werden können. Daraufhin wird eine Richtlinie für alle Apps erstellt, die die Anmeldung von diesem benannten Standort blockieren. Stellen Sie sicher, dass Ihre Administratoren von dieser Richtlinie ausgenommen sind.

### <a name="risk-based-policies"></a>Risikobasierte Richtlinien

Wenn in Ihrer Organisation [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) zum Erkennen von Risikosignalen verwendet wird, sollten Sie anstelle von benannten Standorten [risikobasierte Richtlinien](../identity-protection/howto-identity-protection-configure-risk-policies.md) verwenden. Richtlinien können erstellt werden, um Kennwortänderungen zu erzwingen, wenn die Gefahr besteht, dass Identitäten kompromittiert werden, oder um eine mehrstufige Authentifizierung zu verlangen, wenn eine Anmeldung aufgrund von Ereignissen wie kompromittierte Anmeldeinformationen, Anmeldungen von anonymen IP-Adressen usw. als [riskant](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) eingestuft wird. 

Folgende Risikorichtlinien sind verfügbar:

- [Festlegen, dass sich Benutzer bei der Anmeldung für Azure AD MFA registrieren müssen](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
- [Festlegen, dass Benutzer, die ein hohes Risiko darstellen, ihr Kennwort ändern müssen](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)
- [Festlegen, dass Benutzer mit einem mittleren oder hohen Anmelderisiko MFA verwenden müssen](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertieren von Benutzern von „MFA pro Benutzer“ zu „MFA mit bedingtem Zugriff“

Wenn für Ihre Benutzer die Azure AD Multi-Factor Authentication pro Benutzer aktiviert und erzwungen wurde, können Sie mithilfe des folgenden PowerShell-Codes eine Konvertierung in die Azure AD Multi-Factor Authentication mit bedingtem Zugriff vornehmen.

Führen Sie diese PowerShell-Instanz in einem ISE-Fenster aus, oder speichern Sie sie zur lokalen Ausführung als `.PS1`-Datei. Der Vorgang kann nur mithilfe des Moduls [MSOnline](/powershell/module/msonline/?view=azureadps-1.0#msonline) ausgeführt werden. 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="plan-user-session-lifetime"></a>Planen der Lebensdauer von Benutzersitzungen

Bei der Planung Ihrer MFA-Bereitstellung müssen Sie überlegen, wie häufig Sie die Benutzer zu einer Eingabe auffordern möchten. Benutzer zur Angabe von Anmeldeinformationen aufzufordern, kann häufig sinnvoll erscheinen, sich aber nachteilig auswirken. Wenn Benutzer darin geschult werden, ihre Anmeldeinformationen ohne Nachdenken einzugeben, können sie diese versehentlich in einer böswilligen Eingabeaufforderung für Anmeldeinformationen angeben.
Azure AD verfügt über mehrere Einstellungen, mit denen Sie festlegen können, wie häufig sich Benutzer erneut authentifizieren müssen. Machen Sie sich mit den Anforderungen Ihres Unternehmens und Ihrer Benutzer vertraut, und konfigurieren Sie die Einstellungen, die für Ihre Umgebung die beste Kombination bieten.

Es wird empfohlen, Geräte mit primären Aktualisierungstoken (Primary Refresh Tokens, PRT) zu verwenden, um den Ablauf für den Endbenutzer zu verbessern und die Lebensdauer einer Sitzung mit eine Richtlinie für die Anmeldehäufigkeit nur in bestimmten geschäftlichen Anwendungsfällen zu verkürzen.

Weitere Informationen hierzu finden Sie unter [Optimieren von Aufforderungen zur erneuten Authentifizierung und Grundlegendes zur Sitzungslebensdauer für Azure AD MFA](concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="plan-user-registration"></a>Planen der Benutzerregistrierung

Ein wichtiger Schritt bei jeder MFA-Bereitstellung besteht in der Registrierung von Benutzern für die Verwendung von MFA. Authentifizierungsmethoden wie Sprach- und SMS-Nachrichten ermöglichen die Vorregistrierung, während andere wie die Authenticator App eine Benutzerinteraktion erfordern. Administratoren müssen bestimmen, wie Benutzer ihre Methoden registrieren. 

### <a name="combined-registration-for-sspr-and-azure-ad-mfa"></a>Kombinierten Registrierung für SSPR und Azure AD MFA
Es wird empfohlen, für Azure AD MFA die [kombinierte Registrierung](howto-registration-mfa-sspr-combined.md) und die [Azure AD-Self-Service-Kennwortzurücksetzung (SSPR)](concept-sspr-howitworks.md) zu verwenden. SSPR ermöglicht Benutzern das sichere Zurücksetzen des Kennworts mithilfe derselben Methoden, die sie für Azure AD MFA verwenden. Die kombinierte Registrierung besteht für Endnutzer aus nur einem Schritt.

### <a name="registration-with-identity-protection"></a>Registrierung mit Identity Protection
Azure AD Identity Protection trägt sowohl eine Registrierungsrichtlinie als auch automatische Risikoerkennungs- und Wartungsrichtlinien zu Azure AD MFA bei. Mithilfe von Richtlinien können bei Gefahr einer Identitätskompromittierung Kennwortänderungen erzwungen oder eine mehrstufige Authentifizierung (MFA) verlangt werden, wenn eine Anmeldung als riskant eingestuft wird.
Wenn Sie Azure AD Identity Protection verwenden, [konfigurieren Sie die Azure AD MFA-Registrierungsrichtlinie](../identity-protection/howto-identity-protection-configure-mfa-policy.md), um Ihre Benutzer aufzufordern, sich bei ihrer nächsten Anmeldung interaktiv zu registrieren.

### <a name="registration-without-identity-protection"></a>Registrierung ohne Identity Protection
Wenn Sie keine Lizenzen zum Aktivieren von Azure AD Identity Protection haben, werden Benutzer aufgefordert, sich das nächste Mal zu registrieren, wenn MFA bei der Anmeldung erforderlich ist. Wenn Sie festlegen möchten, dass Benutzer MFA verwenden müssen, können Sie Richtlinien für bedingten Zugriff verwenden und häufig verwendete Anwendungen wie HR-Systeme als Ziel verwenden. Wenn das Kennwort eines Benutzers kompromittiert wird, könnte es zum Registrieren für MFA verwendet und dabei die Kontrolle über das Konto übernommen werden. Daher wird empfohlen, [die Sicherheitsregistrierung mit Richtlinien für bedingten Zugriff zu schützen](../conditional-access/howto-conditional-access-policy-registration.md), indem verlangt wird, dass vertrauenswürdige Geräte und Standorte angegeben werden. Sie können den Prozess weiter schützen, indem Sie zusätzlich einen [befristeten Zugriffspass](howto-authentication-temporary-access-pass.md) verlangen. Ein von einem Administrator ausgegebener zeitlich begrenzter Passcode, der strenge Authentifizierungsanforderungen erfüllt und zum Integrieren anderer Authentifizierungsmethoden wie kennwortloser Methoden verwendet werden kann.

### <a name="increase-the-security-of-registered-users"></a>Erhöhen der Sicherheit registrierter Benutzer
Wenn Sie Benutzer für MFA per SMS oder Sprachanrufe registriert haben, sollten Sie diese auf sicherere Methoden wie die Microsoft Authenticator-App umstellen. Microsoft bietet jetzt eine öffentliche Vorschau der Funktionalität, mit der Sie Benutzer auffordern können, die Microsoft Authenticator-App während der Anmeldung einzurichten. Sie können diese Eingabeaufforderungen nach Gruppen festlegen und auf diese Weise bestimmen, wer zur Eingabe aufgefordert wird, sowie gezielte Kampagnen zur Umstellung von Benutzern auf sicherere Methoden durchführen. 

### <a name="plan-recovery-scenarios"></a>Planen von Wiederherstellungsszenarios 
Wie bereits erwähnt, sollten Sie sicherstellen, dass die Benutzer für mehrere MFA-Methoden registriert sind, damit sie entsprechend ausweichen können, wenn eine Methode nicht verfügbar ist. Wenn dem Benutzer keine Methode zum Ausweichen zur Verfügung steht, haben Sie folgende Möglichkeiten: 

- Stellen Sie ihm einen befristeten Zugriffspass zur Verfügung, damit er eigene Authentifizierungsmethoden verwenden kann. Sie können einen befristeten Zugriffspass auch bereitstellen, um einen befristeten Zugriff auf Ressourcen zu ermöglichen. 
- Aktualisieren Sie als Administrator die Methoden des Benutzers. Wählen Sie hierzu im Azure-Portal den Benutzer und die Authentifizierungsmethoden aus, und aktualisieren Sie die Methoden des Benutzers.
Benutzerkommunikation

Wichtig ist, dass Sie die Benutzer über anstehende Änderungen, Anforderungen der Azure AD MFA-Registrierung und ggf. erforderliche Benutzerhandlungen informieren. Wir stellen [Kommunikationsvorlagen](https://aka.ms/mfatemplates) und [Endbenutzerdokumentation](../user-help/security-info-setup-signin.md) zur Verfügung, mit denen Sie Ihre Informationsmitteilungen entwerfen können. Verweisen Sie Benutzer auf [https://myprofile.microsoft.com](https://myprofile.microsoft.com/), damit sie sich registrieren, indem sie den Link **Sicherheitsinformation** auf dieser Seite auswählen.

## <a name="plan-integration-with-on-premises-systems"></a>Planen der Integration mit lokalen Systemen

Anwendungen, die sich direkt bei Azure AD authentifizieren und die moderne Authentifizierung nutzen (WS-Fed, SAML, OAuth, OpenID Connect), können die Richtlinien für bedingten Zugriff verwenden.
Einige ältere und lokale Anwendungen, die sich nicht direkt bei Azure AD authentifizieren, müssen zusätzliche Schritte zur Verwendung von Azure AD MFA ausführen. Sie können sie integrieren, indem Sie den Azure AD-Anwendungsproxy oder [Netzwerkrichtliniendienste](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures) verwenden.

### <a name="integrate-with-ad-fs-resources"></a>Integrieren mit AD FS-Ressourcen

Es wird empfohlen, mit Anwendungen bei der Migration zu Azure AD mit Active Directory Federation Services (AD FS) zu schützen. Wenn Sie diese jedoch noch nicht zu Azure AD migrieren möchten, können Sie den Azure MFA-Adapter mit AD FS ab Version 2016 verwenden.
Wenn in Ihrer Organisation ein Verbund mit Azure AD genutzt wird, können Sie sowohl lokal als auch in der Cloud [Azure AD MFA als Authentifizierungsanbieter mit AD FS-Ressourcen konfigurieren](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa).  

### <a name="radius-clients-and-azure-ad-mfa"></a>RADIUS-Clients und Azure AD MFA

Clientanwendungen, für die die RADIUS-Authentifizierung verwendet wird, sollten auf moderne Protokolle wie SAML, Open ID Verbinden oder OAuth auf Azure AD umgestellt werden. Wenn die Anwendung nicht aktualisiert werden kann, können Sie den [Netzwerkrichtlinienserver (Network Policy Server, NPS) mit der Azure MFA-Erweiterung](howto-mfa-nps-extension.md) bereitstellen. Die NPS-Erweiterung fungiert als Adapter zwischen RADIUS-basierten Anwendungen und Azure AD MFA, um einen zweiten Authentifizierungsfaktor bereitzustellen.

#### <a name="common-integrations"></a>Allgemeine Integrationen

Viele Anbieter unterstützen inzwischen die SAML-Authentifizierung für ihre Anwendungen. Es wird empfohlen, diese Anwendungen nach Möglichkeit mit Azure AD zu verbinden und MFA durch bedingten Zugriff zu erzwingen. Wenn Ihr Anbieter keine moderne Authentifizierung unterstützt, können Sie die NPS-Erweiterung verwenden.
Häufig verwendete RADIUS-Client-Integrationen sind etwa Anwendungen wie [Remotedesktop-Gateways](howto-mfa-nps-extension-rdg.md) und [VPN-Server](howto-mfa-nps-extension-vpn.md). 

Andere können Folgendes umfassen:

- Citrix Gateway

  [Citrix Gateway](https://docs.citrix.com/en-us/advanced-concepts/implementation-guides/citrix-gateway-microsoft-azure.html#microsoft-azure-mfa-deployment-methods) unterstützt sowohl die Integration der RADIUS- und NPS-Erweiterung als auch eine SAML-Integration.

- Cisco VPN
  - Cisco VPN unterstützt sowohl die RADIUS- als auch [die SAML-Authentifizierung für SSO](../saas-apps/cisco-anyconnect.md).
  - Durch den Wechsel von der RADIUS-Authentifizierung zu SAML können Sie Cisco VPN integrieren, ohne die NPS-Erweiterung bereitzustellen.

- Alle VPNs

## <a name="deploy-azure-ad-mfa"></a>Bereitstellen von Azure AD MFA

Ihr MFA-Rolloutplan sollte eine Pilotbereitstellung enthalten, gefolgt von Bereitstellungsphasen, die innerhalb Ihrer Supportkapazität liegen. Beginnen Sie den Rollout durch Anwenden von Richtlinien für den bedingten Zugriff auf eine kleine Gruppe von Pilotbenutzern. Nach dem Evaluieren der Auswirkungen auf die Pilotbenutzer, den verwendeten Prozess und das Registrierungsverhalten können Sie entweder der Richtlinie weitere Gruppen hinzufügen oder vorhandenen Gruppen weitere Benutzer hinzufügen.

Führen Sie diese Schritte aus:

1. Erfüllen Sie die notwendigen Voraussetzungen.
1. Konfigurieren Sie ausgewählte Authentifizierungsmethoden.
1. Konfigurieren Sie Richtlinien für bedingten Zugriff.
1. Konfigurieren Sie die Einstellungen für die Lebensdauer von Sitzungen.
1. Konfigurieren Sie Azure AD MFA-Registrierungsrichtlinien 

## <a name="manage-azure-ad-mfa"></a>Verwalten von Azure AD MFA
Dieser Abschnitt enthält Informationen zur Berichterstellung und Problembehandlung bei Azure AD MFA.

### <a name="reporting-and-monitoring"></a>Berichterstellung und Überwachung

Azure AD enthält Berichte mit technischen und geschäftlichen Informationen, mit denen der Fortschritt der Bereitstellung verfolgt und geprüft werden kann, ob sich Benutzer bei MFA anmelden können. Sorgen Sie dafür, dass die Besitzer Ihrer geschäftlichen und technischen Anwendungen den Besitz dieser Berichte übernehmen und sie den Anforderungen Ihrer Organisation gemäß nutzen.

Mithilfe des Dashboards [Aktivität für Authentifizierungsmethoden](howto-authentication-methods-activity.md) können Sie die Registrierung und Nutzung von Authentifizierungsmethoden in Ihrer Organisation überwachen. So können Sie nachvollziehen, welche Methoden registriert werden und wie sie verwendet werden.

#### <a name="sign-in-report-to-review-mfa-events"></a>Anmeldebericht zum Überprüfen von MFA-Ereignissen

Die Azure AD-Anmeldeberichte enthalten Authentifizierungsdetails für Ereignisse, bei denen ein Benutzer zur mehrstufigen Authentifizierung aufgefordert wird, sowie Informationen dazu, ob Richtlinien für bedingten Zugriff verwendet wurden. Sie können PowerShell auch zur Erstellung von Berichten über Benutzer verwenden, die für MFA registriert sind. 

Die NPS-Erweiterung und AD FS-Protokolle können über **Sicherheit** > **MFA** > **Aktivitätsbericht** angezeigt werden.

Weitere Informationen und MFA-Berichte finden Sie unter [Überprüfen von Azure AD Multi-Factor Authentication-Ereignissen](howto-mfa-reporting.md#view-the-azure-ad-sign-ins-report).

### <a name="troubleshoot-azure-ad-mfa"></a>Problembehandlung bei Azure AD MFA
Informationen zu häufigen Problemen finden Sie unter [Problembehandlung bei Azure AD MFA](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues).

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen anderer Identitätsfeatures](../fundamentals/active-directory-deployment-plans.md)
