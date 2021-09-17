---
title: Migrieren von Azure MFA-Server zu Azure Multi-Factor Authentication – Azure Active Directory
description: Schritt-für-Schritt-Anleitung für die Migration lokaler Azure MFA-Server zu Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4415e7ce86d2beb9e2903f23d0b6fa9ac7d3ec04
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597833"
---
# <a name="migrate-from-azure-mfa-server-to-azure-multi-factor-authentication"></a>Migrieren von Azure MFA-Server zu Azure Multi-Factor Authentication

Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist wichtig für den Schutz Ihrer Infrastruktur und Ressourcen vor böswilligen Akteuren. Azure MFA-Server ist für neue Bereitstellungen nicht verfügbar und wird bald als veraltet angesehen. Kunden, die MFA-Server verwenden, sollten zur cloudbasierten mehrstufigen Authentifizierung durch Azure Active Directory (Azure AD) wechseln. In dieser Dokumentation wird davon ausgegangen, dass Sie eine Hybridumgebung mit folgenden Eigenschaften haben:

- Sie verwenden MFA-Server für die mehrstufige Authentifizierung.
- Sie verwenden einen Verbund auf Azure AD mit Active Directory-Verbunddiensten (AD FS) oder einem Verbundprodukt eines anderen Identitätsanbieters.
  - Dieser Artikel ist zwar auf AD FS bezogen, für andere Identitätsanbieter gelten aber ähnliche Schritte.
- Ihr MFA-Server ist mit AD FS integriert. 
- Möglicherweise verwenden manche Ihrer Anwendungen AD FS für die Authentifizierung.

Abhängig von Ihrem Ziel gibt es mehrere mögliche Endzustände bei Ihrer Migration.

| <br> | Ziel: Ausschließlich MFA-Server außer Betrieb setzen | Ziel: Außerbetriebsetzen von MFA-Server und Migration zu Azure AD-Authentifizierung | Ziel: MFA-Server und AD FS außer Betrieb setzen |
|------|------------------------------------|-------------------------------------------------------------------|-----------------------------------------|
|MFA-Anbieter | Den MFA-Anbieter von MFA-Server in Azure AD MFA ändern. | Den MFA-Anbieter von MFA-Server in Azure AD MFA ändern. |   Den MFA-Anbieter von MFA-Server in Azure AD MFA ändern. |
|Benutzerauthentifizierung  |Weiterhin einen Verbund für Azure AD-Authentifizierung verwenden. | Zu Azure AD mit Password Hash Synchronization (bevorzugt) oder Passthrough-Authentifizierung **und** Single Sign-On wechseln.| Zu Azure AD mit Password Hash Synchronization (bevorzugt) oder Passthrough-Authentifizierung **und** Single Sign-On wechseln. |
|Anwendungsauthentifizierung | Weiterhin AD FS Authentifizierung für Ihre Anwendungen verwenden. | Weiterhin AD FS Authentifizierung für Ihre Anwendungen verwenden. | Verschieben Sie Apps nach Azure AD, bevor Sie zu Azure MFA migrieren. |

Verlagern Sie wenn möglich sowohl Ihre MFA als auch Ihre Benutzerauthentifizierung nach Azure. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Wechseln zu Azure AD MFA und Azure AD Benutzerauthentifizierung](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md). 

Wenn Sie Ihre Benutzerauthentifizierung nicht verlagern können, lesen Sie die Schritt-für-Schritt-Anleitung für [Die Umstellung auf Azure AD MFA mit Verbund](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md).

## <a name="prerequisites"></a>Voraussetzungen

- AD FS-Umgebung (erforderlich, wenn Sie nicht alle Ihre Apps vor der Migration der MFA zu Azure AD migrieren)
  - Upgrade auf AD FS für Windows Server 2019, Farmverhaltensebene (FBL, Farm Behavior Level) 4. Dadurch können Sie für einen nahtloseren Benutzerübergang den Authentifizierungsanbieter basierend auf der Gruppenmitgliedschaft auswählen. Es ist zwar möglich, auf Grundlage von AD FS für Windows Server 2016 FBL 3 zu migrieren, aber es ist für Benutzer nicht so nahtlos. Während der Migration und bis zu ihrem Abschuss werden Benutzer aufgefordert, einen Authentifizierungsanbieter (MFA-Server oder Azure MFA) auszuwählen. 
- Berechtigungen
  - Die Rolle „Unternehmensadministrator“ in Active Directory, um AD FS Farm für Azure AD MFA zu konfigurieren
  - Die Rolle „globaler Administrator“ in Azure AD, um die Konfiguration von Azure AD mithilfe von Azure AD PowerShell durchzuführen


## <a name="considerations-for-all-migration-paths"></a>Überlegungen für alle Migrationspfade

Die Migration von MFA-Server zu Azure AD MFA umfasst mehr als nur das Verschieben der registrierten MFA-Telefonnummern. Der MFA-Server von Microsoft kann in viele Systeme integriert werden, und Sie müssen bedenken, auf welche Weise diese Systeme MFA-Server verwenden, um die besten Vorgehensweisen für die Integration mit Azure AD MFA zu erkennen. 

### <a name="migrating-mfa-user-information"></a>Migrieren von MFA-Benutzerinformationen

Gängige Möglichkeiten zum Verschieben von Benutzern in Batches sind das Verschieben nach Regionen, Abteilungen oder Rollen wie etwa Administratoren. Stellen Sie unabhängig von der gewählten Strategie sicher, dass Sie Benutzer iterativ verschieben, beginnend mit Test- und Pilotgruppen, und dass Sie über einen Rollbackplan verfügen. 

Sie können zwar die registrierten MFA-Telefonnummern und Hardwaretoken von Benutzern, nicht aber Geräteregistrierungen wie deren Microsoft Authenticator App-Einstellungen migrieren. Benutzer müssen sich registrieren, ein neues Konto in der Authenticator-App hinzufügen und das alte Konto entfernen. 

Damit Benutzer das neu hinzugefügte Konto von dem alten, an den MFA-Server gebundenen Konto unterscheiden können, stellen Sie sicher, dass ein gut davon zu unterscheidender Kontoname für die mobile App auf dem MFA-Server verwendet wird. Beispielsweise wurde der Kontoname, der bei MFA-Server unter „Mobile App“ erscheint, in „OnPrem-MFA-Server“ umbenannt. Der Kontoname auf der Authenticator-App ändert sich mit der nächsten Pushbenachrichtigung an den Benutzer. 

Die Migration von Telefonnummern kann auch dazu führen, dass ungenutzte Nummern migriert werden, wodurch die Wahrscheinlichkeit steigt, dass Benutzer telefonbasierte MFA verwenden, anstatt sicherere Methoden wie Microsoft Authenticator im kennwortlosen Modus einzurichten. Daher wird empfohlen, dass sich alle Benutzer unabhängig vom gewählten Migrationspfad für [kombinierte Sicherheitsinformationen](howto-registration-mfa-sspr-combined.md) registrieren.


#### <a name="migrating-hardware-security-keys"></a>Migrieren von Hardwaresicherheitsschlüsseln

Azure AD bietet Unterstützung für OATH-Hardwaretoken. Um die Token vom MFA-Server zu Azure AD MFA zu migrieren, müssen die [Token in Azure AD mithilfe einer CSV-Datei hochgeladen werden](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview), die häufig als „Ausgangsdatei“ bezeichnet wird. Die Ausgangsdatei enthält die geheimen Schlüssel und Tokenseriennummern sowie andere erforderliche Informationen, die zum Hochladen der Token in Azure AD erforderlich sind. 

Wenn Sie nicht mehr über die Ausgangsdatei mit den geheimen Schlüsseln verfügen, ist es nicht möglich, die geheimen Schlüssel aus MFA-Server zu exportieren. Wenn Sie keinen Zugriff mehr auf die geheimen Schlüssel haben, fragen Sie bei Ihrem Hardwareanbieter um Unterstützung.

Das Webdienst-SDK von MFA-Server kann verwendet werden, um die Seriennummer für alle OATH-Token zu exportieren, die einem bestimmten Benutzer zugewiesen sind. Anhand dieser Informationen und der Ausgangsdatei können IT-Administratoren die Token in Azure AD importieren und dem angegebenen Benutzer das OATH-Token basierend auf der Seriennummer zuweisen. Der Benutzer muss zum Zeitpunkt des Imports auch kontaktiert werden, um OTP-Informationen vom Gerät zum Abschließen der Registrierung anzufordern. Bitte lesen Sie das Thema „GetUserInfo > userSettings > OathTokenSerialNumber“ in der Hilfedatei zum Multi-Factor Authentication Server auf Ihrem MFA-Server. 


### <a name="additional-migrations"></a>Zusätzliche Migrationen

Die Entscheidung, von MFA-Server zu Azure MFA zu migrieren, öffnet die Tür für andere Migrationen. Ob Sie weitere Migrationen durchführen, hängt von vielen Faktoren ab, insbesondere:

- Ihrer Bereitschaft, Azure AD-Authentifizierung für Benutzer zu verwenden
- Ihrer Bereitschaft, Ihre Anwendungen auf Azure AD zu migrieren

Da MFA-Server sowohl mit Anwendungen als auch der Benutzerauthentifizierung integriert ist, sollten Sie erwägen, beide Funktionen als Teil Ihrer MFA-Migration nach Azure zu verschieben und schließlich AD FS außer Betrieb zu nehmen. 

Empfehlungen: 

- Verwenden sie Azure AD zur Authentifizierung für stabilere Sicherheit und Governance.
- Migrieren Sie Anwendungen nach Möglichkeit zu Azure AD.

Informationen zum Auswählen der Benutzerauthentifizierungsmethode, die für Ihre Organisation am besten geeignet ist, finden Sie unter [Wählen der richtigen Authentifizierungsmethode für Ihre Azure AD-Hybrididentitätslösung](../hybrid/choose-ad-authn.md). Es wird empfohlen, die Kennworthashsynchronisierung (Password Hash Synchronization, PHS) zu verwenden.

### <a name="passwordless-authentication"></a>Kennwortlose Authentifizierung

Im Rahmen der Einführung von Microsoft Authenticator als zweitem Authentifizierungsfaktor für Benutzer wird empfohlen, die kennwortlose Anmeldung per Telefon im Rahmen ihrer Registrierung zu aktivieren. Weitere Informationen, einschließlich anderer kennwortloser Methoden wie FIDO und Windows Hello for Business, finden Sie unter [Planen einer Bereitstellung für die kennwortlose Authentifzierung mit Azure AD](howto-authentication-passwordless-deployment.md#plan-for-and-deploy-the-microsoft-authenticator-app).

### <a name="microsoft-identity-manager-self-service-password-reset"></a>Self-Service-Kennwortrücksetzung mit Microsoft Identity Manager 

Microsoft Identity Manager (MIM) SSPR kann MFA-Server verwenden, um SMS-Einmalpasscodes als Teil des Kennwortzurücksetzungsflows einzusetzen. MIM kann nicht für die Verwendung von Azure MFA konfiguriert werden. Wir empfehlen, die Migration Ihres SSPR-Diensts auf Azure AD SSPR zu erwägen.

Die MFA-Registrierung der Benutzer bei Azure MFA bietet Ihnen die Gelegenheit, die kombinierte Registrierungserfahrung zur Registrierung bei Azure AD SSPR zu nutzen.


### <a name="radius-clients-and-azure-ad-mfa"></a>RADIUS-Clients und Azure AD MFA

MFA-Server unterstützt RADIUS, um MFA für Anwendungen und Netzwerkgeräte zu nutzen, die das Protokoll unterstützen. Wenn Sie RADIUS mit MFA-Server verwenden, empfiehlt es sich, Clientanwendungen auf moderne Protokolle wie SAML, Open ID Verbinden oder OAuth auf Azure AD umzustellen. Wenn die Anwendung nicht aktualisiert werden kann, können Sie den Netzwerkrichtlinienserver (Network Policy Server, NPS) mit der Azure MFA-Erweiterung bereitstellen. Die NPS-Erweiterung fungiert als Adapter zwischen RADIUS-basierten Anwendungen und Azure AD MFA, um einen zweiten Authentifizierungsfaktor bereitzustellen. Dadurch können Sie Ihre RADIUS-Clients auf Azure MFA migrieren und den Betrieb Ihres MFA-Servers beenden.

#### <a name="important-considerations"></a>Wichtige Hinweise

Es gibt Einschränkungen bei der Verwendung von NPS für RADIUS-Clients, und es wird empfohlen, bei allen RADIUS-Clients zu erwägen, ob Sie sie auf moderne Authentifizierungsprotokolle aktualisieren können. Informieren Sie sich beim Dienstanbieter über unterstützte Produktversionen und deren Funktionen. 

- Die NPS-Erweiterung verwendet nicht Azure AD-Richtlinien für bedingten Zugriff. Wenn Sie bei RADIUS bleiben und die NPS-Erweiterung verwenden, muss der Benutzer für alle Authentifizierungsanforderungen, die an NPS gerichtet werden, MFA ausführen.
- Benutzer müssen sich für Azure AD MFA registrieren, bevor sie die NPS-Erweiterung verwenden können. Andernfalls kann die Erweiterung den Benutzer nicht authentifizieren, Was zu Anrufen beim Helpdesk führen kann.
- Wenn die NPS-Erweiterung MFA anfordert, wird die MFA-Anforderung an die MFA-Standardmethode des Benutzers gesendet. 
  - Da die Anmeldung in Drittanbieteranwendungen erfolgt, ist es unwahrscheinlich, dass ein Benutzer visuelle Benachrichtigungen darüber sieht, dass MFA erforderlich ist und eine Anforderung an sein Gerät gesendet wurde.
  - Während der MFA-Anforderung müssen Benutzer Zugriff auf seine Standardauthentifizierungsmethode haben, um die MFA-Anforderung zu erfüllen. Sie können keine alternative Methode auswählen. Die Standardauthentifizierungsmethode wird auch dann verwendet, wenn sie in den Mandantenauthentifizierungsmethoden und MFA-Richtlinien deaktiviert wurde.
  - Benutzer können ihre MFA-Standardmethode auf der Seite „Sicherheitsinformationen“ (aka.ms/mysecurityinfo) ändern.
- Verfügbare MFA-Methoden für RADIUS-Clients werden von den Clientsystemen gesteuert, die die RADIUS-Zugriffsanforderungen senden.
  - MFA-Methoden, die Benutzereingaben erfordern, nachdem Benutzer ein Kennwort eingegeben haben, können nur mit Systemen verwendet werden, die Access-Challenge-Antworten mit RADIUS unterstützen. Eingabemethoden sind etwa OTP, Hardware-OATH-Token oder die Microsoft Authenticator-App.
  - Einige Systeme beschränken möglicherweise die verfügbaren MFA-Methoden auf Microsoft Authenticator-Pushbenachrichtigungen und Telefonanrufe.


>[!NOTE]
>Der Kennwortverschlüsselungsalgorithmus, der zwischen dem RADIUS-Client und dem NPS-System verwendet wird, und die Eingabemethoden, die der Client verwenden kann, wirken sich darauf aus, welche Authentifizierungsmethoden verfügbar sind. Weiterführende Informationen finden Sie unter [Bestimmen der Authentifizierungsmethoden, die Ihre Benutzer verwenden können](howto-mfa-nps-extension.md). 

Häufig verwendete RADIUS-Client-Integrationen sind etwa Anwendungen wie [Remotedesktop-Gateways](howto-mfa-nps-extension-rdg.md) und [VPN-Server](howto-mfa-nps-extension-vpn.md). Andere können Folgendes umfassen:

- Citrix Gateway
  - [Citrix Gateway](https://docs.citrix.com/en-us/citrix-gateway) unterstützt sowohl die Integration der RADIUS- und NPS-Erweiterung als auch eine SAML-Integration.
- Cisco VPN
  - Cisco VPN unterstützt sowohl die RADIUS- als auch [die SAML-Authentifizierung für SSO](../saas-apps/cisco-anyconnect.md).
  - Durch den Wechsel von der RADIUS-Authentifizierung zu SAML können Sie Cisco VPN integrieren, ohne die NPS-Erweiterung bereitzustellen.
- Alle VPNs
  - Es wird empfohlen, Ihr VPN nach Möglichkeit als SAML-App zu föderieren. Dadurch können Sie den bedingten Zugriff verwenden. Siehe dazu die [Liste von VPN-Anbietern, die in den Azure AD-App-Katalog integriert sind](../manage-apps/secure-hybrid-access.md#secure-hybrid-access-through-azure-ad-partner-integrations).


### <a name="resources-for-deploying-nps"></a>Ressourcen zum Bereitstellen von NPS

- [Hinzufügen einer neuen NPS-Infrastruktur](/windows-server/networking/technologies/nps/nps-top)
- [Bewährte Methoden vor der Bereitstellung](https://www.youtube.com/watch?v=qV9wddunpCY)
- [Skript für die Integritätsprüfung der MFA-NPS-Erweiterung](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)
- [Integrieren einer vorhandenen NPS-Infrastruktur in Azure AD MFA](howto-mfa-nps-extension-vpn.md)

## <a name="next-steps"></a>Nächste Schritte

- [Wechsel zu Azure AD MFA mit Verbund](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md)
- [Wechsel zu Azure AD MFA und Azure AD Benutzerauthentifizierung](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)


