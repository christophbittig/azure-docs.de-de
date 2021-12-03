---
title: Planen einer Bereitstellung mit kennwortloser Authentifizierung in Azure Active Directory
description: Anweisungen für die Bereitstellung einer kennwortlosen Authentifizierung
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/28/2021
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5597ca90a2264d192273ebfd57ecfd51382c3c4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349298"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planen einer Bereitstellung mit kennwortloser Authentifizierung in Azure Active Directory

Kennwörter stellen einen primären Angriffsvektor dar. Angreifer verwenden Social Engineering-, Phishing- und Spray-Angriffe, um Kennwörter zu kompromittieren. Durch eine Strategie für die kennwortlose Authentifizierung wird das Risiko dieser Angriffe verringert.

Microsoft bietet die folgenden [drei Optionen für die kennwortlose Authentifizierung](concept-authentication-passwordless.md), die in Azure Active Directory (Azure AD) integriert sind:

* [Microsoft Authenticator-App](./concept-authentication-passwordless.md#microsoft-authenticator-app): Mit dieser App wird jedes iOS- oder Android-Smartphone zu einer Anmeldeinformation für die sichere, kennwortlose Authentifizierung, mit dem sich Benutzer bei einer beliebigen Plattform oder einem beliebigen Browser anmelden können.

* [FIDO2-konforme Sicherheitsschlüssel](./concept-authentication-passwordless.md#fido2-security-keys): Diese Schlüssel sind besonders nützlich für Benutzer, die sich bei gemeinsam genutzten Computern wie z. B. Kioskcomputern anmelden, für Situationen, in denen die Verwendung von Telefonen eingeschränkt ist, sowie für hochprivilegierte Identitäten. 

* [Windows Hello for Business-](./concept-authentication-passwordless.md#windows-hello-for-business): Diese Option eignet sich am besten für Benutzer von dedizierten Windows-Computern. 

> [!NOTE]
> Wenn Sie eine Offlineversion dieses Plans mit sämtlichen Links erstellen möchten, verwenden Sie hierzu die Browserfunktion zum Drucken als PDF-Datei.

## <a name="use-the-passwordless-methods-wizard"></a>Verwenden des Assistenten für kennwortlose Methoden

Das [Azure-Portal](https://portal.azure.com/) umfasst jetzt einen Assistenten für kennwortlose Methoden, mit dem Sie die geeignete Methode für jede Ihrer Zielgruppen auswählen können. Wenn Sie die geeigneten Methoden noch nicht bestimmt haben, wechseln Sie zu [https://aka.ms/passwordlesswizard](https://aka.ms/passwordlesswizard), und kehren Sie dann zu diesem Artikel zurück, um mit der Planung für Ihre ausgewählten Methoden fortzufahren. **Sie benötigen Administratorrechte, um auf diesen Assistenten zugreifen zu können.**

## <a name="passwordless-authentication-scenarios"></a>Szenarien der kennwortlosen Authentifizierung

Die Methoden für die kennwortlose Authentifizierung von Microsoft unterstützen eine Vielzahl von Szenarien. Beachten Sie die Anforderungen Ihrer Organisation, die Voraussetzungen und die Funktionen der einzelnen Authentifizierungsmethoden, um Ihre Strategie für die kennwortlose Authentifizierung auszuwählen. 

In der folgenden Tabelle sind die Methoden für die kennwortlose Authentifizierung nach Gerätetypen aufgeführt. Empfehlungen werden in **Fettformatierung** dargestellt.

| Device types (Gerätetypen)| Methoden für die kennwortlose Authentifizierung |
| - | - |
| Dedizierte Nicht-Windows-Geräte| <li> **Microsoft Authenticator-App** <li> Sicherheitsschlüssel |
| Dedizierte Windows 10-Computer (Version 1703 und höher)| <li> **Windows Hello for Business** <li> Sicherheitsschlüssel |
| Dedizierte Windows 10-Computer (vor Version 1703)| <li> **Windows Hello for Business** <li> Microsoft Authenticator-App |
| Freigegebene Geräte: Tablets und mobile Geräte| <li> **Microsoft Authenticator-App** <li> Anmeldung mit Einmalkennwort |
| Kioskcomputer (Legacy)| **Microsoft Authenticator-App** |
| Kioskcomputer und freigegebene Computer (Windows 10)| <li> **Sicherheitsschlüssel** <li> Microsoft Authenticator-App |


## <a name="prerequisites"></a>Voraussetzungen 

Stellen Sie sicher, dass die Voraussetzungen erfüllt sind, bevor Sie mit der kennwortlosen Bereitstellung beginnen.

### <a name="required-roles"></a>Erforderliche Rollen

Nachfolgend werden die Rollen mit den geringsten Rechten aufgeführt, die für diese Bereitstellung erforderlich sind:
<p>

| Azure AD-Rolle| BESCHREIBUNG |
| - | -|
| Globaler Administrator| Für die Implementierung einer kombinierten Registrierung. |
| Authentifizierungsadministrator| Für die Implementierung und Verwaltung von Authentifizierungsmethoden. |
| User| Für die Konfiguration der Authenticator-App auf dem Gerät oder für die Registrierung eines Hardwaresicherheitsschlüssel für Web- oder Windows 10-Anmeldungen. |

Im Rahmen dieses Bereitstellungsplans empfiehlt Microsoft, die kennwortlose Authentifizierung für alle [privilegierten Konten](../privileged-identity-management/pim-configure.md) zu aktivieren.

### <a name="microsoft-authenticator-app-and-security-keys"></a>Microsoft Authenticator-App und Sicherheitsschlüssel

Die Voraussetzungen werden durch die ausgewählten Methoden für die kennwortlose Authentifizierung bestimmt.

| Voraussetzung| Microsoft Authenticator-App| FIDO2-Sicherheitsschlüssel|
| - | -|-|
| [Kombinierte Registrierung für Azure AD Multi-Factor Authentication (MFA) und Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR)](howto-registration-mfa-sspr-combined.md) sind aktiviert.| √| √|
| [Benutzer können Azure AD MFA verwenden.](howto-mfa-getstarted.md)| √| √|
| [Benutzer haben sich für Azure AD MFA und SSPR registriert.](howto-registration-mfa-sspr-combined.md)| √| √|
| [Benutzer haben ihre mobilen Geräte für Azure Active Directory registriert](../devices/overview.md)| √| |
| Windows 10 Version 1809 oder höher mit einem unterstützten Browser wie Microsoft Edge oder Mozilla Firefox (ab Version 67) Microsoft empfiehlt Version 1903 oder höher für native Unterstützung.| | √|
| Kompatible Sicherheitsschlüssel. Stellen Sie sicher, dass Sie einen [von Microsoft getesteten und verifizierten FIDO2-Sicherheitsschlüssel](concept-authentication-passwordless.md) oder einen anderen kompatiblen FIDO2-Sicherheitsschlüssel verwenden.| | √|


### <a name="windows-hello-for-business"></a>Windows Hello for Business

Die Voraussetzungen und Bereitstellungspfade für Windows Hello for Business hängen stark davon ab, ob Sie die Bereitstellung in einer lokalen Konfiguration, in einer Hybridkonfiguration oder in einer reinen Cloudkonfiguration durchführen. Sie hängen außerdem von Ihrer Strategie für den Gerätebeitritt ab. 

Wählen Sie Windows Hello for Business aus, und [führen Sie den Assistenten aus](https://aka.ms/passwordlesswizard), um die für Ihre Organisation geltenden Voraussetzungen und geeigneten Bereitstellungen zu ermitteln.

![Wählen Sie im Assistenten Windows Hello for Business aus.](media/howto-authentication-passwordless-deployment/passwordless-wizard-select.png)


Der Assistent verwendet Ihre Eingaben, um einen Schritt-für-Schritt-Plan zu erstellen, dem Sie folgen können.

## <a name="plan-the-project"></a>Planen des Projekts

Wenn Technologieprojekte nicht gelingen, ist dies in der Regel auf nicht erfüllte Erwartungen auf den Gebieten Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie die [richtigen Beteiligten hinzuziehen](../fundamentals/active-directory-deployment-plans.md) und dass die Rollen der Beteiligten im Projekt gut verstanden werden.

### <a name="plan-a-pilot"></a>Planen eines Pilotprojekts

Wenn Sie die kennwortlose Authentifizierung bereitstellen, sollten Sie zunächst mindestens eine Pilotgruppe aktivieren. Sie können speziell zu diesem Zweck Gruppen erstellen. Fügen Sie die Benutzer, die am Pilotversuch teilnehmen, den Gruppen hinzu. Aktivieren Sie dann neue kennwortlose Authentifizierungsmethoden für die ausgewählten Gruppen. Lesen Sie hierzu [Bewährte Methoden für einen Pilotversuch](../fundamentals/active-directory-deployment-plans.md).

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Ihre Kommunikation mit Endbenutzern muss folgende Informationen enthalten:

* [Leitfaden zur kombinierten Registrierung für Azure AD MFA und SSPR](howto-registration-mfa-sspr-combined.md)

* [Herunterladen der Microsoft Authenticator-App](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)

* [Registrieren der Microsoft Authenticator-App](howto-authentication-passwordless-phone.md)

* [Anmelden per Telefon](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)

Microsoft stellt Kommunikationsvorlagen für Endbenutzer bereit. Laden Sie das Material für den [Authentifizierungsrollout](https://aka.ms/MFAtemplates) herunter, um Ihre Kommunikation zu entwerfen. Die Materialien für den Rollout enthalten anpassbare Poster und E-Mail-Vorlagen, mit denen Sie Ihre Benutzer über die geplanten Optionen für die kennwortlose Authentifizierung in Ihrer Organisation informieren können.

## <a name="plan-user-registration"></a>Planen der Benutzerregistrierung

Benutzer registrieren ihre kennwortlose Methode als Teil des **kombinierten Workflows für Sicherheitsinformationen** unter [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Azure AD protokolliert die Registrierung von Sicherheitsschlüsseln und der Microsoft Authenticator- App sowie alle weiteren Änderungen an den Authentifizierungsmethoden. 

Für die erstmalige Nutzung können Administratoren einem Benutzer, der kein Kennwort besitzt, einen [befristeten Zugriffspass](howto-authentication-temporary-access-pass.md) für die Registrierung ihrer Sicherheitsinformationen in [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo.md) bereitstellen. Dieser Zugriffspass ist zeitlich befristet und erfüllt strenge Authentifizierungsanforderungen. **Ein befristeter Zugriffspass wird pro Benutzer bereitgestellt.**

Diese Methode kann außerdem für eine einfache Wiederherstellung genutzt werden, wenn ein Benutzer seine Anmeldeinformationen verloren oder vergessen hat (z. B. einen Sicherheitsschlüssel oder die Microsoft Authenticator-App), sich jedoch anmelden muss, um eine **neue Methode für die strenge Authentifizierung zu registrieren**. 

>[!NOTE] 
> Wenn Sie den Sicherheitsschlüssel oder die Microsoft Authenticator-App in bestimmten Szenarien nicht verwenden können, kann die mehrstufige Authentifizierung mit einem Benutzernamen und einem Kennwort zusammen mit einer anderen registrierten Methode als Fallbackoption genutzt werden.

## <a name="plan-for-and-deploy-the-microsoft-authenticator-app"></a>Planen und Bereitstellen der Microsoft Authenticator-App

Durch die [Microsoft Authenticator-App](concept-authentication-passwordless.md) wird jedes iOS- oder Android-Telefon zu einer Anmeldeinformation für die sichere, kennwortlose Authentifizierung. Die App steht als kostenloser Download bei Google Play und im Apple App Store zur Verfügung. Weisen Sie die Benutzer an, die [Microsoft Authenticator-App herunterzuladen](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a) und die Anweisungen zum Aktivieren der Anmeldung per Telefon zu befolgen.

### <a name="technical-considerations"></a>Technische Überlegungen

**Integration der Active Directory-Verbunddienste (AD FS)** : Wenn ein Benutzer die kennwortlosen Microsoft Authenticator-Anmeldeinformationen aktiviert, wird die Authentifizierung für diesen Benutzer standardmäßig auf das Senden einer Genehmigungsbenachrichtigung festgelegt. Benutzer in einem hybriden Mandanten werden für die Anmeldung nur dann an AD FS weitergeleitet, wenn die Option „Stattdessen Ihr Kennwort verwenden“ ausgewählt wird. Bei diesem Prozess werden auch alle lokalen Richtlinien für bedingten Zugriff und Passthrough-Authentifizierungsflüsse umgangen. Wenn jedoch ein Anmeldehinweis (login_hint) angegeben ist, wird ein Benutzer an AD FS weitergeleitet, und die Option zur Verwendung von kennwortlosen Anmeldeinformationen wird umgangen.

**Azure MFA-Server**: Endbenutzer, für die die mehrstufige Authentifizierung über den lokalen Azure MFA-Server einer Organisation aktiviert ist, können einen einzigen Satz von Anmeldeinformationen für die kennwortlose Anmeldung per Telefon erstellen und verwenden. Wenn der Benutzer versucht, mehrere Installationen (5 oder mehr) der Microsoft Authenticator-App mit den Anmeldeinformationen zu aktualisieren, kann diese Änderung zu einem Fehler führen.

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die für die Anmeldung der Benutzer mehrstufige Authentifizierung anfordern möchten, sollten cloudbasierte Multi-Factor Authentication von Azure AD verwenden. Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren. Es wird empfohlen, von Azure MFA Server auf Azure Active Directory MFA umzustellen.

**Geräteregistrierung**: Um die Authenticator-App für die kennwortlose Authentifizierung zu verwenden, muss das Gerät im Azure AD-Mandanten registriert sein und darf nicht von mehreren Personen gemeinsam genutzt werden. Ein Gerät kann nur bei einem einzigen Mandanten registriert werden. Dies bedeutet, dass nur ein einzelnes Geschäfts-, Schul- oder Unikonto für die Anmeldung per Telefon über die Microsoft Authenticator-App unterstützt wird.

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Bereitstellen der Anmeldung per Telefon mit der Microsoft Authenticator-App

Führen Sie die Schritte im Artikel [Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App (Vorschauversion)](howto-authentication-passwordless-phone.md) aus, um die Microsoft Authenticator-App als kennwortlose Authentifizierungsmethode in Ihrer Organisation zu aktivieren.

### <a name="testing-microsoft-authenticator-app"></a>Testen der Microsoft Authenticator-App

Im Anschluss finden Sie Beispieltestfälle für die kennwortlose Authentifizierung mit der Microsoft Authenticator-App:

| Szenario| Erwartete Ergebnisse |
| - |-|
| Benutzer kann Microsoft Authenticator-App registrieren| Benutzer kann die App über https://aka.ms/mysecurityinfo registrieren |
| Benutzer kann die Anmeldung per Telefon aktivieren| Anmeldung per Telefon ist für das Geschäftskonto konfiguriert |
| Benutzer kann auf eine App mit Anmeldung per Telefon zugreifen| Benutzer durchläuft die Anmeldung per Telefon und erreicht die Anwendung. |
| Testen des Rollbacks der Registrierung für die Anmeldung per Telefon durch Deaktivieren der kennwortlosen Microsoft Authenticator-Anmeldung im Bildschirm „Authentifizierungsmethoden“ des Azure Active Directory-Portals| Zuvor aktivierte Benutzer können die kennwortlose Anmeldung über Microsoft Authenticator nicht mehr verwenden. |
| Entfernen der telefonischen Anmeldung per Microsoft Authenticator-App| Geschäftskonto ist auf Microsoft Authenticator nicht mehr verfügbar |


### <a name="troubleshoot-phone-sign-in"></a>Behandeln von Problemen bei der Anmeldung per Telefon


| Szenario| Lösung |
| - |-|
| Der Benutzer kann keine kombinierte Registrierung ausführen.| Sicherstellen, dass die [kombinierte Registrierung](concept-registration-mfa-sspr-combined.md) aktiviert ist. |
| Der Benutzer kann die Anmeldung per Telefon in der Authenticator-App nicht aktivieren.| Sicherstellen, dass sich der Benutzer im Gültigkeitsbereich für die Bereitstellung befindet. |
| Der Benutzer befindet sich NICHT im Gültigkeitsbereich für die kennwortlose Authentifizierung. Trotzdem wird ihm die Option für die kennwortlose Anmeldung angezeigt, er kann sie jedoch nicht abschließen.| Dieser Fall tritt ein, wenn der Benutzer vor der Erstellung der Richtlinie die Anmeldung per Telefon in der Anwendung aktiviert hat. Um die Anmeldung zu aktivieren, fügen Sie den Benutzer einer Benutzergruppe zu, die für die kennwortlose Anmeldung aktiviert ist. So blockieren Sie die Anmeldung Veranlassen Sie, dass der Benutzer seine Anmeldeinformationen aus der Anwendung entfernt. |


## <a name="plan-for-and-deploy-fido2-compliant-security-keys"></a>Planen und Bereitstellen FIDO2-konformer Sicherheitsschlüssel

Aktivieren Sie kompatible Sicherheitsschlüssel. Hier finden Sie eine Liste der [FIDO2-Sicherheitsschlüsselanbieter](concept-authentication-passwordless.md), die Schlüssel bereitstellen, die bekanntermaßen mit der Funktion für die kennwortlose Anmeldung kompatibel sind.

### <a name="plan-security-key-lifecycle"></a>Planen des Lebenszyklus für Sicherheitsschlüssel

Planen Sie den Schlüssellebenszyklus, und treffen Sie entsprechende Vorbereitungen.

**Schlüsselverteilung**: Planen Sie, wie Schlüssel für Ihre Organisation bereitgestellt werden sollen. Möglicherweise verfügen Sie über einen zentralisierten Bereitstellungsprozess oder erlauben den Endbenutzern, mit FIDO 2.0 kompatible Schlüssel zu erwerben.

 **Schlüsselaktivierung**: Endbenutzer müssen den Sicherheitsschlüssel selbst aktivieren. Endbenutzer registrieren ihre Sicherheitsschlüssel unter [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) und aktivieren den zweiten Faktor (PIN oder biometrisch) bei der ersten Verwendung. Bei der erstmaligen Verwendung können Benutzer einen befristeten Zugriffspass verwenden, um ihre Sicherheitsinformationen zu registrieren.

 **Deaktivieren eines Schlüssels**: Wenn ein Administrator einen FIDO2-Schlüssel entfernen möchte, der einem Benutzerkonto zugeordnet ist, kann er zu diesem Zweck den Schlüssel wie unten beschrieben aus der Authentifizierungsmethode des Benutzers löschen. Weitere Informationen finden Sie unter [Deaktivieren eines Schlüssels](howto-authentication-passwordless-security-key.md#disable-a-key).

 

**Neuen Schlüssel ausstellen**: Der Benutzer kann den neuen FIDO2-Schlüssel registrieren, indem er zu [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) wechselt. 

### <a name="technical-considerations"></a>Technische Überlegungen

Es gibt drei Arten von Bereitstellungen der kennwortlosen Anmeldung mit Sicherheitsschlüsseln:

* Azure AD-Web-Apps in einem unterstützten Browser

* In Azure AD eingebundene Windows 10-Geräte

* In Azure AD Hybrid eingebundene Windows 10-Geräte 

  * Sie ermöglichen den Zugriff auf cloudbasierte und lokale Ressourcen. Weitere Informationen zum Zugriff auf lokale Ressourcen finden Sie unter [Einmaliges Anmelden bei lokalen Ressourcen mithilfe von FIDO2-Schlüsseln](howto-authentication-passwordless-security-key-on-premises.md).

Verwenden Sie **für Azure AD-Web-Apps und Windows-Geräte mit Azure AD-Einbindung** Folgendes:

* Windows 10 Version 1809 oder höher mit einem unterstützten Browser wie Microsoft Edge oder Mozilla Firefox (ab Version 67)

* Windows 10 Version 1809 unterstützt die FIDO2-Anmeldung und erfordert möglicherweise die Bereitstellung von Software des FIDO2-Schlüsselanbieters. Es wird die Verwendung von mindestens Version 1903 empfohlen.

Verwenden Sie für **Geräte mit Azure AD Hybrid-Einbindung** Folgendes: 

* Windows 10, Version 2004 oder höher 

* Vollständig gepatchte Domänenserver unter Windows Server 2016 oder 2019. 

* Aktuelle Version von Azure AD Connect

#### <a name="enable-windows-10-support"></a>Aktivieren von Windows 10-Unterstützung

Um die Windows 10-Anmeldung mit FIDO2-Sicherheitsschlüsseln zu aktivieren, muss die Funktionalität für Anmeldeinformationsanbieter in Windows 10 aktiviert werden. Wählen Sie eine der folgenden Optionen aus:

* [Aktivieren des Anmeldeinformationsanbieters mit Intune](howto-authentication-passwordless-security-key-windows.md)

  * Wir empfehlen eine Intune-Bereitstellung.

* [Aktivieren des Anmeldeinformationsanbieters mittels eines Bereitstellungspakets](howto-authentication-passwordless-security-key-windows.md)

  * Wenn die Intune-Bereitstellung nicht möglich ist, müssen Administratoren auf jedem Computer ein Paket bereitstellen, um die Anmeldeinformationsanbieter-Funktionalität zu aktivieren. Die Paketinstallation kann mit einer der folgenden Optionen ausgeführt werden:
    * Gruppenrichtlinie oder Configuration Manager
    * Lokale Installation auf einem Windows 10-Computer

* [Aktivieren mit Gruppenrichtlinie](howto-authentication-passwordless-security-key-windows.md)

   * Diese Option wird nur für Geräte mit Azure AD-Hybrideinbindung unterstützt.

#### <a name="enable-on-premises-integration"></a>Aktivieren der lokalen Integration

Befolgen Sie die Schritte im Artikel [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT] 
> Diese Schritte müssen auch für alle Geräte mit Azure AD Hybrid-Einbindung ausgeführt werden, um FIDO2-Sicherheitsschlüssel für die Windows 10-Anmeldung verwenden zu können.


### <a name="key-restrictions-policy"></a>Richtlinie zur Schlüsseleinschränkung

Wenn Sie den Sicherheitsschlüssel bereitstellen, können Sie die Verwendung von FIDO2-Schlüsseln optional nur auf bestimmte Hersteller beschränken, die von Ihrer Organisation genehmigt wurden. Zum Einschränken von Schlüsseln wird die Authenticator-Nachweis-GUID (Authenticator Attestation GUID, AAGUID) benötigt. [Sie können Ihre AAGUID auf zwei Arten abrufen](howto-authentication-passwordless-security-key.md#security-key-authenticator-attestation-guid-aaguid).

![Erzwingen von Schlüsseleinschränkungen](media/howto-authentication-passwordless-deployment/security-key-enforce-key-restriction.png)


Wenn der Sicherheitsschlüssel eingeschränkt ist und der Benutzer versucht, den FIDO2-Sicherheitsschlüssel zu registrieren, wird folgender Fehler angezeigt:

![Sicherheitsschlüsselfehler, wenn der Schlüssel eingeschränkt ist](media/howto-authentication-passwordless-deployment/security-key-restricted-error.png)


Wenn die AAGUID eingeschränkt ist, nachdem der Benutzer den Sicherheitsschlüssel registriert hat, wird die folgende Meldung angezeigt:

![Anzeigen für Benutzer, wenn AAGUID eingeschränkt ist](media/howto-authentication-passwordless-deployment/security-key-block-user-window.png)


*FIDO2-Schlüssel durch Schlüsseleinschränkungsrichtlinie blockiert

### <a name="deploy-fido2-security-key-sign-in"></a>Bereitstellen der FIDO2-Sicherheitsschlüsselanmeldung

Befolgen Sie die Schritte im Artikel [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln](howto-authentication-passwordless-security-key.md), um FIDO2-Sicherheitsschlüssel als kennwortlose Authentifizierungsmethode in Ihrer Organisation zu aktivieren. 

### <a name="testing-security-keys"></a>Testen von Sicherheitsschlüsseln

Im Folgenden finden Sie beispielhafte Testfälle für die kennwortlose Authentifizierung mit Sicherheitsschlüsseln.

#### <a name="passwordless-fido-sign-in-to-azure-active-directory-joined-windows-10-devices"></a>Kennwortlose FIDO-Anmeldung bei mit Azure Active Directory verbundenen Windows 10-Geräten


| Szenario (Windows-Build)| Erwartete Ergebnisse |
| - |-|
| Der Benutzer kann das FIDO2-Gerät registrieren (1809)| Der Benutzer kann das FIDO2-Gerät unter „Einstellungen > Konten > Anmeldeoptionen > Sicherheitsschlüssel“ registrieren |
| Der Benutzer kann das FIDO2-Gerät zurücksetzen (1809)| Der Benutzer kann das FIDO2-Gerät mithilfe der Herstellersoftware zurücksetzen |
| Der Benutzer kann sich mit dem FIDO2-Gerät (1809) anmelden| Der Benutzer kann den Sicherheitsschlüssel im Anmeldefenster auswählen und sich erfolgreich anmelden. |
| Der Benutzer kann das FIDO2-Gerät registrieren (1903)| Der Benutzer kann das FIDO2-Gerät unter „Einstellungen > Konten > Anmeldeoptionen > Sicherheitsschlüssel“ registrieren |
| Der Benutzer kann das FIDO2-Gerät zurücksetzen (1903)| Der Benutzer kann das FIDO2-Gerät unter „Einstellungen > Konten > Anmeldeoptionen > Sicherheitsschlüssel“ zurücksetzen |
| Der Benutzer kann sich mit dem FIDO2-Gerät (1903) anmelden| Der Benutzer kann den Sicherheitsschlüssel im Anmeldefenster auswählen und sich erfolgreich anmelden. |


#### <a name="passwordless-fido-sign-in-to-azure-ad-web-apps"></a>Kennwortlose FIDO-Anmeldung bei Azure AD-Web-Apps


| Szenario| Erwartete Ergebnisse |
| - |-|
| Der Benutzer kann das FIDO2-Gerät über „aka.ms/mysecurityinfo“ mit Microsoft Edge registrieren| Registrierung sollte erfolgreich sein |
| Der Benutzer kann das FIDO2-Gerät über „aka.ms/mysecurityinfo“ mit Firefox registrieren| Registrierung sollte erfolgreich sein |
| Der Benutzer kann sich mithilfe des FIDO2-Geräts über Microsoft Edge bei OneDrive online anmelden| Anmeldung sollte erfolgreich sein |
| Der Benutzer kann sich mithilfe des FIDO2-Geräts über Firefox bei OneDrive online anmelden| Anmeldung sollte erfolgreich sein |
| Testen des Rollbacks der FIDO2-Geräteregistrierung durch Deaktivieren der FIDO2-Sicherheitsschlüssel im Fenster mit den Authentifizierungsmethoden im Azure Active Directory-Portal| Für die Benutzer gilt Folgendes: <li> Sie werden aufgefordert, sich mit ihrem Sicherheitsschlüssel anzumelden. <li> Nach erfolgreicher Anmeldung wird folgender Fehler angezeigt: „Für Ihre Unternehmensrichtlinie müssen Sie eine andere Methode für die Anmeldung verwenden.“ <li>Sie können eine andere Methode auswählen und sich erfolgreich anmelden. Schließen Sie das Fenster, und melden Sie sich erneut an, um zu überprüfen, ob diese Fehlermeldung nicht angezeigt wird. |


### <a name="troubleshoot-security-key-sign-in"></a>Behandeln von Problemen bei der Anmeldung mit Sicherheitsschlüssel

| Szenario| Lösung |
| - | -|
| Der Benutzer kann keine kombinierte Registrierung ausführen.| Sicherstellen, dass die [kombinierte Registrierung](concept-registration-mfa-sspr-combined.md) aktiviert ist. |
| Der Benutzer kann in seinen [Sicherheitseinstellungen](https://aka.ms/mysecurityinfo) keinen Sicherheitsschlüssel hinzufügen.| Sicherstellen, dass [Sicherheitsschlüssel](howto-authentication-passwordless-security-key.md) aktiviert sind. |
| Der Benutzer kann in den Windows 10-Anmeldeoptionen keinen Sicherheitsschlüssel hinzufügen.| [Stellen Sie sicher, dass Sicherheitsschlüssel für die Windows-Anmeldung](concept-authentication-passwordless.md) aktiviert sind. |
| **Fehlermeldung**: Wir haben festgestellt, dass dieser Browser oder dieses Betriebssystem keine FIDO2-Sicherheitsschlüssel unterstützt.| Kennwortlose FIDO2-Sicherheitsgeräte können nur in unterstützten Browsern (Microsoft Edge, Firefox-Version 67) unter Windows 10, Version 1809 oder höher, registriert werden. |
| **Fehlermeldung**: Für Ihre Unternehmensrichtlinie müssen Sie eine andere Anmeldungsmethode verwenden.| Stellen Sie sicher, dass Sicherheitsschlüssel im Mandanten aktiviert sind. |
| Benutzer kann meinen Sicherheitsschlüssel unter Windows 10, Version 1809 nicht verwalten| Version 1809 erfordert, dass Sie die vom FIDO2-Schlüsselanbieter bereitgestellte Sicherheitsschlüssel-Verwaltungssoftware verwenden. Bitten Sie den Anbieter um Support. |
| Mein FIDO2-Sicherheitsschlüssel ist möglicherweise fehlerhaft. Wie kann ich ihn testen?| Navigieren Sie zu [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), geben Sie Anmeldeinformationen für ein Testkonto ein, stecken Sie den betroffenen Sicherheitsschlüssel ein, wählen Sie rechts oben auf dem Bildschirm die Schaltfläche + aus, klicken Sie auf „Erstellen“, und durchlaufen Sie den Erstellungsvorgang. Wenn in diesem Szenario ein Fehler auftritt, ist Ihr Gerät möglicherweise fehlerhaft. |

## <a name="manage-passwordless-authentication"></a>Verwalten der kennwortlosen Authentifizierung

Um die kennwortlosen Authentifizierungsmethoden Ihres Benutzers im [Azure-Portal](https://portal.azure.com/) zu verwalten, wählen Sie Ihr Benutzerkonto und dann „Authentifizierungsmethoden“ aus.

### <a name="microsoft-graph-apis"></a>Microsoft Graph-APIs 

Sie können die Methoden für die kennwortlose Authentifizierung auch mithilfe der Authentifizierungsmethoden-API in Microsoft Graph verwalten. Zum Beispiel:

* Sie können Details zum FIDO2-Sicherheitsschlüssel eines Benutzers abrufen und löschen, wenn der Benutzer den Schlüssel verloren hat.

* Sie können Details zur Microsoft Authenticator-Registrierung eines Benutzers abrufen und löschen, wenn der Benutzer das Telefon verloren hat.

* Verwalten Sie Ihre Richtlinien für Authentifizierungsmethoden für Sicherheitsschlüssel und Microsoft Authenticator-App.

Weitere Informationen dazu, welche Authentifizierungsmethoden in Microsoft Graph verwaltet werden können, finden Sie unter [Übersicht über die Azure AD-Authentifizierungsmethoden-API](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

### <a name="rollback"></a>Rollback

Obwohl die kennwortlose Authentifizierung ein einfaches Feature mit minimalen Auswirkungen auf Endbenutzer ist, muss möglicherweise ein Rollback ausgeführt werden.

Für das Rollback muss sich der Administrator beim Azure-Portal anmelden, die gewünschten sicheren Authentifizierungsmethoden auswählen und die Aktivierungsoption in „Nein“ ändern. Dadurch wird die kennwortlose Funktion für alle Benutzer deaktiviert.

![Kennwortloses Rollback](media/howto-authentication-passwordless-deployment/passwordless-rollback.png)

Benutzer, die bereits FIDO2-Sicherheitsgeräte registriert haben, werden bei der nächsten Anmeldung aufgefordert, das Sicherheitsgerät zu verwenden, und es wird die folgende Fehlermeldung angezeigt:

![Fehlerfenster für Kennwortrollback](media/howto-authentication-passwordless-deployment/passswordless-rollback-error-window.png)

### <a name="reporting-and-monitoring"></a>Berichterstellung und Überwachung

Azure AD stellt Berichte zur Verfügung, die technische und geschäftliche Einblicke bieten. Sorgen Sie dafür, dass die Besitzer Ihrer geschäftlichen und technischen Anwendungen den Besitz dieser Berichte übernehmen und sie den Anforderungen Ihrer Organisation gemäß nutzen.

Die folgende Tabelle enthält einige Beispiele für typische Berichtsszenarien:

| Risikomanagement| Steigerung der Produktivität| Governance und Einhaltung| Andere|
|-|-|-|-|
| Berichtstypen| Authentifizierungsmethoden: Benutzer, die für die kombinierte Sicherheitsregistrierung registriert sind| Authentifizierungsmethoden: Benutzer, die für die App-Benachrichtigung registriert sind| Anmeldungen: Überprüfen, wer wie auf den Mandanten zugreift |
| Mögliche Aktionen| Zielbenutzer noch nicht registriert| Übernahme von Microsoft Authenticator-App oder Sicherheitsschlüsseln steuern| Zugriff widerrufen oder zusätzliche Sicherheitsrichtlinien für Administratoren erzwingen |


#### <a name="track-usage-and-insights"></a>Nachverfolgen von Nutzung und Erkenntnissen

Azure AD fügt den Überwachungsprotokollen in folgenden Fällen Einträge hinzu:

* Ein Administrator nimmt im Authentifizierungsmethoden-Abschnitt Änderungen vor.

* Ein Benutzer nimmt in Azure AD Änderungen an seinen Anmeldeinformationen vor.

* Ein Benutzer aktiviert oder deaktiviert sein Konto für einen Sicherheitsschlüssel oder setzt den zweiten Faktor für den Sicherheitsschlüssel auf dem Windows 10-Computer zurück. Siehe Ereignis-IDs 4670 und 5382.

**Azure AD speichert die meisten Überwachungsdaten 30 Tage** und stellt sie per Azure-Verwaltungsportal oder API zur Verfügung, damit Sie sie in Ihre Analysesysteme herunterladen können. Wenn Sie einen längeren Aufbewahrungszeitraum benötigen, exportieren und nutzen Sie die Protokolle in einem SIEM-Tool wie [Microsoft Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk oder Sumo Logic. Wir empfehlen eine längere Aufbewahrung für Überwachungen, Trendanalysen und andere Geschäftsanforderungen.

Das Aktivitätsdashboard für Authentifizierungsmethoden umfasst zwei Registerkarten: „Registrierung“ und „Nutzung“.

Auf der [Registerkarte „Registrierung“](https://portal.azure.com/) wird angegeben, wie viele Benutzer eine kennwortlose Authentifizierung durchführen können, und es werden weitere Authentifizierungsmethoden angezeigt. Diese Registerkarte umfasst zwei Diagramme:

* Registrierte Benutzer nach Authentifizierungsmethode

* Kürzlich durchgeführte Registrierungen nach Authentifizierungsmethode

![Registerkarte „Registrierung“ zum Anzeigen von Authentifizierungsmethoden](media/howto-authentication-passwordless-deployment/monitoring-registration-tab.png)

Auf der [Registerkarte „Nutzung“](https://portal.azure.com/) werden die Anmeldungen nach Authentifizierungsmethode angezeigt.

![Registerkarte „Nutzung“ zum Anzeigen von Authentifizierungsmethoden](media/howto-authentication-passwordless-deployment/monitoring-usage-tab.png)

Weitere Informationen finden Sie unter [Nachverfolgen von registrierten Authentifizierungsmethoden und Nutzung innerhalb der Azure AD-Organisation](howto-authentication-methods-activity.md).

#### <a name="sign-in-activity-reports"></a>Berichte zu Anmeldeaktivitäten

Verwenden Sie den Bericht zur [Anmeldeaktivität](../reports-monitoring/concept-sign-ins.md), um die Authentifizierungsmethoden nachzuverfolgen, die zum Anmelden bei den verschiedenen Anwendungen verwendet werden. 

Wählen Sie die Benutzerzeile und dann die Registerkarte **Authentifizierungsdetails** aus, um anzuzeigen, welche Authentifizierungsmethode für welche Anmeldeaktivität verwendet wurde.

![Berichte zur Anmeldeaktivität](media/howto-authentication-passwordless-deployment/reporting-sign-in-activity.png)

## <a name="next-steps"></a>Nächste Schritte

* [Wie funktioniert die kennwortlose Authentifizierung?](concept-authentication-passwordless.md)

* [Bereitstellen anderer Identitätsfeatures](../fundamentals/active-directory-deployment-plans.md)
