---
title: 'Azure AD Connect: Cloudauthentifizierung über gestaffelten Rollout | Microsoft-Dokumentation'
description: In diesem Artikel wird erläutert, wie Sie mithilfe eines gestaffelten Rollouts eine Migration von der Verbundauthentifizierung zur Cloudauthentifizierung durchführen.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35dcb1125451e378d79aa7bab4c4e066ccab9acb
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044306"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout"></a>Migrieren zur Cloudauthentifizierung mithilfe eines gestaffelten Rollouts

Gestaffelte Rollouts ermöglichen Ihnen das selektive Testen von Benutzergruppen mit Cloudauthentifizierungsfunktionen (wie Azure AD Multi-Factor Authentication (MFA), bedingter Zugriff, Identity Protection für kompromittierte Anmeldeinformationen, Identity Governance usw.) vor der Umstellung Ihrer Domänen.  In diesem Artikel wird erläutert, wie Sie den Umstieg vornehmen. Bevor Sie mit dem gestaffelten Rollout beginnen, sollten Sie jedoch die Auswirkungen berücksichtigen, wenn mindestens eine der folgenden Bedingungen zutrifft:
    
-  Sie verwenden zurzeit einen lokalen Server für mehrstufige Authentifizierung. 
-  Sie verwenden Smartcards für die Authentifizierung. 
-  Ihr aktueller Server bietet bestimmte Features, die sich ausschließlich auf Verbundauthentifizierung beziehen.

Bevor Sie dieses Feature testen, empfehlen wir Ihnen, unseren Leitfaden zum Auswählen der richtigen Authentifizierungsmethode zu lesen. Weitere Informationen finden Sie in der Tabelle „Methodenvergleich“ unter [Auswählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](./choose-ad-authn.md#comparing-methods).

Einen Überblick über das Feature finden Sie hier: „Azure Active Directory: Was sind gestaffelte Rollouts?“ (Video):

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Voraussetzungen

-   Sie verfügen über einen Azure AD-Mandanten (Azure Active Directory) mit Verbunddomänen.

-   Sie haben sich entschieden, zu einer von zwei Optionen zu wechseln:
    - **Option A** - *Kennworthashsynchronisierung*.  Weitere Informationen finden Sie unter [Was ist Kennworthashsynchronisierung?](whatis-phs.md) 
    - **Option B** - *Passthrough-Authentifizierung*.  Weitere Informationen finden Sie unter [Worum handelt es sich bei der Passthrough-Authentifizierung?](how-to-connect-pta.md).  
    
    Für beide Optionen wird empfohlen, einmaliges Anmelden (Single Sign-On, SSO) zu aktivieren, um eine automatische Anmeldung zu ermöglichen. 
    Für in die Domäne eingebundene Windows 7- oder 8.1-Geräte wird die Verwendung von nahtlosem SSO empfohlen. Weitere Informationen finden Sie unter [Was ist die nahtlose einmalige Anmeldung?](how-to-connect-sso.md). 
    Für Windows 10, Windows Server 2016 und höhere Versionen wird die Verwendung von SSO über ein [primäres Aktualisierungstoken (Primary Refresh Token, PRT)](../devices/concept-primary-refresh-token.md) mit [in Azure AD eingebundenen Geräten](../devices/concept-azure-ad-join.md), [in Azure AD eingebundenen Hybridgeräten](../devices/concept-azure-ad-join-hybrid.md) oder mit Geräten empfohlen, die per „Geschäfts-, Schul- oder Unikonto hinzufügen“ persönlich registriert sind.

-   Sie haben alle geeigneten Richtlinien für Mandantenbranding und für bedingten Zugriff konfiguriert, die Sie für Benutzer benötigen, die zur Cloudauthentifizierung migriert werden.

-   Wenn Sie Azure AD Multi-Factor Authentication verwenden möchten, wird empfohlen, die [kombinierte Registrierung für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) und Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) zu verwenden, damit Ihre Benutzer ihre Authentifizierungsmethoden einmalig registrieren können. Hinweis: Bei Verwendung von SSPR zum Zurücksetzen oder Ändern des Kennworts mithilfe der Seite MyProfile im gestaffeltem Rollout muss Azure AD Connect den neuen Kennworthash synchronisieren, was nach dem Zurücksetzen bis zu 2 Minuten dauern kann.

-   Um dieses Feature für gestaffelten Rollout verwenden zu können, müssen Sie globaler Administrator für Ihren Mandanten sein.

-   Um *nahtloses einmaliges Anmelden* für eine bestimmte Active Directory-Gesamtstruktur aktivieren zu können, müssen Sie Domänenadministrator sein.

-  Wenn Sie Azure AD Hybrid oder Azure AD Join bereitstellen, müssen Sie ein Upgrade auf das Update Windows 10 1903 durchführen.


## <a name="supported-scenarios"></a>Unterstützte Szenarios

Die folgenden Szenarien werden für gestaffelten Rollout unterstützt. Das Feature funktioniert nur für:

- Benutzer, die für Azure AD mithilfe von Azure AD Connect bereitgestellt werden. Es gilt nicht für reine Cloudbenutzer.

- Datenverkehr bei der Benutzeranmeldung in Browsern und auf Clients mit *moderner Authentifizierung*. Für Anwendungen oder Clouddienste mit Legacyauthentifizierung werden wieder Verbundauthentifizierungsflows verwendet. Ein Beispiel ist Exchange Online mit deaktivierter moderner Authentifizierung oder Outlook 2010, das keine moderne Authentifizierung unterstützt.

- Die Gruppengröße ist derzeit auf 50.000 Benutzer beschränkt.  Wenn Sie über Gruppen verfügen, die größer als 50.000 Benutzer sind, empfiehlt es sich, diese Gruppe für ein gestaffeltes Rollout in mehrere Gruppen aufzuteilen.

- Windows 10 Hybrid Join oder Azure AD Join – Abrufen eines primären Aktualisierungstokens ohne Sichtverbindung mit dem Verbundserver für Windows 10 (Version 1903 und neuer), wenn der UPN des Benutzers routingfähig ist und das Domänensuffix in Azure AD überprüft wird.

## <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien

Die folgenden Szenarien werden für gestaffelten Rollout nicht unterstützt:

- Die Legacyauthentifizierung wie POP3 und SMTP wird nicht unterstützt.

- Bestimmte Anwendungen senden bei der Authentifizierung den Abfrageparameter „domain_hint“ an Azure AD. Diese Flows werden fortgesetzt, und für den gestaffelten Rollout aktivierte Benutzer verwenden weiterhin den Verbund für die Authentifizierung.

<!-- -->

- Administratoren können den Rollout der Cloudauthentifizierung mithilfe von Sicherheitsgruppen ausführen. Um Synchronisierungslatenzen zu vermeiden, wenn Sie lokale Active Directory-Sicherheitsgruppen verwenden, empfehlen wir Ihnen, Cloudsicherheitsgruppen zu verwenden. Die folgenden Bedingungen gelten:

    - Pro Feature können maximal 10 Gruppen verwendet werden. Das heißt, Sie können jeweils 10 Gruppen für *Kennworthashsynchronisierung*, *Passthrough-Authentifizierung* und *nahtloses SSO* verwenden.
    - Geschachtelte Gruppen werden *nicht unterstützt*. 
    - Dynamische Gruppen werden für den gestaffelten Rollout *nicht unterstützt*.
    - Kontaktobjekte innerhalb der Gruppe blockieren das Hinzufügen der Gruppe.

- Wenn Sie zum ersten Mal eine Sicherheitsgruppe für den gestaffelten Rollout hinzufügen, besteht eine Einschränkung auf 200 Benutzer, um ein UX-Timeout zu vermeiden. Nachdem Sie die Gruppe hinzugefügt haben, können Sie ihr nach Bedarf weitere Benutzer hinzufügen.

- Während für Benutzer ein gestaffelter Rollout ausgeführt wird, ist die Kennwortablaufrichtlinie auf 90 Tage festgelegt, wobei keine Möglichkeit zur Anpassung besteht. 

- Windows 10 Hybrid Join oder Azure AD Join – Abrufen eines primären Aktualisierungstokens für Windows 10-Versionen vor 1903. Dieses Szenario greift auf den WS-Trust-Endpunkt des Verbundservers zurück, auch wenn sich der anmeldende Benutzer im Bereich des gestaffelten Rollouts befindet.

- Windows 10 Hybrid Join oder Azure AD Join – Abrufen eines primären Aktualisierungstokens für alle Versionen, wenn der lokale UPN des Benutzers nicht routingfähig ist. Im Modus „Gestaffelter Rollout“ greift dieses Szenario auf den WS-Trust-Endpunkt zurück. Dies funktioniert jedoch nicht mehr, wenn die gestaffelte Migration abgeschlossen ist und die Benutzeranmeldung nicht mehr auf den Verbundserver angewiesen ist.

- Wenn Sie ein nicht persistentes VDI-Setup mit Windows 10 Version 1903 oder höher verwenden, müssen Sie in einer Verbunddomäne verbleiben. Das Verschieben in eine verwaltete Domäne wird für nicht persistente VDI nicht unterstützt. Weitere Informationen finden Sie unter [Geräteidentität und Desktopvirtualisierung](../devices/howto-device-identity-virtual-desktop-infrastructure.md).

- Wenn Sie über eine Windows Hello for Business-Hybridzertifikat-Vertrauensstellung mit Zertifikaten verfügen, die entweder über Ihren Verbundserver, der als Registrierungszertifizierungsstelle agiert oder über Smartcardbenutzer ausgestellt werden, wird kein gestaffelter Rollout unterstützt. 

- Die Autopilot-Registrierung wird beim gestaffelten Rollout nicht unterstützt. Benutzer, die für den gestaffelten Rollout aktiviert sind, verwenden zum Zeitpunkt der Autopilot-Registrierung weiterhin die Verbundauthentifizierung. Wenn Ihr Gerät über Windows 10 Version 1903 oder höher verfügt, erfolgen nach der Autopilot-Registrierung alle Authentifizierungsanforderungen über den gestaffelten Rollout. 

  >[!NOTE]
  >Die endgültige Umstellung von Verbundauthentifizierung auf Cloudauthentifizierung muss weiterhin mithilfe von Azure AD Connect oder PowerShell erfolgen. Bei einem gestaffelten Rollout werden Domänen nicht von Verbunddomänen auf verwaltete Domänen umgestellt.  Weitere Informationen zur Domänenumstellung finden Sie unter [Migrieren vom Verbund zur Kennworthashsynchronisierung für Azure Active Directory](./migrate-from-federation-to-cloud-authentication.md) und [Migrieren vom Verbund zur Passthrough-Authentifizierung für Azure Active Directory](./migrate-from-federation-to-cloud-authentication.md).
  
## <a name="get-started-with-staged-rollout"></a>Erste Schritte mit gestaffeltem Rollout

Zum Testen der *Kennworthashsynchronisierung* melden Sie sich mithilfe des gestaffelten Rollouts an und befolgen die Anweisungen für die Vorbereitung im nächsten Abschnitt.

Weitere Informationen dazu, welche PowerShell-Cmdlets verwendet werden sollten, finden Sie unter [Azure AD 2.0 Vorschau](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Vorbereitende Schritte für die Kennworthashsynchronisierung

1. Aktivieren Sie die *Kennworthashsynchronisierung* über die Seite [Optionale Features](how-to-connect-install-custom.md#optional-features) in Azure AD Connect. 

   ![Screenshot der Seite „Optionale Features“ in Azure Active Directory Connect](media/how-to-connect-staged-rollout/staged-1.png)

1. Stellen Sie sicher, dass ein vollständiger Zyklus der *Kennworthashsynchronisierung* ausgeführt wurde, damit alle Kennworthashes der Benutzer mit Azure AD synchronisiert wurden. Zum Überprüfen des Status der *Kennworthashsynchronisierung* können Sie die PowerShell-Diagnose unter [Problembehandlung der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](tshoot-connect-password-hash-synchronization.md) verwenden.

   ![Screenshot des AADConnect-Problembehandlungsprotokolls](./media/how-to-connect-staged-rollout/staged-2.png)

Wenn Sie *Passthrough-Authentifizierung* testen möchten, melden Sie sich mithilfe des gestaffelten Rollouts an, und aktivieren Sie diese dann, indem Sie die Anweisungen zu den vorbereitenden Schritten im nächsten Abschnitt befolgen.

## <a name="pre-work-for-pass-through-authentication"></a>Vorbereitende Schritte für Passthrough-Authentifizierung

1. Identifizieren Sie einen Server unter Windows Server 2012 R2 (oder höher), auf dem der *Passthrough-Authentifizierungs-Agent* ausgeführt werden soll. 

   Wählen Sie *nicht* den Azure AD Connect-Server aus.  Stellen Sie sicher, dass der Server in die Domäne eingebunden ist, die ausgewählten Benutzer bei Active Directory authentifizieren und mit Azure AD über ausgehende Ports/URLs kommunizieren kann. Weitere Informationen finden Sie im Abschnitt „Schritt 1: Überprüfen der Voraussetzungen“ in [Schnellstart: Nahtloses einmaliges Anmelden mit Azure AD](how-to-connect-sso-quick-start.md).

1. [Laden Sie den Microsoft Azure AD Connect-Authentifizierungs-Agent herunter](https://aka.ms/getauthagent), und installieren Sie ihn auf dem Server. 

1. Installieren Sie zusätzliche Authentifizierungs-Agents auf anderen Servern, um [Hochverfügbarkeit](how-to-connect-sso-quick-start.md) zu ermöglichen.

1. Stellen Sie sicher, dass Sie die Einstellungen für die intelligente Sperre ([Smart Lockout](../authentication/howto-password-smart-lockout.md)) entsprechend konfiguriert haben. Dadurch wird sichergestellt, dass die lokalen Active Directory-Konten Ihrer Benutzer nicht von böswilligen Akteuren gesperrt werden.

Es wird empfohlen, *nahtloses einmaliges Anmelden* unabhängig von der für den gestaffelten Rollout ausgewählten Anmeldemethode (*Kennworthashsynchronisierung* oder *Passthrough-Authentifizierung*) zu aktivieren. Um *nahtlose einmaliges Anmelden* zu aktivieren, befolgen Sie die Anweisungen zu den vorbereitenden Schritten im nächsten Abschnitt.

## <a name="pre-work-for-seamless-sso"></a>Vorbereitende Schritte für nahtloses einmaliges Anmelden

Aktivieren Sie *nahtloses einmaliges Anmelden* für die Active Directory-Gesamtstrukturen mithilfe von PowerShell. Wenn Sie über mehrere Active Directory-Gesamtstrukturen verfügen, aktivieren Sie das gleiche Feature für jede Gesamtstruktur einzeln. *Nahtloses einmaliges Anmelden* wird nur für Benutzer ausgelöst, die für das gestaffelte Rollout ausgewählt wurden. Dies wirkt sich nicht auf Ihre vorhandene Verbundeinrichtung aus.

Aktivieren Sie *nahtloses einmaliges Anmelden*, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich beim Azure AD Connect-Server an.

2. Navigieren Sie zum Ordner *%Programme%\\Microsoft Azure Active Directory Connect*.

3. Importieren Sie das PowerShell-Modul für *nahtloses SSO*, indem Sie den folgenden Befehl ausführen: 

   `Import-Module .\AzureADSSO.psd1`

4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem wird ein Bereich geöffnet, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.

5. Rufen Sie `Get-AzureADSSOStatus | ConvertFrom-Json` auf. Dieser Befehl zeigt eine Liste der Active Directory-Gesamtstrukturen (siehe Liste „Domänen“) an, für die diese Funktion aktiviert wurde. Standardmäßig ist die Option auf der Mandantenebene auf „false“ festgelegt.

   ![Beispiel der Windows PowerShell-Ausgabe](./media/how-to-connect-staged-rollout/staged-3.png)

6. Rufen Sie `$creds = Get-Credential` auf. Geben Sie an der Eingabeaufforderung die Anmeldeinformationen des Domänenadministrators für die vorgesehene Active Directory-Gesamtstruktur ein.

7. Rufen Sie `Enable-AzureADSSOForest -OnPremCredentials $creds` auf. Mit diesem Befehl wird das Computerkonto „AZUREADSSOACC“ vom lokalen Domänencontroller für die Active Directory-Gesamtstruktur erstellt, das für *nahtloses SSO* erforderlich ist.

8. *Nahtloses SSO* erfordert, dass sich URLs in der Intranetzone befinden. Informationen zum Bereitstellen dieser URLs mithilfe von Gruppenrichtlinien finden Sie unter [Schnellstart: Nahtloses einmaliges Anmelden mit Azure AD](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Sie können auch unsere [Bereitstellungspläne](https://aka.ms/SeamlessSSODPDownload) für *nahtloses SSO* herunterladen.

## <a name="enable-staged-rollout"></a>Aktivieren von gestaffeltem Rollout

Um den Rollout eines bestimmten Features (*Passthrough-Authentifizierung*, *Kennworthashsynchronisierung* oder *nahtloses SSO*) für ausgewählte Benutzer in einer Gruppe auszuführen, befolgen Sie die Anweisungen in den nächsten Abschnitten.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Aktivieren eines gestaffelten Rollouts eines bestimmten Features für Ihren Mandanten

Sie können Rollouts für eine der folgenden Optionen ausführen:

- **Option A** - *Kennworthashsynchronisierung* + *Nahtloses SSO*
- **Option B** - *Passthrough-Authentifizierung* + *Nahtloses SSO*
- **Nicht unterstützt** - *Kennworthashsynchronisierung* + *Passthrough-Authentifizierung* + *Nahtloses SSO*

Gehen Sie folgendermaßen vor:

1. Um auf die UX zuzugreifen, melden Sie sich am [Azure AD Portal](https://aka.ms/stagedrolloutux) an.

2. Wählen Sie den Link **Stufenweises Rollout für verwaltete Benutzeranmeldung aktivieren**.

   Wenn Sie z.B. *Option A* aktivieren möchten, stellen Sie den Schieberegler für die Steuerelemente **Kennworthashsynchronisierungs** und **Nahtloses SSO** auf die Position **Ein**, wie in den folgenden Abbildungen gezeigt.

   

  

3. Fügen Sie dem Feature die Gruppen hinzu, um *Passthrough-Authentifizierung* und *nahtloses SSO* zu aktivieren. Um ein UX-Timeout zu vermeiden, stellen Sie sicher, dass die Sicherheitsgruppen anfangs nicht mehr als 200 Mitglieder enthalten.

   

   >[!NOTE]
   >Die Mitglieder einer Gruppe werden automatisch für den gestaffelten Rollout aktiviert. Geschachtelte und dynamische Gruppen werden für gestaffelte Rollouts nicht unterstützt.
   >Wenn Sie eine neue Gruppe hinzufügen, werden die Benutzer in der Gruppe (bis zu 200 Benutzer für eine neue Gruppe) aktualisiert, damit die verwaltete Authentifizierung sofort verwendet werden kann. Wenn Sie eine Gruppe bearbeiten (Benutzer hinzufügen oder entfernen), kann es bis zu 24 Stunden dauern, bis die Änderungen wirksam werden.
   >Das nahtlose einmalige Anmelden gilt nur, wenn sich Benutzer in der Gruppe „Nahtloses einmaliges Anmelden“ und auch in einer PTA- oder Kennworthashsynchronisierungs-Gruppe befinden.

## <a name="auditing"></a>Überwachung

Wir haben Überwachungsereignisse für die verschiedenen Aktionen aktiviert, die wir für gestaffelte Rollouts ausführen:

- Überwachungsereignis: Sie aktivieren den gestaffelten Rollout für *Kennworthashsynchronisierung*, *Passthrough-Authentifizierung* oder *nahtloses SSO*.

  >[!NOTE]
  >Es wird ein Überwachungsereignis protokolliert, wenn *nahtloses SSO* mithilfe des gestaffelten Rollouts aktiviert wird.

  ![Bereich „Rolloutrichtlinie für Feature erstellen“, Registerkarte „Aktivität“](./media/how-to-connect-staged-rollout/staged-7.png)

  ![Bereich „Rolloutrichtlinie für Feature erstellen“, Registerkarte „Geänderte Eigenschaften“](./media/how-to-connect-staged-rollout/staged-8.png)

- Überwachungsereignis: Eine Gruppe wird dem Feature *Kennworthashsynchronisierung*, *Passthrough-Authentifizierung* oder *nahtloses SSO* hinzugefügt.

  >[!NOTE]
  >Es wird ein Überwachungsereignis protokolliert, wenn eine Gruppe der *Kennworthashsynchronisierung* für den gestaffelten Rollout hinzugefügt wird.

  ![Bereich „Gruppe zum Featurerollout hinzufügen“, Registerkarte „Aktivität“](./media/how-to-connect-staged-rollout/staged-9.png)

  ![Bereich „Gruppe zum Featurerollout hinzufügen“, Registerkarte „Geänderte Eigenschaften“](./media/how-to-connect-staged-rollout/staged-10.png)

- Überwachungsereignis: Ein der Gruppe hinzugefügter Benutzer wird für den gestaffelten Rollout aktiviert.

  ![Bereich „Benutzer zum Featurerollout hinzufügen“, Registerkarte „Aktivität“](media/how-to-connect-staged-rollout/staged-11.png)

  ![Bereich „Benutzer zum Featurerollout hinzufügen“, Registerkarte „Ziel(e)“](./media/how-to-connect-staged-rollout/staged-12.png)

## <a name="validation"></a>Überprüfen

Gehen Sie wie folgt vor, um die Anmeldung mit *Kennworthashsynchronisierung* oder *Passthrough-Authentifizierung* (Anmeldung mit Benutzername/Kennwort) zu testen:

1. Navigieren Sie im Extranet in einer privaten Browsersitzung zur Seite [Apps](https://myapps.microsoft.com), und geben Sie dann den UserPrincipalName (UPN) des Benutzerkontos ein, das für den gestaffelten Rollout ausgewählt wurde.

   Benutzer, die für einen gestaffelten Rollout vorgesehen sind, werden nicht auf Ihre Verbundanmeldeseite umgeleitet. Stattdessen werden sie aufgefordert, sich auf der Azure AD-Anmeldeseite mit Mandantenbranding anzumelden.

1. Stellen Sie sicher, dass die Anmeldung im [Azure AD-Bericht zu Anmeldeaktivitäten](../reports-monitoring/concept-sign-ins.md) erfolgreich angezeigt wird, indem Sie nach dem Benutzerprinzipalnamen (UserPrincipalName) filtern.

Gehen Sie wie folgt vor, um die Anmeldung mit *nahtlosem SSO* zu testen:

1. Navigieren Sie im Intranet in einer privaten Browsersitzung zur Seite [Apps](https://myapps.microsoft.com), und geben Sie dann den UserPrincipalName (UPN) des Benutzerkontos ein, das für den gestaffelten Rollout ausgewählt wurde.

   Benutzern, die für gestaffelten Rollout von *nahtlosem SSO* vorgesehen ist, wird die Meldung „Anmeldung wird versucht“ angezeigt, bevor sie automatisch angemeldet werden.

1. Stellen Sie sicher, dass die Anmeldung im [Azure AD-Bericht zu Anmeldeaktivitäten](../reports-monitoring/concept-sign-ins.md) erfolgreich angezeigt wird, indem Sie nach dem Benutzerprinzipalnamen (UserPrincipalName) filtern.

   Um Benutzeranmeldungen nachzuverfolgen, die weiterhin für Active Directory-Verbunddienste (AD FS) für ausgewählte Benutzer mit gestaffeltem Rollout auftreten, befolgen Sie die Anweisungen unter [Problembehandlung von AD FS: Ereignisse und Protokollierung](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Lesen Sie in der Dokumentation des Herstellers nach, wie dies für Drittanbieter von Verbunddiensten überprüft wird.

## <a name="monitoring"></a>Überwachung
Mithilfe der neuen Hybrid Auth-Arbeitsmappen im Azure-Portal können Sie die Benutzer und Gruppen, die während des gestaffelten Rollouts hinzugefügt oder daraus entfernt wurden, und die Benutzeranmeldungen während des gestaffelten Rollouts überwachen.

 ![Hybrid Auth-Arbeitsmappen](./media/how-to-connect-staged-rollout/staged-13.png)

## <a name="remove-a-user-from-staged-rollout"></a>Entfernen eines Benutzers aus dem gestaffelten Rollout

Durch Entfernen eines Benutzers aus der Gruppe wird der gestaffelte Rollout für diesen Benutzer deaktiviert. Um das Feature für gestaffelten Rollout zu deaktivieren, stellen Sie den Schieberegler für das Steuerelement zurück in die Position **Aus**.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Kann ich dieses Feature in der Produktionsumgebung verwenden?**

A: Ja, Sie können dieses Feature in Ihrem Produktionsmandanten verwendet werden. Wir empfehlen Ihnen jedoch, es zunächst in Ihrem Testmandanten auszuprobieren.

**F: Kann dieses Feature verwendet werden, um eine permanente „Koexistenz“ aufrechtzuerhalten, bei der einige Benutzer Verbundauthentifizierung und andere Cloudauthentifizierung verwenden?**

A: Nein, dieses Feature ist für das Testen der Cloudauthentifizierung konzipiert. Nachdem Sie einige Gruppen von Benutzern erfolgreich getestet haben, sollten Sie zur Cloudauthentifizierung wechseln. Von der Verwendung eines dauerhaften gemischten Zustands wird abgeraten, weil dieser Ansatz zu unerwarteten Authentifizierungsflows führen könnte.

**F: Kann PowerShell verwendet werden, um einen gestaffelten Rollout auszuführen?**

A: Ja. Informationen zum Verwenden von PowerShell zum Ausführen eines gestaffelten Rollouts finden Sie unter [Azure AD Vorschau](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="next-steps"></a>Nächste Schritte
- [Azure AD 2.0 Vorschau](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout )
- [Ändern der Anmeldemethode in die Kennworthashsynchronisierung](./migrate-from-federation-to-cloud-authentication.md)
- [Ändern der Anmeldemethode in die Passthrough-Authentifizierung](./migrate-from-federation-to-cloud-authentication.md)
- [Interaktiver Leitfaden für den gestaffelten Rollout](https://mslearn.cloudguides.com/en-us/guides/Test%20migration%20to%20cloud%20authentication%20using%20staged%20rollout%20in%20Azure%20AD)
