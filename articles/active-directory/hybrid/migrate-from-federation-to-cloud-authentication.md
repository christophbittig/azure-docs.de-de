---
title: Migrieren vom Verbund zur Cloudauthentifizierung in Azure Active Directory
description: Dieser Artikel enthält Informationen zur Umstellung Ihrer Hybrididentitätsumgebung vom Verbund auf die Cloudauthentifizierung.
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: baselden
author: BarbaraSelden
manager: MartinCo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd25928e245e6561be53ace29b017f476da7f1f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350247"
---
# <a name="migrate-from-federation-to-cloud-authentication"></a>Migrieren vom Verbund zur Cloudauthentifizierung 

In diesem Artikel erfahren Sie, wie Sie die Cloudbenutzerauthentifizierung entweder mit [Kennwort-Hashsynchronisierung (Password Hash Synchronization, PHS)](whatis-phs.md) oder [Passthrough-Authentifizierung (PTA)](how-to-connect-pta.md) von Azure Active Directory bereitstellen können. Wir zeigen hier den Anwendungsfall einer Umstellung von [Active Directory-Verbunddiensten (Active Directory Federation Services, AD FS)](whatis-fed.md) auf Cloudauthentifizierungsmethoden, doch der Leitfaden gilt im Wesentlichen auch für andere lokale Systeme.

Bevor Sie den Vorgang fortsetzen, schlagen wir vor, dass Sie sich in unserem Leitfaden zum [Wählen der richtigen Authentifizierungsmethode](choose-ad-authn.md) informieren und die für Ihre Organisation am besten geeigneten Methoden vergleichen.

Wir empfehlen, PHS für die Cloudauthentifizierung zu verwenden.

## <a name="staged-rollout"></a>Gestaffelter Rollout

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]

Gestaffelte Rollouts eignen sich ideal zum selektiven Testen von Benutzergruppen mit Cloudauthentifizierungsfunktionen (z. B Azure AD Multi-Factor Authentication (MFA), bedingter Zugriff, Identity Protection für kompromittierte Anmeldeinformationen, Identity Governance und weitere) vor der Umstellung Ihrer Domänen. 

Sehen Sie sich den Implementierungsplan für einen gestaffelten Rollout an, um die [unterstützten](how-to-connect-staged-rollout.md#supported-scenarios) und [nicht unterstützten Szenarien](how-to-connect-staged-rollout.md#unsupported-scenarios) zu verstehen. Wir empfehlen Tests mithilfe von gestaffeltem Rollout vor der Umstellung von Domänen.

Informationen zum Konfigurieren von gestaffeltem Rollout finden Sie im [Interaktiven Leitfaden für den gestaffelten Rollout](https://mslearn.cloudguides.com/guides/Test%20migration%20to%20cloud%20authentication%20using%20staged%20rollout%20in%20Azure%20AD) „Migration to cloud authentication using staged rollout in Azure AD“ (Migration zur Cloudauthentifizierung mithilfe eines gestaffelten Rollouts in Azure AD).

## <a name="migration-process-flow"></a>Ablauf des Migrationsprozesses

![Ablauf des Prozesses für die Migration zur Cloudauthentifizierung](media/deploy-cloud-user-authentication/process-flow-migration.png)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der Migration beginnen, müssen Sie diese Voraussetzungen erfüllen.

### <a name="required-roles"></a>Erforderliche Rollen

Für gestaffeltes Rollout müssen Sie ein globaler Administrator auf Ihrem Mandanten sein. 

Zum Aktivieren von nahtlosem einmaligem Anmelden für eine bestimmte Windows Active Directory-Gesamtstruktur müssen Sie ein Domänenadministrator sein.

### <a name="step-up-azure-ad-connect-server"></a>Durchführen eines Step-ups bei Azure AD Connect-Server

Installieren Sie [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect), oder [aktualisieren Sie auf die neueste Version](how-to-upgrade-previous-version.md). Wenn Sie bei Azure AD Connect-Server ein Step-up durchführen, verkürzt sich dadurch die Zeit für die Migration von AD FS zu den Cloudauthentifizierungsmethoden von potenziell Stunden auf Minuten. 

### <a name="document-current-federation-settings"></a>Dokumentieren der aktuellen Verbundeinstellungen

Führen Sie das Cmdlet [Get-MsolDomainFederationSettings](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login) aus, um nach Ihren aktuellen Verbundeinstellungen zu suchen. 

Überprüfen Sie alle Einstellungen, die für Ihren Verbundentwurf ggf. angepasst wurden, und die Bereitstellungsdokumentation. Suchen Sie vor allem nach Anpassungen in **PreferredAuthenticationProtocol**, **SupportsMfa** und **PromptLoginBehavior**.

### <a name="back-up-federation-settings"></a>Sichern von Verbundeinstellungen

Obwohl diese Bereitstellung keine anderen vertrauenden Seiten in Ihrer AD FS-Farm ändert, können Sie Ihre Einstellungen sichern:

 - Verwenden Sie das [AD FS Rapid Restore Tool](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) (AD FS-Tool für schnelle Wiederherstellung) von Microsoft zum Wiederherstellen einer vorhandenen Farm, oder erstellen Sie eine neue Farm.

- Exportieren Sie die Microsoft 365 Identity Platform-Vertrauensstellung der vertrauenden Seite und alle zugeordneten benutzerdefinierten Anspruchsregeln, die Sie hinzugefügt haben, anhand des folgenden PowerShell-Beispiels:

    ```powershell

    (Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"

    ```

## <a name="plan-the-project"></a>Planen des Projekts

Wenn Technologieprojekte scheitern, ist dies oft auf nicht erfüllte Erwartungen in Bezug auf Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie die [richtigen Beteiligten hinzuziehen](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) und dass die Rollen der Beteiligten im Projekt gut verstanden werden.

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Nach der Migration zur Cloudauthentifizierung ändert sich die Anmeldeerfahrung für Benutzer beim Zugriff auf Microsoft 365 und andere Ressourcen, die über Azure AD authentifiziert werden. Benutzern außerhalb des Netzwerks wird nur die Azure AD-Anmeldeseite angezeigt. 

Kommunizieren Sie proaktiv mit Ihren Benutzern darüber, wie sich die Nutzung ändern wird, wann sie sich ändert und wie sie Unterstützung erhalten, wenn sie auf Probleme stoßen.

### <a name="plan-the-maintenance-window"></a>Planen des Wartungsfensters

Nach der Domänenkonvertierung sendet Azure AD möglicherweise (bis zu vier Stunden lang) weiterhin einige Legacyauthentifizierungsanforderungen von Exchange Online an Ihre AD FS-Server. Der Grund für diese Verzögerung: Beim Exchange Online-Cache für die [Authentifizierung von Legacyanwendungen](../fundamentals/concept-fundamentals-block-legacy-authentication.md) kann es bis zu 4 Stunden dauern, bis er die Umstellung von Verbund- auf Cloudauthentifizierung erkennt.

Während dieses vierstündigen Fensters können Sie Benutzer wiederholt zur Eingabe von Anmeldeinformationen auffordern, wenn sie sich bei Anwendungen, die Legacyauthentifizierung verwenden, erneut authentifizieren. Obwohl der Benutzer die Authentifizierung für AD FS weiterhin erfolgreich durchführen kann, akzeptiert Azure AD sein ausgestelltes Token nicht mehr, weil diese Verbundvertrauensstellung jetzt entfernt wurde.

Vorhandene Legacyclients (Exchange ActiveSync, Outlook 2010/2013) sind davon nicht betroffen, weil Exchange Online einen Cache mit deren Anmeldeinformationen für einen festgelegten Zeitraum beibehält. Der Cache wird genutzt, um für den Benutzer im Hintergrund die erneute Authentifizierung durchzuführen. Der Benutzer muss nicht zu AD FS zurückwechseln. Anmeldeinformationen, die für diese Clients auf dem Gerät gespeichert wurden, dienen dazu, nach dem Löschen des Caches die erneute Authentifizierung im Hintergrund durchzuführen. Benutzer sollten aufgrund des Prozesses für die Domänenkonvertierung keine Aufforderungen zur Eingabe eines Kennworts erhalten.

Moderne Authentifizierungsclients (Office 2016-/Office 2013-, iOS- und Android-Apps) nutzen ein gültiges Aktualisierungstoken, um neue Zugriffstoken für den weiteren Zugriff auf Ressourcen abzurufen, anstatt zurück zu AD FS zu wechseln. Diese Clients sind „immun“ gegen Aufforderungen zur Kennworteingabe, die sich aus dem Prozess der Domänenkonvertierung ergeben. Die Clients werden auch ohne eine zusätzliche Konfiguration weiterhin funktionieren.

### <a name="plan-for-rollback"></a>Planen der Zurücksetzung

> [!TIP]
> Erwägen Sie die Planung der Umstellung von Domänen außerhalb der Geschäftszeiten, falls eine Zurücksetzung angefordert wird. 

Verwenden Sie zum Planen der Zurücksetzung die [dokumentierten aktuellen Verbundeinstellungen](#document-current-federation-settings), und informieren Sie sich in der [Dokumentation zu Verbundentwurf und Bereitstellung](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide). 

Im Zurücksetzungsvorgang sollte das Konvertieren von verwalteten Domänen in Verbunddomänen mithilfe des Cmdlets [Convert-MSOLDomainToFederated](/powershell/module/msonline/convert-msoldomaintofederated) enthalten sein. Konfigurieren Sie bei Bedarf zusätzliche Anspruchsregeln.

## <a name="migration-considerations"></a>Migrationsüberlegungen 

Hier sind wichtige Überlegungen zur Migration.

### <a name="plan-for-customizations-settings"></a>Planen von Anpassungseinstellungen 

Die Datei „onload.js“ kann in Azure AD nicht dupliziert werden. Wenn Ihre AD FS-Instanz stark angepasst wurde und auf bestimmten Anpassungseinstellungen in der Datei „onload.js“ basiert, überprüfen Sie, ob Azure AD Ihre aktuellen Anpassungsanforderungen erfüllen kann, und planen Sie entsprechend. Teilen Sie Ihren Benutzern diese bevorstehenden Änderungen mit.

#### <a name="sign-in-experience"></a>Anmeldevorgang

Sie können den Azure AD-Anmeldevorgang nicht anpassen. Unabhängig davon, wie sich Ihre Benutzer zuvor angemeldet haben, benötigen Sie einen vollqualifizierten Domänennamen wie Benutzerprinzipalname (User Principal Name, UPN) oder E-Mail für die Anmeldung bei Azure AD. 

#### <a name="organization-branding"></a>Organisationsbranding

Sie können [die Azure AD-Anmeldeseite anpassen](../fundamentals/customize-branding.md). Einige visuelle Änderungen von AD FS auf den Anmeldeseiten sollten nach der Konvertierung erwartet werden. 

>[!NOTE] 
>Organisationsbranding ist in kostenlosen Azure AD-Lizenzen nur dann verfügbar, wenn Sie eine Microsoft 365-Lizenz haben.

### <a name="plan-for-conditional-access-policies"></a>Planen von Richtlinien für bedingten Zugriff

Bewerten Sie, ob Sie zurzeit Folgendes verwenden: bedingten Zugriff für die Authentifizierung oder Zugriffssteuerungsrichtlinien in AD FS. 

Erwägen Sie, die AD FS-Zugriffssteuerungsrichtlinien durch die entsprechenden [Azure AD-Richtlinien für den bedingten Zugriff](../conditional-access/overview.md) und die [Clientzugriffsregeln für Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules) zu ersetzen. Sie können für den bedingten Zugriff entweder Azure AD-Gruppen oder lokale Gruppen verwenden.

**Deaktivieren Sie die Legacyauthentifizierung** – Erstellen Sie aufgrund des erhöhten Risikos, das mit Legacyauthentifizierungsprotokollen verbunden ist, eine [Richtlinie für bedingten Zugriff zum Blockieren von Legacyauthentifizierung](../conditional-access/howto-conditional-access-policy-block-legacy.md).

### <a name="plan-support-for-mfa"></a>Planen der Unterstützung für MFA

Jede Verbunddomäne in Azure AD enthält ein „SupportsMFA“-Flag. 

**Wenn das Flag „SupportsMFA“ auf „True“ festgelegt wurde**, leitet Azure AD Benutzer zur Durchführung von MFA in AD FS oder anderen Verbundanbietern um. Wenn ein Benutzer beispielsweise auf eine Anwendung zugreift, bei der eine Richtlinie für bedingten Zugriff konfiguriert wurde, für die MFA erforderlich ist, wird er zu AD FS umgeleitet. Durch Hinzufügen von Azure AD MFA als Authentifizierungsmethode in AD FS kann Azure AD MFA aufgerufen werden, sobald Ihre Konfigurationen abgeschlossen sind.

**Wenn das Flag „SupportsMFA“ auf „False“ festgelegt wurde**, verwenden Sie Azure MFA wahrscheinlich nicht. Sie verwenden wahrscheinlich Anspruchsregeln auf AD FS vertrauenden Seiten, um MFA auszulösen. 

Sie können den Status Ihres Flags **SupportsMFA** mit dem folgenden Windows PowerShell-Cmdlet überprüfen:
```powershell
 Get-MsolDomainFederationSettings –DomainName yourdomain.com
 ```

>[!NOTE] 
>Microsoft MFA Server nähert sich dem Ende der Supportlebensdauer, und wenn Sie ihn nutzen, müssen Sie zu Azure AD MFA wechseln. Weitere Informationen finden Sie in der **[Dokumentation zu „Migrieren von Microsoft MFA Server zu Azure Multi-Factor Authentication“](../authentication/how-to-migrate-mfa-server-to-azure-mfa.md)** .
Wenn Sie Azure AD MFA verwenden möchten, empfehlen wir die Verwendung von **[kombinierter Registrierung für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) und Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md)** , damit Ihre Benutzer ihre Authentifizierungsmethoden einmalig registrieren können. 

## <a name="plan-for-implementation"></a>Planen der Implementierung

Dieser Abschnitt enthält die Vorarbeiten, die auszuführen sind, bevor Sie Ihre Anmeldemethode umstellen und die Domänen konvertieren.

### <a name="create-necessary-groups-for-staged-rollout"></a>Erstellen der erforderlichen Gruppen für den gestaffelten Rollout

*Wenn Sie den gestaffelten Rollout nicht nutzen, überspringen Sie diesen Schritt.*

Erstellen Sie Gruppen für den gestaffelten Rollout. Sie müssen auch Gruppen für Richtlinien für bedingten Zugriff erstellen, wenn Sie sie hinzufügen möchten.

Wir empfehlen die Verwendung einer Gruppe, die in Azure AD gemastert wurde. Dies wird auch als „reine Cloudgruppe“ bezeichnet. Sie können Azure AD-Sicherheitsgruppen oder Microsoft 365-Gruppen sowohl für das Verschieben von Benutzern nach MFA als auch für Richtlinien für bedingten Zugriff verwenden. Weitere Informationen finden Sie im Artikel zum [Erstellen einer Azure AD-Sicherheitsgruppe](../fundamentals/active-directory-groups-create-azure-portal.md) und dieser [Übersicht über Microsoft 365-Gruppen für Administratoren](/microsoft-365/admin/create-groups/office-365-groups).

Die Mitglieder einer Gruppe werden automatisch für den gestaffelten Rollout aktiviert. Geschachtelte und dynamische Gruppen werden für gestaffelte Rollouts nicht unterstützt.

### <a name="pre-work-for-sso"></a>Vorarbeiten für einmaliges Anmelden

Die Version des von Ihnen verwendeten einmaligen Anmeldens ist abhängig vom Betriebssystem Ihres Geräts und dem Verknüpfungsstatus.

- **Für Windows 10, Windows Server 2016 und höhere Versionen** empfehlen wir die Verwendung von einmaligem Anmelden per [primärem Aktualisierungstoken (Primary Refresh Token, PRT)](../devices/concept-primary-refresh-token.md) mit [in Azure AD eingebundenen Geräten](../devices/concept-azure-ad-join.md), [in Azure AD eingebundenen Hybridgeräten](../devices/concept-azure-ad-join-hybrid.md) und [bei Azure AD registrierten Geräten](../devices/concept-azure-ad-register.md). 

- **Für Windows 7- und 8.1-Geräte** empfehlen wir die Verwendung von [nahtlosem einmaligem Anmelden](how-to-connect-sso.md) mit Domäneneinbindung zum Registrieren des Computers in Azure AD. Sie müssen diese Konten nicht wie bei Ihren Windows 10-Geräten synchronisieren. Allerdings müssen Sie diese [Vorarbeiten für nahtloses einmaliges Anmelden mithilfe von PowerShell](how-to-connect-staged-rollout.md#pre-work-for-seamless-sso) ausführen.

### <a name="pre-work-for-phs-and-pta"></a>Vorarbeiten für PHS und PTA

Führen Sie je nach Ihrer Wahl der Anmeldemethode die [Vorarbeiten für PHS](how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) oder [für PTA](how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication) aus.

## <a name="implement-your-solution"></a>Implementieren Ihrer Lösung

Schließlich stellen Sie die Anmeldemethode wie geplant auf PHS oder PTA um und konvertieren die Domänen vom Verbund in Cloudauthentifizierung. 

### <a name="using-staged-rollout"></a>Mithilfe von gestaffeltem Rollout

Wenn Sie gestaffeltes Rollout verwenden, führen Sie die Schritte unter den folgenden Links aus:

1. [Aktivieren Sie den gestaffelten Rollout für ein bestimmtes Feature in Ihrem Mandanten.](how-to-connect-staged-rollout.md#enable-staged-rollout)

2. Nach Abschluss der Tests: [Konvertieren Sie Domänen von Verbunddomänen in verwaltete Domänen](#convert-domains-from-federated-to-managed).

### <a name="without-using-staged-rollout"></a>Ohne gestaffelten Rollout 

Sie haben zwei Optionen zum Aktivieren dieser Änderung:

- **Option A:** Umstellen mithilfe von Azure AD Connect
  
  *Ist verfügbar, wenn Sie Ihre AD FS-/PingFederate-Umgebung ursprünglich mithilfe von Azure AD Connect konfiguriert haben*.

- **Option B:** Umstellen mithilfe von Azure AD Connect und PowerShell
 
  *Ist verfügbar, wenn Sie Ihre Verbunddomänen ursprünglich nicht mithilfe von Azure AD Connect konfiguriert haben oder wenn Sie Verbunddienste von Drittanbietern nutzen*.

Zum Auswählen einer dieser Optionen müssen Sie wissen, wie Ihre aktuellen Einstellungen lauten.

#### <a name="verify-current-azure-ad-connect-settings"></a>Überprüfen der aktuellen Azure AD Connect-Einstellungen

Melden Sie sich beim [Azure AD-Portal](https://aad.portal.azure.com/) an, wählen Sie **Azure AD Connect** aus, und überprüfen Sie die Einstellungen unter **BENUTZERANMELDUNG**, wie in dieser Abbildung gezeigt wird:

![Überprüfen der aktuellen Azure AD Connect-Einstellungen](media/deploy-cloud-user-authentication/current-user-settings-on-azure-ad-portal.png)


**So überprüfen Sie, wie der Verbund konfiguriert wurde:**

1. Öffnen Sie auf Ihrem Azure AD Connect-Server **Azure AD Connect**, und wählen Sie **Konfigurieren** aus.

2. Wählen Sie unter **Weitere Aufgaben > Verbund verwalten** die Option **Verbundkonfiguration anzeigen** aus. 

    ![Ansicht von „Verbund verwalten“](media/deploy-cloud-user-authentication/manage-federation.png)

    Wenn die AD FS-Konfiguration in diesem Abschnitt angezeigt wird, können Sie mit Sicherheit davon ausgehen, dass AD FS ursprünglich mit Azure AD Connect konfiguriert wurde. Ein Beispiel zeigt die folgende Abbildung:

    ![Anzeigen der AD FS-Konfiguration](media/deploy-cloud-user-authentication/federation-configuration.png)

    Wenn AD FS in den aktuellen Einstellungen nicht aufgeführt wird, müssen Sie Ihre Domänen mithilfe von PowerShell manuell von Verbundidentität auf verwaltete Identität umstellen.

#### <a name="option-a"></a>Option A

**Umstellen vom Verbund auf die neue Anmeldemethode mithilfe von Azure AD Connect**

1. Öffnen Sie auf Ihrem Azure AD Connect-Server **Azure AD Connect**, und wählen Sie **Konfigurieren** aus.

2. Wählen Sie auf der Seite **Weitere Aufgaben** die Option **Benutzeranmeldung ändern** und dann **Weiter** aus.
   
    ![Anzeigen von zusätzlichen Aufgaben](media/deploy-cloud-user-authentication/additional-tasks.png)

3. Geben Sie auf der Seite **Mit Azure AD verbinden** Ihre Anmeldeinformationen für ein Konto vom Typ „Globaler Administrator“ ein.

4. Auf der Seite **Benutzeranmeldung**:

    - Wenn Sie das Optionsfeld **Passthrough-Authentifizierung** auswählen, aktivieren Sie **Einmaliges Anmelden aktivieren**, und wählen Sie **Weiter** aus.

    -  Wenn Sie das Optionsfeld **Kennwort-Hashsynchronisierung** auswählen, müssen Sie das Kontrollkästchen **Benutzerkonten nicht konvertieren** aktivieren. Diese Option ist veraltet. Aktivieren Sie **Einmaliges Anmelden aktivieren**, und wählen Sie **Weiter** aus.

      ![Aktivieren von „Einmaliges Anmelden aktivieren“ auf der Seite „Benutzeranmeldung“](media/deploy-cloud-user-authentication/user-sign-in.png)

5. Geben Sie auf der Seite **Einmaliges Anmelden aktivieren** die Anmeldeinformationen eines Domänenadministratorkontos ein, und wählen Sie dann **Weiter**.

    ![Seite „Einmaliges Anmelden aktivieren“](media/deploy-cloud-user-authentication/enable-single-sign-on.png)

    Die Kontoanmeldeinformationen des Domänenadministrators sind erforderlich, um nahtloses einmaliges Anmelden zu aktivieren. Im Rahmen des Prozesses werden die folgenden Aktionen durchgeführt, für die diese erhöhten Berechtigungen benötigt werden:
      - In Ihrer lokalen Active Directory-Instanz wird ein Computerkonto mit dem Namen „AZUREADSSO“ (steht für „Azure AD“) erstellt.
      - Der Kerberos-Entschlüsselungsschlüssel des Computerkontos wird für Azure AD auf sichere Weise freigegeben.
      - Es werden zwei Kerberos-Dienstprinzipalnamen (Service Principal Names, SPNs) zur Darstellung der beiden URLs erstellt, die während der Azure AD-Anmeldung verwendet werden.

    Die Anmeldeinformationen des Domänenadministrators werden in Azure AD Connect oder Azure AD nicht gespeichert und verworfen, wenn der Prozess erfolgreich abgeschlossen wurde. Sie werden zum Aktivieren dieses Features verwendet.

6. Vergewissern Sie sich auf der Seite **Bereit zur Konfiguration**, dass das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, nachdem die Konfiguration abgeschlossen wurde** aktiviert ist. Wählen Sie anschließend **Konfigurieren**.

    ![Seite „Bereit zur Konfiguration“](media/deploy-cloud-user-authentication/ready-to-configure.png)

 > [!IMPORTANT] 
 > An diesem Punkt werden Ihre gesamten Verbunddomänen auf die verwaltete Authentifizierung umgestellt. Ihre ausgewählte Benutzer-Anmeldemethode ist die neue Authentifizierungsmethode.

1. Wählen Sie im Azure AD-Portal die Option **Azure Active Directory** und dann **Azure AD Connect**.

2. Überprüfen Sie diese Einstellungen:

      - **Verbund** ist auf **Deaktiviert** festgelegt.
      - **Nahtloses einmaliges Anmelden** ist auf **Aktiviert** festgelegt.
      - **Kennworthashsynchronisierung** ist auf **Aktiviert** festgelegt.

  ![ Erneutes Überprüfen der aktuellen Benutzereinstellungen](media/deploy-cloud-user-authentication/reverify-settings.png)

3. Falls Sie auf PTA umstellen, führen Sie die nächsten Schritte aus.

##### <a name="deploy-more-authentication-agents-for-pta"></a>Bereitstellen von weiteren Authentifizierungs-Agents für PTA

>[!NOTE]
> Für PTA müssen auf dem Azure AD Connect-Server und Ihrem lokalen Computer, auf dem Windows Server ausgeführt wird, einfache Agents bereitgestellt werden. Installieren Sie die Agents so nah wie möglich bei Ihren Active Directory-Domänencontrollern, um die Latenz zu verringern.

Für die meisten Kunden reichen zwei oder drei Authentifizierungs-Agents aus, um Hochverfügbarkeit und die erforderliche Kapazität bereitzustellen. Für einen Mandanten können maximal zwölf Agents registriert werden. Der erste Agent wird immer auf dem Azure AD Connect-Server selbst installiert. Informationen zu Einschränkungen und Bereitstellungsoptionen für Agents finden Sie unter [Azure AD-Passthrough-Authentifizierung: Aktuelle Einschränkungen](how-to-connect-pta-current-limitations.md).

1. Wählen Sie **Passthrough-Authentifizierung** aus.
2. Wählen Sie auf der Seite **Passthrough-Authentifizierung** die Schaltfläche **Herunterladen**.
3. Wählen Sie auf der Seite **Agent herunterladen** die Option **Bedingungen akzeptieren > Herunterladen**.

    Der Download von weiteren Authentifizierungs-Agents beginnt. Installieren Sie den sekundären Authentifizierungs-Agent auf einem mit einer Domäne verknüpften Server.

4. Führen Sie die Installation des Authentifizierungs-Agents durch. Während der Installation müssen Sie die Anmeldeinformationen eines Kontos vom Typ „Globaler Administrator“ eingeben.

    ![ Microsoft Azure AD Connect-Authentifizierungs-Agent](media/deploy-cloud-user-authentication/install-azure-ad-connect-installation-agent.png)

5. Wenn der Authentifizierungs-Agent installiert wurde, können Sie zur Seite für PTA-Integrität zurückkehren, um den Status der weiteren Agents zu überprüfen.

#### <a name="option-b"></a>Option B

**Umstellen vom Verbund auf die neue Anmeldemethode mithilfe von Azure AD Connect und PowerShell**

*Ist verfügbar, wenn Sie Ihre Verbunddomänen ursprünglich nicht mithilfe von Azure AD Connect konfiguriert haben oder wenn Sie Verbunddienste von Drittanbietern nutzen.*

Führen Sie auf Ihrem Azure AD Connect-Server die Schritte 1–5 in [Option A](#option-a) aus. Auf der Seite „Benutzeranmeldung“ werden Sie sehen, dass die Option **Nicht konfigurieren** vorausgewählt ist.

![ Weitere Informationen finden Sie auf der Seite „Benutzeranmeldung“unter der Option „Nicht konfigurieren“.](media/deploy-cloud-user-authentication/do-not-configure-on-user-sign-in-page.png)

1. Wählen Sie im Azure AD-Portal die Option **Azure Active Directory** und dann **Azure AD Connect**. 

2. Überprüfen Sie diese Einstellungen:

  - **Verbund** ist auf **Aktiviert** festgelegt.
  - **Nahtloses einmaliges Anmelden** ist auf **Deaktiviert** festgelegt.
  - **Kennworthashsynchronisierung** ist auf **Aktiviert** festgelegt.

    ![ Überprüfen der aktuellen Benutzereinstellungen im Azure-Portal](media/deploy-cloud-user-authentication/verify-current-user-settings-on-azure-ad-portal.png)

Führen Sie **nur für PTA** diese Schritte zum Installieren von weiteren PTA-Agent-Servern aus:

1. Wählen Sie im Azure AD-Portal die Option **Azure Active Directory** und dann **Azure AD Connect**.

2. Wählen Sie **Passthrough-Authentifizierung** aus. Überprüfen Sie, ob der Status **Aktiv** lautet.

    ![ Passthrough-Authentifizierungseinstellungen](media/deploy-cloud-user-authentication/pass-through-authentication-settings.png)

   Wenn der Authentifizierungs-Agent nicht aktiv ist, führen Sie diese [Problembehandlungsschritte](tshoot-connect-pass-through-authentication.md) aus, bevor Sie im nächsten Schritt den Prozess zur Domänenkonvertierung fortsetzen. Sie riskieren einen Authentifizierungsausfall, wenn Sie Ihre Domänen konvertieren, bevor Sie überprüft haben, ob Ihre PTA-Agents erfolgreich installiert wurden und ob deren Status im Azure-Portal **Aktiv** lautet.

3. [Stellen Sie weitere Authentifizierungs-Agents bereit](#deploy-more-authentication-agents-for-pta).

### <a name="convert-domains-from-federated-to-managed"></a>Konvertieren von Domänen von Verbunddomänen in verwaltete Domänen

**An diesem Punkt ist die Verbundauthentifizierung noch aktiv und für Ihre Domänen betriebsbereit**. Zum Fortsetzen der Bereitstellung müssen Sie jede Domäne von Verbundidentität in verwaltete Identität konvertieren.

>[!IMPORTANT]
> Sie müssen nicht alle Domänen gleichzeitig konvertieren. Sie können auch mit einer Testdomäne auf Ihrem Produktionsmandanten oder mit der Domäne beginnen, die bei Ihnen die niedrigste Anzahl von Benutzern aufweist.

**Führen Sie die Konvertierung durch, indem Sie das Azure AD PowerShell-Modul verwenden:**

1. Melden Sie sich in PowerShell an Azure AD an, indem Sie ein Konto vom Typ „Globaler Administrator“ verwenden.

2. Führen Sie folgenden Befehl aus, um die erste Domäne zu konvertieren:
   ```powershell
    Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
    ```
    Lesen Sie dazu [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication).

3. Wählen Sie im Azure AD-Portal **Azure Active Directory > Azure AD Connect** aus.

4. Stellen Sie mithilfe des folgenden Befehls sicher, dass die Domäne in eine verwaltete Domäne konvertiert wurde:
    ```powershell
    Get-MsolDomain -DomainName <domain name>
    ```
## <a name="complete-your-migration"></a>Abschließen Ihrer Migration

Führen Sie die folgenden Aufgaben aus, um die Registrierungsmethode zu überprüfen und den Konvertierungsprozess abzuschließen.

### <a name="test-the-new-sign-in-method"></a>Testen der neuen Anmeldemethode

Als für Ihren Mandanten die Verbundidentität verwendet wurde, wurden Benutzer von der Azure AD-Anmeldeseite in Ihre AD FS-Umgebung umgeleitet. Nachdem der Mandant für die Verwendung der neuen Anmeldemethode statt Verbundauthentifizierung konfiguriert wurde, werden Benutzer ab jetzt nicht mehr an AD FS umgeleitet. 

**Stattdessen melden sich Benutzer direkt auf der Azure AD-Anmeldeseite an.**

Führen Sie (bei Bedarf) die Schritte unter diesem Link: [Validate sign in with PHS/ PTA and seamless SSO](how-to-connect-staged-rollout.md#validation) (Überprüfen der Anmeldung mit PHS/PTA und nahtlosem einmaligem Anmelden) aus.

### <a name="remove-a-user-from-staged-rollout"></a>Entfernen eines Benutzers aus dem gestaffelten Rollout

Wenn Sie einen gestaffelten Rollout durchgeführt haben, sollten Sie daran denken, die Features dafür nach Abschluss der Übernahme zu deaktivieren. 

**Zum Deaktivieren des Features für gestaffelten Rollout stellen Sie den Schieberegler für das Steuerelement zurück in die Position „Aus“.**

### <a name="sync-userprincipalname-updates"></a>Synchronisieren von „userPrincipalName“-Updates

Updates des Attributs **UserPrincipalName**, für das der Synchronisierungsdienst aus der lokalen Umgebung verwendet wird, werden generell blockiert, sofern nicht die beiden folgenden Bedingungen erfüllt sind:

   - Der Benutzer befindet sich in einer Domäne mit verwalteter Identität (kein Verbund).
   - Dem Benutzer wurde keine Lizenz zugewiesen.

Informationen dazu, wie Sie dieses Feature überprüfen oder aktivieren, finden Sie im Artikel zur [Synchronisierung von userPrincipalName-Updates](how-to-connect-syncservice-features.md).

## <a name="manage-your-implementation"></a>Verwalten Ihrer Implementierung

### <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Durchführen des Rollovers des Kerberos-Entschlüsselungsschlüssels für das einmalige Anmelden

Wir empfehlen, den Rollover für den Kerberos-Entschlüsselungsschlüssel mindestens alle 30 Tage durchzuführen und so anzupassen, wie die Mitglieder der Active Directory-Domäne Kennwortänderungen übermitteln sollen. Weil an das Computerkontoobjekt „AZUREADSSO“ kein zugeordnetes Gerät angefügt ist, müssen Sie den Rollover manuell durchführen.

Weitere Informationen finden Sie in „Häufig gestellte Fragen“ unter [Wie kann ich einen Rollover des Kerberos-Entschlüsselungsschlüssels für das Computerkontos „AZUREADSSO“ durchführen?](how-to-connect-sso.md).

### <a name="monitoring-and-logging"></a>Überwachung und Protokollierung

Überwachen Sie die Server, auf denen die Authentifizierungs-Agents ausgeführt werden, um die Verfügbarkeit der Lösung aufrechtzuerhalten. Die Authentifizierungs-Agents stellen nicht nur allgemeine Leistungsindikatoren für die Server bereit, sondern machen auch Leistungsobjekte verfügbar, die Ihnen beim Nachvollziehen der Authentifizierungsstatistiken und -fehler als Hilfe dienen.

Authentifizierungs-Agents protokollieren Vorgänge in den Windows-Ereignisprotokollen, die unter „Anwendungs- und Dienstprotokolle“ gespeichert sind. Sie können auch die Protokollierung für die Problembehandlung aktivieren.

Zur Bestätigung der verschiedenen Aktionen, die beim gestaffelten Rollout durchgeführt wurden, können Sie [Ereignisse für PHS, PTA oder nahtloses einmaliges Anmelden überwachen](how-to-connect-staged-rollout.md#auditing).

### <a name="troubleshoot"></a>Problembehandlung

Ihr Supportteam muss wissen, wie jegliche Authentifizierungsprobleme behoben werden können, die entweder während oder nach dem Wechsel von „Verbund“ zu „Verwaltet“ auftreten können. Verwenden Sie die folgende Dokumentation zur Problembehandlung, um Ihrem Supportteam zu helfen, sich mit den allgemeinen Schritten zur Problembehandlung und den entsprechenden Maßnahmen vertraut zu machen, die Ihnen helfen können, das Problem zu isolieren und zu beheben.

-  [Azure AD PHS](tshoot-connect-password-hash-synchronization.md)
- [Azure AD PTA](tshoot-connect-pass-through-authentication.md)
- [Nahtloses einmaliges Anmelden mit Azure AD](tshoot-connect-sso.md)

## <a name="decommission-ad-fs-infrastructure"></a>Außerbetriebsetzen der AD FS-Infrastruktur

### <a name="migrate-app-authentication-from-ad-fs-to-azure-ad"></a>Migrieren der App-Authentifizierung von AD FS zu Azure AD

Die Migration erfordert eine Bewertung, wie die Anwendung lokal konfiguriert wird, und anschließend die Zuordnung der Konfiguration zu Azure AD.

Wenn Sie beabsichtigen, AD FS bei lokalen und SaaS-Anwendungen mithilfe des SAML/WS-FED-Oauth-Protokolls weiterhin zu nutzen, verwenden Sie nach Konvertierung der Domänen für Benutzerauthentifizierung sowohl AD FS als auch Azure AD. In diesem Fall können Sie Ihre lokalen Anwendungen und Ressourcen mit sicherem Hybridzugriff (Secure Hybrid Access, SHA) über den [Azure AD-Anwendungsproxy](../app-proxy/what-is-application-proxy.md) oder eine von [Azure AD-Partnerintegrationen](../manage-apps/secure-hybrid-access.md) schützen. Mithilfe des Anwendungsproxys oder eines unserer Partner können Sie einen sicheren Remotezugriff auf Ihre lokalen Anwendungen bieten. Benutzer profitieren nach dem [einmaligen Anmelden](../manage-apps/add-application-portal-setup-sso.md) von einer einfachen Verbindung mit ihren Anwendungen von jedem Gerät aus.

Sie können SaaS-Anwendungen, die zurzeit im Verbund mit AD FS sind, in Azure AD verschieben. Konfigurieren Sie sie neu für die Authentifizierung mit Azure AD entweder über einen integrierten Connector aus dem [Azure App-Katalog](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) oder durch [Registrieren der Anwendung in Azure AD](../develop/quickstart-register-app.md).

Weitere Informationen finden Sie unter: 

- [Verschieben der Anwendungsauthentifizierung von Active Directory-Verbunddiensten (AD FS) in Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) und
- [AD FS to Azure AD application migration playbook for developers](/samples/azure-samples/ms-identity-adfs-to-aad/ms-identity-dotnet-adfs-to-aad) (Playbook für Entwickler zur Migration von Anwendungen von AD FS zu Azure AD)

### <a name="remove-relying-party-trust"></a>Entfernen der Vertrauensstellung der vertrauenden Seite

Wenn Sie Azure AD Connect Health haben, können Sie über das Azure-Portal die [Nutzung überwachen](how-to-connect-health-adfs.md). Falls die Nutzung keine neue Authentifizierungsanforderung anzeigt und nachdem Sie überprüft haben, ob alle Benutzer und Clients über Azure AD erfolgreich authentifiziert werden konnten, können Sie die Vertrauensstellung der vertrauenden Seite von Microsoft 365 sicher entfernen.

Wenn Sie AD FS nicht für andere Zwecke nutzen (d. h. für andere Vertrauensstellungen der vertrauenden Seite), können Sie AD FS jetzt außer Betrieb nehmen.

## <a name="next-steps"></a>Nächste Schritte

- [Informieren Sie sich über das Migrieren von Anwendungen](../manage-apps/migration-resources.md)
- [Bereitstellen anderer Identitätsfeatures](../fundamentals/active-directory-deployment-plans.md)