---
title: 'Azure AD Connect: Nahtloses einmaliges Anmelden: Schnellstart | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie Ihre ersten Schritte für das nahtlose einmalige Anmelden von Azure Active Directory ausführen.
services: active-directory
keywords: Was ist Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f691f2c6cff0abec7454282a239e8ede47277c6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068111"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Nahtloses einmaliges Anmelden mit Azure Active Directory: Schnellstart

## <a name="deploy-seamless-single-sign-on"></a>Bereitstellen des nahtlosen einmaligen Anmeldens

Mit dem nahtlosen einmaligen Anmelden von Azure Active Directory (Azure AD Seamless Single Sign-On) werden Benutzer automatisch angemeldet, wenn sie an ihren mit dem Unternehmensnetzwerk verbundenen Unternehmens-Desktops arbeiten. Nahtloses SSO ermöglicht Ihren Benutzern einen einfachen Zugriff auf Ihre cloudbasierten Anwendungen, ohne dass zusätzliche lokale Komponenten erforderlich sind.

Um die nahtlose einmalige Anmeldung bereitzustellen, führen Sie die folgenden Schritte aus:

## <a name="step-1-check-the-prerequisites"></a>Schritt 1: Überprüfen der Voraussetzungen

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt werden:

* **Richten Sie Ihren Azure AD Connect-Server ein:** Wenn Sie die [Pass-Through-Authentifizierung](how-to-connect-pta.md) als Anmeldemethode verwenden, ist keine zusätzliche Überprüfung der Voraussetzungen erforderlich. Wenn die [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) Ihre Anmeldemethode ist und eine Firewall zwischen Azure AD Connect und Azure AD vorhanden ist, sollten Sie Folgendes sicherstellen:
   - Sie verwenden Azure AD Connect 1.1.644.0 oder eine höhere Version. 
   - Wenn es Ihre Firewall oder Ihr Proxy zulässt, fügen Sie die Verbindungen zur Zulassungsliste für **\*.msappproxy.net**-URLs über Port 443 hinzu. Wenn Sie anstelle eines Platzhalters für die Proxykonfiguration eine bestimmte URL benötigen, können Sie **tenantid.registration.msappproxy.net** konfigurieren, wobei „tenantid“ die GUID des Mandanten ist, in dem Sie das Feature konfigurieren. Wenn in Ihrer Organisation URL-basierte Proxyausnahmen nicht möglich sind, können Sie stattdessen den Zugriff auf die [IP-Adressbereiche der Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653) zulassen, die wöchentlich aktualisiert werden. Diese Voraussetzung gilt nur, wenn Sie das Feature aktivieren. Sie ist für tatsächliche Benutzeranmeldungen nicht erforderlich.

    >[!NOTE]
    >Die Azure AD Connect-Versionen 1.1.557.0, 1.1.558.0, 1.1.561.0 und 1.1.614.0 weisen ein Problem in Bezug auf die Kennworthashsynchronisierung auf. Wenn Sie die Kennworthashsynchronisierung _nicht_ zusammen mit der Passthrough-Authentifizierung verwenden möchten, finden Sie weitere Informationen dazu in den [Versionshinweisen zu Azure AD Connect](./reference-connect-version-history.md).
    
    >[!NOTE]
    >Wenn Sie einen ausgehenden HTTP-Proxy verwenden, vergewissern Sie sich, dass diese URL, autologon.microsoftazuread-sso.com, in der Liste der zulässigen URLs enthalten ist. Sie sollten diese URL explizit angeben, da der Platzhalter möglicherweise nicht akzeptiert wird. 

* **Verwenden Sie eine unterstützte Azure AD Connect-Topologie:** Stellen Sie sicher, dass Sie eine der [hier](plan-connect-topologies.md) beschriebenen, von Azure AD Connect unterstützten Topologien verwenden.

    >[!NOTE]
    >Nahtloses einmaliges Anmelden (Single Sign-On, SSO) unterstützt mehrere AD-Gesamtstrukturen, unabhängig davon, ob AD-Vertrauensstellungen zwischen ihnen vorhanden sind.

* **Richten Sie Anmeldeinformationen des Domänenadministrators ein:** Sie benötigen Anmeldeinformationen des Domänenadministrators für jede Active Directory-Gesamtstruktur, die:
    * Sie über Azure AD Connect mit Azure AD synchronisieren.
    * Benutzer enthält, für die Sie nahtloses SSO aktivieren möchten.
    
* **Aktivieren Sie die moderne Authentifizierung:** Sie müssen die [moderne Authentifizierung](/office365/enterprise/modern-auth-for-office-2013-and-2016) auf Ihrem Mandanten aktivieren, damit dieses Feature funktioniert.

* **Verwenden Sie die neuesten Versionen der Microsoft 365-Clients:** Zur automatischen Anmeldung bei Microsoft 365-Clients (Outlook, Word, Excel und anderen) benötigen Ihre Benutzer Versionen ab 16.0.8730.xxxx.

## <a name="step-2-enable-the-feature"></a>Schritt 2: Aktivieren des Features

Aktivieren Sie nahtloses SSO über [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Sie können [das nahtlose einmalige Anmelden auch mithilfe von PowerShell aktivieren](tshoot-connect-sso.md#manual-reset-of-the-feature), wenn Azure AD Connect Ihre Anforderungen nicht erfüllt. Nutzen Sie diese Option, wenn Sie über mehr als eine Domäne pro Active Directory-Gesamtstruktur verfügen und die Domäne, für die das nahtlose einmalige Anmelden aktiviert werden soll, genauer bestimmen möchten.

Wenn Sie Azure AD Connect neu installieren, wählen Sie den [benutzerdefinierten Installationspfad](how-to-connect-install-custom.md) aus. Aktivieren Sie auf der Seite **Benutzeranmeldung** die Option **Einmaliges Anmelden aktivieren**.

>[!NOTE]
> Die Option steht nur bei den Anmeldemethoden **Kennworthash-Synchronisierung** oder **Passthrough-Authentifizierung** zur Auswahl.

![Azure AD Connect: Benutzeranmeldung](./media/how-to-connect-sso-quick-start/sso8.png)

Wenn Sie bereits eine Installation von Azure AD Connect haben, wählen Sie in Azure AD Connect die Seite **Benutzeranmeldung ändern**, und klicken Sie auf **Weiter**. Bei Verwendung von Azure AD Connect-Versionen ab 1.1.880.0 wird die Option **Einmaliges Anmelden aktivieren** standardmäßig ausgewählt. Wenn Sie ältere Versionen von Azure AD Connect verwenden, wählen Sie die Option **Einmaliges Anmelden aktivieren** aus.

![Azure AD Connect: Ändern der Benutzeranmeldung](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Fahren Sie mit dem Assistenten fort, bis Sie zur Seite **Einmaliges Anmelden aktivieren** gelangen. Geben Sie Anmeldeinformationen des Domänenadministrators für jede Active Directory-Gesamtstruktur an, die:

* Sie über Azure AD Connect mit Azure AD synchronisieren.
* Benutzer enthält, für die Sie nahtloses SSO aktivieren möchten.

Nach Abschluss des Assistenten ist das nahtlose einmalige Anmelden für Ihren Mandanten aktiviert.

>[!NOTE]
> Die Anmeldeinformationen des Domänenadministrators werden weder in Azure AD Connect noch in Azure AD gespeichert. Sie werden nur für die Aktivierung des Features verwendet.

Befolgen Sie diese Anweisungen, um zu überprüfen, ob die nahtlose SSO ordnungsgemäß aktiviert ist:

1. Melden Sie sich mit den Anmeldeinformationen des globalen Administrators für Ihren Mandanten beim [Azure Active Directory-Verwaltungscenter](https://aad.portal.azure.com) an.
2. Wählen Sie im linken Bereich die Option **Azure Active Directory** aus.
3. Wählen Sie **Azure AD Connect** aus.
4. Überprüfen Sie, ob **Aktiviert** für **Nahtloses einmaliges Anmelden** angezeigt wird.

![Azure-Portal: Bereich „Azure AD Connect“](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Für das nahtlose einmalige Anmelden wird das Computerkonto `AZUREADSSOACC` in jeder AD-Gesamtstruktur in Ihrem lokalen Active Directory (AD) erstellt. Das Computerkonto `AZUREADSSOACC` muss aus Sicherheitsgründen stark geschützt werden. Nur Domänen-Admins sollten das Computerkonto verwalten können. Stellen Sie sicher, dass Kerberos-Delegierung für das Computerkonto deaktiviert ist und dass kein anderes Konto in Active Directory über Delegierungsberechtigungen für das `AZUREADSSOACC`-Computerkonto verfügt. Speichern Sie das Computerkonto in einer Organisationseinheit, in der es vor versehentlichem Löschen geschützt ist und auf die nur Domänenadministratoren zugreifen können.

>[!NOTE]
> Wenn Sie Pass-the-Hash- und Credential Theft Mitigation-Architekturen in Ihrer lokalen Umgebung verwenden, nehmen Sie entsprechende Änderungen vor, um sicherzustellen, dass das Computerkonto `AZUREADSSOACC` nicht im Quarantänecontainer landet. 

## <a name="step-3-roll-out-the-feature"></a>Schritt 3: Ausrollen des Features

Sie können mithilfe der unten stehenden Anleitung nach und nach das Rollout des nahtlosen einmaligen Anmeldens für Ihre Benutzer ausführen. Sie fügen zuerst die folgende Azure AD-URL allen oder ausgewählten Intranetzoneneinstellungen Ihrer Benutzer mithilfe der Gruppenrichtlinie in Active Directory hinzu:

- `https://autologon.microsoftazuread-sso.com`

Darüber hinaus müssen Sie mithilfe der Gruppenrichtlinie eine Richtlinieneinstellung für eine Intranetzone namens **Aktualisierungen der Statusleiste per Skript zulassen** aktivieren. 

>[!NOTE]
> Die folgende Anleitung funktioniert nur für Internet Explorer, Microsoft Edge und Google Chrome unter Windows (wenn ein Satz von URLs vertrauenswürdiger Websites wie für Internet Explorer freigegeben wird). Lesen Sie im nächsten Abschnitt die Anweisungen zum Einrichten von Mozilla Firefox und Google Chrome auf macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Warum müssen Sie Einstellungen von Benutzern für Intranetzonen ändern?

Der Browser berechnet standardmäßig anhand der URL automatisch die richtige Zone (Internet oder Intranet). Beispielsweise ist `http://contoso/` der Intranetzone und `http://intranet.contoso.com/` der Internetzone zugeordnet (da die URL einen Punkt enthält). Browser senden keine Kerberos-Tickets an Cloudendpunkte wie die Azure AD-URL, sofern Sie die URL nicht explizit zur Intranetzone des Browsers hinzufügen.

Es gibt zwei Möglichkeiten, die Einstellungen von Benutzern für Intranetzonen zu ändern:

| Option | Maßnahme des Administrators | Benutzererfahrung |
| --- | --- | --- |
| Gruppenrichtlinie | Der Administrator sperrt die Bearbeitung von Einstellungen für Intranetzonen. | Benutzer können ihre eigenen Einstellungen nicht ändern. |
| Gruppenrichtlinieneinstellung |  Der Administrator lässt die Bearbeitung von Einstellungen für Intranetzonen zu. | Benutzer können ihre eigenen Einstellungen ändern. |

### <a name="group-policy-option---detailed-steps"></a>Option „Gruppenrichtlinie“ – detaillierte Schritte

1. Öffnen Sie das Tool Gruppenrichtlinienverwaltungs-Editor.
2. Bearbeiten Sie die Gruppenrichtlinie, die auf einige oder alle Benutzer angewendet wird. In diesem Beispiel wird **Standardrichtlinie der Domäne** verwendet.
3. Navigieren Sie zu **Benutzerkonfiguration** > **Richtlinien** > **Administrative Vorlagen** > **Windows-Komponenten** > **Internet Explorer** > **Internetsystemsteuerung** > **Seite „Sicherheit“** . Wählen Sie dann **Liste der Site zu Zonenzuweisungen**.
    ![Screenshot der Sicherheitsseite, auf der „Liste der Site zu Zonenzuweisungen“ ausgewählt ist.](./media/how-to-connect-sso-quick-start/sso6.png)
4. Aktivieren Sie die Richtlinie, und geben Sie die folgenden Werte in das Dialogfeld ein:
   - **Wertname:** Die Azure AD-URL, an die die Kerberos-Tickets weitergeleitet werden.
   - **Wert** (Daten): **1** gibt die Intranetzone an.

     Das Ergebnis sieht wie folgt aus:

     Wertname: `https://autologon.microsoftazuread-sso.com`
  
     Wert (Daten): 1

   >[!NOTE]
   > Wenn Sie das nahtlose einmalige Anmelden für einige Benutzer verbieten möchten (beispielsweise weil sich diese Benutzer bei freigegebenen Kiosken anmelden), legen Sie die vorherigen Werte auf **4** fest. Diese Aktion fügt die Azure AD-URL zur Zone eingeschränkter Sites hinzu und löst für das nahtlose einmalige Anmelden ständig einen Fehler aus.
   >

5. Klicken Sie auf **OK** und anschließend erneut auf **OK**.

    ![Screenshot des Fensters „Inhalt anzeigen“ mit einer ausgewählten Zonenzuweisung.](./media/how-to-connect-sso-quick-start/sso7.png)

6. Navigieren Sie zu **Benutzerkonfiguration** > **Richtlinien** > **Verwaltungsvorlagen** > **Windows-Komponenten** > **Internet Explorer** > **Internetsystemsteuerung** > **Seite „Sicherheit“**  > **Intranetzone**. Wählen Sie dann **Aktualisierungen der Statusleiste per Skript zulassen**.

    ![Screenshot der Seite „Intranetzone“, auf der „Aktualisierungen der Statusleiste per Skript zulassen“ ausgewählt ist.](./media/how-to-connect-sso-quick-start/sso11.png)

7. Aktivieren Sie die Richtlinieneinstellung, und klicken Sie dann auf **OK**.

    ![Screenshot des Fensters „Aktualisierungen der Statusleiste per Skript zulassen“ mit aktivierter Richtlinieneinstellung.](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Option „Gruppenrichtlinieneinstellung“ – detaillierte Schritte

1. Öffnen Sie das Tool Gruppenrichtlinienverwaltungs-Editor.
2. Bearbeiten Sie die Gruppenrichtlinie, die auf einige oder alle Benutzer angewendet wird. In diesem Beispiel wird **Standardrichtlinie der Domäne** verwendet.
3. Navigieren Sie zu **Benutzerkonfiguration** > **Voreinstellungen** > **Windows-Einstellungen** > **Registrierung** > **Neu** > **Registrierungselement**.

    ![Screenshot, in dem die Optionen „Registrierung“ und „Registrierungselement“ ausgewählt sind.](./media/how-to-connect-sso-quick-start/sso15.png)

4. Geben Sie die folgenden Werte in die entsprechenden Felder ein, und klicken Sie anschließend auf **OK**.
   - **Schlüsselpfad**: **_Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon_**
   - **Wertname**: **_https_**
   - **Werttyp**: **_REG_DWORD_**
   - **Wertdaten**: **_00000001_**
 
     ![Screenshot des Fensters „Neue Registrierungseigenschaften“.](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Einmaliges Anmelden](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Überlegungen zum Browser

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alle Plattformen)

Wenn Sie die [Authentication](https://github.com/mozilla/policy-templates/blob/master/README.md#authentication)-Richtlinieneinstellungen in Ihrer Umgebung verwenden, stellen Sie sicher, dass Sie auch die URL von Azure AD (`https://autologon.microsoftazuread-sso.com`) zum Abschnitt **SPNEGO** hinzufügen. Sie können auch die Option **PrivateBrowsing** auf „true“ festlegen, um nahtloses einmaliges Anmelden im privaten Browsermodus zu ermöglichen.

#### <a name="safari-macos"></a>Safari (macOS)

Stellen Sie sicher, dass der Computer mit macOS in AD eingebunden ist. Anweisungen zum Einbinden Ihres macOS-Geräts in AD werden in diesem Artikel nicht bereitgestellt.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge auf Chromium-Basis (alle Plattformen)

Wenn Sie die Richtlinieneinstellungen [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) oder [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) in Ihrer Umgebung überschrieben haben, stellen Sie sicher, dass Sie ihnen auch die URL von Azure AD (`https://autologon.microsoftazuread-sso.com`) hinzufügen.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge auf Chromium-Basis (macOS und andere Nicht-Windows-Plattformen)

Für Microsoft Edge auf Chromium-Basis auf macOS und anderen Nicht-Windows-Plattformen finden Sie unter [Microsoft Edge – Richtlinien](/DeployEdge/microsoft-edge-policies#authserverallowlist) Informationen zum Hinzufügen der Azure AD-URL zu Ihrer Zulassungsliste für die integrierte Authentifizierung.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (alle Plattformen)

Wenn Sie die Richtlinieneinstellungen [AuthNegotiateDelegateAllowlist](https://chromeenterprise.google/policies/#AuthNegotiateDelegateAllowlist) oder [AuthServerAllowlist](https://chromeenterprise.google/policies/#AuthServerAllowlist) in Ihrer Umgebung überschrieben haben, stellen Sie sicher, dass Sie ihnen auch die URL von Azure AD (`https://autologon.microsoftazuread-sso.com`) hinzufügen.

#### <a name="macos"></a>macOS

Der Rollout der Azure AD-URL für Firefox und Google Chrome unter macOS mithilfe von Active Directory-Gruppenrichtlinienerweiterungen von Drittanbietern wird in diesem Artikel nicht behandelt.

#### <a name="known-browser-limitations"></a>Bekannte Browsereinschränkungen

Nahtloses SSO funktioniert bei Internet Explorer nicht, wenn der Browser im erweiterten geschützten Modus ausgeführt wird. In der nächsten Version von Microsoft Edge auf Chromium-Basis wird das einmalige Anmelden unterstützt und kann im InPrivate- und Gastmodus erfolgen. Microsoft Edge (Legacyversion) wird nicht mehr unterstützt. 

 `AmbientAuthenticationInPrivateModesEnabled` muss möglicherweise gemäß den entsprechenden Dokumentationen für InPrivate- und/oder Gastbenutzer konfiguriert werden:
 
   - [Microsoft Edge Chromium](/DeployEdge/microsoft-edge-policies#ambientauthenticationinprivatemodesenabled)
   - [Google Chrome](https://chromeenterprise.google/policies/?policy=AmbientAuthenticationInPrivateModesEnabled)

## <a name="step-4-test-the-feature"></a>Schritt 4: Testen des Features

Um das Feature für einen bestimmten Benutzer zu testen, stellen Sie sicher, dass alle folgenden Bedingungen erfüllt werden:
  - Der Benutzer meldet sich auf einem Gerät des Unternehmens an.
  - Das Gerät ist mit Ihrer Active Directory-Domäne verknüpft. Das Gerät muss _nicht_[In Azure AD eingebunden](../devices/overview.md) sein.
  - Es muss eine direkte Verbindung zwischen dem Gerät und Ihrem Domänencontroller (DC) bestehen, entweder über das Unternehmensnetzwerk (Kabel- oder Funknetzwerk) oder per Remotezugriff, z.B. über eine VPN-Verbindung.
  - Sie haben [das Feature für diesen Benutzer mithilfe von Gruppenrichtlinien ausgerollt](#step-3-roll-out-the-feature).

So testen Sie das Szenario, wenn der Benutzer nur den Benutzernamen eingibt, jedoch kein Kennwort:
   - Melden Sie sich bei https://myapps.microsoft.com/ an. Löschen Sie den Browsercache, oder verwenden Sie eine neue private Browsersitzung in einem der unterstützten Browser, der im privaten Modus ausgeführt wird.

Führen Sie zum Testen des Szenarios, in dem der Benutzer weder den Benutzernamen noch das Kennwort eingeben muss, einen der folgenden Schritte aus: 
   - Melden Sie sich bei `https://myapps.microsoft.com/contoso.onmicrosoft.com` an. Löschen Sie den Browsercache, oder verwenden Sie eine neue private Browsersitzung in einem der unterstützten Browser, der im privaten Modus ausgeführt wird. Ersetzen Sie *contoso* durch den Namen Ihres Mandanten.
   - Melden Sie sich in einer neuen privaten Browsersitzung bei `https://myapps.microsoft.com/contoso.com` an. Ersetzen Sie *contoso.com* durch eine überprüfte Domäne (keine Verbunddomäne) in Ihrem Mandanten.

## <a name="step-5-roll-over-keys"></a>Schritt 5: Ausführen des Rollovers für Schlüssel

In Schritt 2 erstellt Azure AD Connect Computerkonten (die Azure AD repräsentieren) in allen Active Directory-Gesamtstrukturen, für die Sie das nahtlose einmalige Anmelden aktiviert haben. Weitere Informationen finden Sie unter [Azure Active Directory: Nahtloses einmaliges Anmelden: Technische Einblicke](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>Wenn der Kerberos-Entschlüsselungsschlüssel auf einem Computerkonto kompromittiert wird, kann er dazu verwendet werden, für jeden Benutzer in der AD-Gesamtstruktur Kerberos-Tickets zu generieren. Böswillige Akteure können dann Azure AD-Anmeldungen für kompromittierte Benutzer imitieren. Sie sollten das Rollover dieser Kerberos-Entschlüsselungsschlüssel regelmäßig durchführen – mindestens alle 30 Tage.

Anweisungen zum Durchführen des Rollovers für Schlüssel finden Sie unter [Nahtloses einmaliges Anmelden mit Azure Active Directory: Häufig gestellte Fragen (FAQs)](how-to-connect-sso-faq.yml).

>[!IMPORTANT]
>Sie müssen diesen Schritt nicht _sofort_ nach der Aktivierung des Features ausführen. Führen Sie für die Kerberos-Entschlüsselungsschlüssel mindestens alle 30 Tage ein Rollover durch.

## <a name="next-steps"></a>Nächste Schritte

- [Technische Einzelheiten](how-to-connect-sso-how-it-works.md): Informationen zur Funktionsweise des nahtlosen einmaligen Anmeldens
- [Häufig gestellte Fragen](how-to-connect-sso-faq.yml): Antworten auf häufig gestellte Fragen zum nahtlosen einmaligen Anmelden
- [Problembehandlung](tshoot-connect-sso.md): Informationen zum Beheben von allgemeinen Problemen, die mit der Funktion für nahtloses einmaliges Anmelden auftreten können
- [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789): Fordern Sie neue Features über das Azure Active Directory-Forum an.
