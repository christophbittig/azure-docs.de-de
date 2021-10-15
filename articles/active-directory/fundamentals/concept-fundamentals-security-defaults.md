---
title: Azure Active Directory-Sicherheitsstandards
description: Informationen zu Sicherheitsstandardrichtlinien zum Schutz von Organisationen vor gängigen Angriffen in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: lvandenende
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: b88cb7baa3c8eb8d40bd23d8697f563406bd1fdb
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535013"
---
# <a name="what-are-security-defaults"></a>Was sind Sicherheitsstandards?

Die Aufrechterhaltung der Sicherheit kann sich angesichts der zunehmenden Verbreitung gängiger identitätsbezogener Angriffe wie Kennwortspray, Replay und Phishing als schwierig erweisen. Sicherheitsstandards in Azure Active Directory (Azure AD) können den Schutz Ihrer Organisation vor solchen Angriffen mit vorkonfigurierten Sicherheitseinstellungen vereinfachen:

- Alle Benutzer müssen sich für die Azure AD Multi-Factor-Authentifizierung registrieren.
- Administratoren müssen eine mehrstufige Authentifizierung durchführen.
- Blockieren älterer Authentifizierungsprotokolle.
- Benutzer müssen bei Bedarf eine mehrstufige Authentifizierung durchführen.
- Schützen privilegierter Aktivitäten Zugriff auf das Azure-Portal.

![Screenshot: Azure-Portal mit Umschaltfläche zum Aktivieren der Sicherheitsstandards](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Weitere Informationen dazu, warum diese Sicherheitsstandards verfügbar gemacht werden, finden Sie im Blogbeitrag von Alex Weinert [Introducing security defaults](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414) (Einführung von Sicherheitsstandards).

## <a name="availability"></a>Verfügbarkeit

Microsoft stellt allen Benutzern Sicherheitsstandards zur Verfügung. Das Ziel ist sicherzustellen, dass in allen Organisationen eine Standardsicherheitsebene ohne zusätzliche Kosten aktiviert ist. Sie aktivieren die Sicherheitsstandards im Azure-Portal. Wenn Ihr Mandant am oder nach dem 22. Oktober 2019 erstellt wurde, sind darin möglicherweise Sicherheitsstandards aktiviert. Zum Schutz aller Benutzer werden für alle neuen Mandanten bei deren Erstellung Sicherheitsstandards konfiguriert.

### <a name="whos-it-for"></a>Für wen eignet sich diese Funktion?

- Wenn Sie als Organisation Ihren Sicherheitsstatus verbessern möchten, aber nicht wissen, wie oder womit Sie anfangen sollen, sind die Sicherheitsstandards genau das Richtige für Sie.
- Wenn Sie als Organisation die kostenlose Version der Azure Active Directory-Lizenzierung nutzen, sind die Sicherheitsstandards ebenfalls die richtige Wahl.

### <a name="who-should-use-conditional-access"></a>Wer sollte bedingten Zugriff verwenden?

- Wenn Sie als Organisation derzeit Richtlinien für den bedingten Zugriff verwenden, um Signale zusammenzuführen, Entscheidungen zu treffen und Unternehmensrichtlinien durchzusetzen, sind die Sicherheitsstandards wahrscheinlich nicht für Sie geeignet. 
- Wenn Sie als Organisation über Azure Active Directory Premium-Lizenzen verfügen, sind die Sicherheitsstandards wahrscheinlich nicht die richtige Wahl für Sie.
- Bei komplexen Sicherheitsanforderungen empfiehlt sich ggf. die Verwendung von bedingtem Zugriff.

## <a name="policies-enforced"></a>Erzwungene Richtlinien

### <a name="unified-multi-factor-authentication-registration"></a>Einheitliche Registrierung für die mehrstufige Authentifizierung

Alle Benutzer in Ihrem Mandanten müssen sich für mehrstufige Authentifizierung (MFA) in Form von Azure AD Multi-Factor Authentication registrieren. Benutzer haben 14 Tage Zeit, sich mithilfe der Microsoft Authenticator-App für Azure AD Multi-Factor Authentication zu registrieren. Nach Ablauf der 14 Tage kann sich der Benutzer erst nach erfolgter Registrierung anmelden. Die 14-tägige Frist eines Benutzers beginnt nach seiner ersten erfolgreichen interaktiven Anmeldung nach Aktivierung der Sicherheitsstandards.

### <a name="protecting-administrators"></a>Schützen von Administratoren

Benutzer mit privilegiertem Zugriff besitzen erweiterten Zugriff auf Ihre Umgebung. Aufgrund der weitreichenden Befugnisse, die diese Konten haben, sollten Sie sie mit Bedacht verwalten. Eine gängige Methode zur Verbesserung des Schutzes von privilegierten Konten ist eine strengere Form der Kontoüberprüfung für die Anmeldung. In Azure AD können Sie eine striktere Kontoüberprüfung erreichen, indem Sie mehrstufige Authentifizierung verlangen.

Nachdem die Registrierung mit Azure AD Multi-Faktor-Authentifizierung abgeschlossen ist, wird von den folgenden Azure AD-Administratorrollen bei jeder Anmeldung eine zusätzliche Authentifizierung erfordert:

- Globaler Administrator
- Anwendungsadministrator
- Authentifizierungsadministrator
- Rechnungsadministrator
- Cloudanwendungsadministrator
- Administrator für den bedingten Zugriff
- Exchange-Administrator
- Helpdesk-Administrator
- Kennwortadministrator
- Privilegierter Authentifizierungsadministrator
- Sicherheitsadministrator
- SharePoint-Administrator
- Benutzeradministrator

> [!WARNING]
> Stellen Sie sicher, dass Ihrem Verzeichnis mindestens zwei Konten mit Berechtigungen vom Typ „Globaler Administrator“ zugewiesen sind. Dies ist hilfreich, wenn ein globaler Administrator gesperrt wird. Ausführlichere Informationen finden Sie im Artikel [Verwalten von Konten für den Notfallzugriff in Azure AD](../roles/security-emergency-access.md).

### <a name="protecting-all-users"></a>Schützen aller Benutzer

Wir gehen häufig davon aus, dass nur Administratorkonten durch eine mehrstufige Authentifizierung geschützt werden müssen. Administratoren haben umfassenden Zugriff auf vertrauliche Informationen und können Änderungen an abonnementweiten Einstellungen vornehmen. Angriffe richten sich jedoch häufig gegen Endbenutzer. 

Nachdem diese Angreifer sich Zugang verschafft haben, können sie im Namen des ursprünglichen Kontoinhabers Zugriff auf privilegierte Informationen anfordern. Sie können sogar das gesamte Verzeichnis herunterladen, um einen Phishing-Angriff auf die gesamte Organisation auszuführen. 

Eine gängige Methode zur Verbesserung des Schutzes für alle Benutzer besteht in einer strengeren Kontoüberprüfung, beispielsweise durch eine mehrstufige Authentifizierung (MFA). Nachdem die Benutzer die MFA-Registrierung abgeschlossen haben, werden sie bei Bedarf zu einer zusätzlichen Authentifizierung aufgefordert. Eine solche Aufforderung erfolgt in erster Linie dann, wenn sie sich mit einem neuen Gerät oder einer neuen Anwendung authentifizieren, oder wenn sie kritische Rollen und Aufgaben ausführen. Diese Funktion schützt alle Anwendungen, die bei Azure AD registriert sind (einschließlich SaaS-Anwendungen).

### <a name="blocking-legacy-authentication"></a>Blockieren der Legacyauthentifizierung

Von Azure AD werden verschiedene Authentifizierungsprotokolle (einschließlich Legacyauthentifizierung) unterstützt, damit Benutzer problemlos auf Ihre Cloud-Apps zugreifen können. Der Begriff *Legacyauthentifizierung* bezieht sich auf eine Authentifizierungsanforderung von:

- Clients, die keine moderne Authentifizierung verwenden (z. B. ein Office 2010-Client)
- jedem Client, der ältere E-Mail-Protokolle wie IMAP, SMTP oder POP3 verwendet

Heutzutage sind die meisten gefährdenden Anmeldeversuche auf Legacyauthentifizierungen zurückzuführen. Die Legacyauthentifizierung unterstützt keine mehrstufige Authentifizierung. Selbst wenn Sie in Ihrem Verzeichnis eine MFA-Richtlinie aktiviert haben, kann sich ein Angreifer mit einem älteren Protokoll authentifizieren und die mehrstufige Authentifizierung umgehen. 

Nach Aktivierung der Sicherheitsstandards in Ihrem Mandanten werden alle Authentifizierungsanforderungen blockiert, die über ein Legacyprotokoll an einen beliebigen Mandanten gerichtet werden. Durch die Sicherheitsstandards ist die Standardauthentifizierung für Exchange Active Sync blockiert.

> [!WARNING]
> Stellen Sie vor dem Aktivieren von Sicherheitsstandards sicher, dass Ihre Administratoren keine älteren Authentifizierungsprotokolle verwenden. Weitere Informationen finden Sie unter [Blockieren der Legacyauthentifizierung](concept-fundamentals-block-legacy-authentication.md).

- [Einrichten eines Multifunktionsgeräts oder einer -anwendung zum Senden von E-Mails mit Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Schützen privilegierter Aktionen

Organisationen verwenden verschiedene Azure-Dienste, die über die Azure Resource Manager-API verwaltet werden. Hierzu zählen beispielsweise:

- Azure-Portal 
- Azure PowerShell 
- Azure CLI

Benutzer, die Dienste mit Azure Resource Manager verwalten, verfügen über weitreichende Berechtigungen. Beispielsweise können mit Azure Resource Manager mandantenweite Konfigurationen wie Diensteinstellungen und die Abonnementabrechnung geändert werden. Die einstufige Authentifizierung ist für verschiedene Bedrohungen anfällig, beispielsweise für Phishing- und Kennwortspray-Angriffe. 

Es ist wichtig, die Identität der Benutzer zu überprüfen, die auf Azure Resource Manager zugreifen und Konfigurationen aktualisieren möchten. Sie überprüfen die Identität von Benutzern, indem Sie vor der Zugriffserteilung eine zusätzliche Authentifizierung anfordern.

Nach Aktivierung der Sicherheitsstandards in Ihrem Mandanten muss jeder Benutzer, der auf das Azure-Portal, Azure PowerShell oder die Azure CLI zugreift, eine zusätzliche Authentifizierung durchlaufen. Diese Richtlinie gilt für alle Benutzer mit Zugriff auf Azure Resource Manager – nicht nur für Administratoren. 

> [!NOTE]
> Bei Mandanten vor Exchange Online 2017 ist die moderne Authentifizierung standardmäßig deaktiviert. Sie müssen die [moderne Authentifizierung aktivieren](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online), um eine mögliche Anmeldeschleife bei der Authentifizierung über diese Mandanten zu vermeiden.

> [!NOTE]
> Das Azure AD Connect-Synchronisierungskonto ist von den Sicherheitsstandards ausgenommen. Sie werden nicht aufgefordert, sich für die mehrstufige Authentifizierung zu registrieren bzw. diese auszuführen. Organisationen sollten dieses Konto nicht für andere Zwecke verwenden.

## <a name="deployment-considerations"></a>Überlegungen zur Bereitstellung

Nachfolgend werden zusätzliche Aspekte im Zusammenhang mit der Bereitstellung von Sicherheitsstandards beleuchtet.

### <a name="authentication-methods"></a>Authentifizierungsmethoden

Diese kostenlosen Sicherheitsstandards ermöglichen die Registrierung für und Verwendung von Azure AD Multi-Factor Authentication **unter alleiniger Verwendung der Microsoft Authenticator-App mit Benachrichtigungen**. Der bedingte Zugriff ermöglicht die Verwendung und Aktivierung einer beliebigen Authentifizierungsmethode, für die sich der Administrator entscheidet.

| Methode | Standardwerte für die Sicherheit | Bedingter Zugriff |
| --- | --- | --- |
| Benachrichtigung über mobile App | X | X |
| Prüfcode aus mobiler App oder Hardwaretoken | X** | X |
| Textnachricht an Telefon |   | X |
| Auf Telefon anrufen |   | X |
| App-Kennwörter |   | X*** |

- ** Benutzer können Prüfcodes von der Microsoft Authenticator-App verwenden, können sich jedoch nur mit der Benachrichtigungsoption registrieren.
- ** App-Kennwörter sind nur in Szenarien mit benutzerbasierter MFA mit Legacyauthentifizierung verfügbar, wenn diese Methode von Administratoren aktiviert wurde.

> [!WARNING]
> Deaktivieren Sie keine Methoden für Ihre Organisation, wenn Sie Sicherheitsstandards verwenden. Das Deaktivieren von Methoden kann dazu führen, dass Sie sich von Ihrem Mandanten abmelden. Lassen Sie alle **Methoden für Benutzer verfügbar** im [Portal für MFA-Diensteinstellungen](../authentication/howto-mfa-getstarted.md#choose-authentication-methods-for-mfa) aktiviert.

### <a name="disabled-mfa-status"></a>Deaktivierter MFA-Status

Wenn Ihre Organisation die benutzerbasierte Azure AD Multi-Factor Authentication bereits verwendet hat, sollten Sie nicht beunruhigt sein, wenn auf der Statusseite der mehrstufigen Authentifizierung keine Benutzer mit dem Status **Aktiviert** oder **Erzwungen** angezeigt werden. **Deaktiviert** ist der geeignete Status für Benutzer, die Sicherheitsstandards oder auf bedingtem Zugriff basierende Azure AD Multi-Factor Authentication verwenden.

### <a name="conditional-access"></a>Bedingter Zugriff

Sie können den bedingten Zugriff zum Konfigurieren von Richtlinien verwenden. Diese sind mit den Sicherheitsstandards vergleichbar, bieten aber eine höhere Granularität (einschließlich Benutzerausschlüsse), die bei Sicherheitsstandards nicht verfügbar ist. Wenn Sie mit dem bedingtem Zugriff arbeiten und in Ihrer Umgebung die Richtlinien für bedingten Zugriff aktiviert sind, stehen Ihnen die Sicherheitsstandards nicht zur Verfügung. Wenn Sie über eine Lizenz für die Bereitstellung des bedingten Zugriffs verfügen, aber keine Richtlinien für bedingten Zugriff in Ihrer Umgebung aktiviert sind, können Sie die Sicherheitsstandards so lange nutzen, bis Sie Richtlinien für bedingten Zugriff aktivieren. Weitere Informationen zur Azure AD-Lizenzierung finden Sie auf der Seite [Azure Active Directory (AD) – Preise](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

![Warnmeldung mit dem Hinweis, dass Sicherheitsstandards und bedingter Zugriff nicht gleichzeitig verwendet werden können](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Nachfolgend finden Sie Schrittanleitungen zur Verwendung des bedingten Zugriffs, um eine Reihe von Richtlinien zu konfigurieren, die einen guten Ausgangspunkt für den Schutz Ihrer Identitäten darstellen:

- [Vorschreiben der MFA für Administratoren](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Vorschreiben der MFA für die Azure-Verwaltung](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blockieren älterer Authentifizierungsmethoden](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Erzwingen der MFA für alle Benutzer](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

## <a name="enabling-security-defaults"></a>Aktivieren von Sicherheitsstandards

So aktivieren Sie Sicherheitsstandards in Ihrem Verzeichnis

1. Melden Sie sich beim  [Azure-Portal](https://portal.azure.com)  als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Wechseln Sie zu  **Azure Active Directory** > **Eigenschaften**.
1. Wählen Sie **Sicherheitsstandards verwalten** aus.
1. Legen Sie die Option **Sicherheitsstandards aktivieren** auf **Ja** fest.
1. Wählen Sie **Speichern** aus.

## <a name="disabling-security-defaults"></a>Deaktivieren von Sicherheitsstandards

Organisationen, die Richtlinien für den bedingten Zugriff implementieren, die Sicherheitsstandards ersetzen, müssen Sicherheitsstandards deaktivieren. 

![Warnmeldung zur Deaktivierung von Sicherheitsstandards für das Aktivieren des bedingten Zugriffs](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

So deaktivieren Sie Sicherheitsstandards in Ihrem Verzeichnis

1. Melden Sie sich beim  [Azure-Portal](https://portal.azure.com)  als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Wechseln Sie zu  **Azure Active Directory** > **Eigenschaften**.
1. Wählen Sie **Sicherheitsstandards verwalten** aus.
1. Legen Sie die Option **Sicherheitsstandards aktivieren** auf **Nein** fest.
1. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

[Allgemeine Richtlinien für bedingten Zugriff](../conditional-access/concept-conditional-access-policy-common.md)
