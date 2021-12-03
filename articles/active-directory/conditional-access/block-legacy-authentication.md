---
title: Blockieren der Legacyauthentifizierung – Azure Active Directory
description: Erfahren Sie, wie Sie durch Blockieren der Legacyauthentifizierung mithilfe des bedingten Azure AD-Zugriffs Ihren Sicherheitsstatus verbessern.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/12/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, dawoo, jebeckha, grtaylor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 707e5b1e2c9adbe3b30a73a75e89ae7cf7a1beab
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520767"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Gewusst wie: Blockieren der Legacyauthentifizierung bei Azure AD mit bedingtem Zugriff   

Um Ihren Benutzern den einfachen Zugriff auf Ihre Cloud-Apps zu ermöglichen, unterstützt Azure Active Directory (Azure AD) eine Vielzahl von Authentifizierungsprotokollen einschließlich der Legacyauthentifizierung. Die Legacyauthentifizierung unterstützt jedoch keine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA). MFA ist in vielen Umgebungen eine allgemeine Anforderung zum Schutz vor Identitätsdiebstahl. 

> [!NOTE]
> Ab dem 1. Oktober 2022 wird die Standardauthentifizierung für Exchange Online in allen Microsoft 365-Mandanten unabhängig von der Nutzung dauerhaft deaktiviert, mit Ausnahme der SMTP-Authentifizierung.

Alex Weinert, Director of Identity Security bei Microsoft, hebt in seinem am 12. März 2020 veröffentlichten Blogbeitrag [Neue Tools zum Blockieren der Legacyauthentifizierung in Ihrer Organisation](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) hervor, warum Organisationen die Legacyauthentifizierung blockieren sollten und welche weiteren Tools Microsoft für diese Aufgabe bereitstellt:

> Damit die mehrstufige Authentifizierung (MFA) wirksam wird, müssen Sie auch die Legacyauthentifizierung blockieren. Legacyauthentifizierungsprotokolle wie POP, SMTP, IMAP und MAPI können nämlich keine MFA erzwingen und sind dadurch bevorzugte Einstiegspunkte für Angreifer, die Ihre Organisation attackieren...
> 
>... Die Zahlen zur Legacyauthentifizierung sind nach einer Analyse des Azure Active Directory (Azure AD)-Datenverkehrs ziemlich krass:
> 
> - Mehr als 99 Prozent der Kennwort-Spray-Angriffe verwenden Legacyauthentifizierungsprotokolle
> - Mehr als 97 Prozent der Angriffe in Bezug auf Anmeldeinformationen verwenden die Legacyauthentifizierung
> - Bei Azure AD-Konten in Organisationen, welche die Legacyauthentifizierung deaktiviert haben, sind 67 Prozent weniger Angriffe festzustellen als bei Organisation mit aktivierter Legacyauthentifizierung
>

Wenn Ihre Umgebung für das Blockieren der Legacyauthentifizierung bereit ist, um den Schutz Ihres Mandanten zu verbessern, können Sie dieses Ziel mit bedingtem Zugriff erreichen. In diesem Artikel wird erläutert, wie Sie die Richtlinien für bedingten Zugriff konfigurieren können, mit denen die Legacyauthentifizierung für alle Workloads innerhalb Ihrer Mandanten blockiert wird. 

Während der Einführung des Legacyauthentifizierungsschutzes wird ein stufenweiser Ansatz empfohlen, anstatt ihn für alle Benutzer gleichzeitig zu deaktivieren. Kunden können zunächst mit der Deaktivierung der Standardauthentifizierung pro Protokoll beginnen, indem sie Authentifizierungsrichtlinien von Exchange Online nutzen und dann (optional) auch die Legacyauthentifizierung über Richtlinien für bedingten Zugriff blockieren, wenn sie bereit sind.

Kunden ohne Lizenzen, die bedingten Zugriff einschließen, können [Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md) verwenden, um die Legacyauthentifizierung zu blockieren.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie mit den [grundlegenden Konzepten](overview.md) des bedingten Azure AD-Zugriff vertraut sind.

> [!NOTE]
> Richtlinien für den bedingten Zugriff werden durchgesetzt, wenn die First-Factor-Authentifizierung abgeschlossen ist. Der bedingte Zugriff nicht ist als erste Abwehrmaßnahme einer Organisation für Szenarien wie Denial-of-Service-Angriffe (DoS) gedacht, sondern kann Signale von diesen Ereignissen nutzen, um den Zugriff zu bestimmen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Azure AD unterstützt mehrere der am häufigsten verwendeten Protokolle zur Authentifizierung und Autorisierung, einschließlich der Legacyauthentifizierung. Legacyauthentifizierung bezieht sich auf die Standardauthentifizierung, eine weit verbreitete Branchenstandardmethode zum Sammeln von Benutzernamen- und Kennwortinformationen. In der Regel können Legacyauthentifizierungsclients keine Art der zweistufigen Authentifizierung erzwingen. Beispiele für Anwendungen, die häufig oder nur Legacyauthentifizierung verwenden, sind:

- Microsoft Office 2013 oder höher.
- Apps, die E-Mail-Protokolle wie POP, IMAP und SMTP AUTH verwenden.

Weitere Informationen zur Unterstützung der modernen Authentifizierung in Office finden Sie unter [So funktioniert die moderne Authentifizierung für Office-Client-Apps](/microsoft-365/enterprise/modern-auth-for-office-2013-and-2016?view=o365-worldwide).

Eine einstufige Authentifizierung (z. B. mit Benutzername und Kennwort) reicht heutzutage nicht mehr aus. Kennwörter sind unzulänglich, weil sie leicht zu erraten sind, und wir (Menschen) sind schlecht darin, gute Kennwörter auszuwählen. Kennwörter sind auch für verschiedene Angriffe wie Phishing und Kennwort-Spray anfällig. Eine der einfachsten Maßnahmen, die Sie ergreifen können, um sich vor Kennwortsicherheitsverletzungen zu schützen, ist das Implementieren der mehrstufigen Authentifizierung (MFA). Denn selbst wenn ein Angreifer in den Besitz des Kennworts eines Benutzers gelangt, reicht bei Verwendung von MFA das Kennwort allein nicht aus, um sich erfolgreich authentifizieren und auf die Daten zugreifen zu können.

Wie können Sie verhindern, dass Apps mit Legacyauthentifizierung auf Ressourcen Ihres Mandanten zugreifen? Es wird empfohlen, diese Apps einfach mit einer Richtlinie für bedingten Zugriff zu blockieren. Gegebenenfalls können Sie nur bestimmten Benutzern und bestimmten Netzwerkadressen die Verwendung von Apps erlauben, die auf der Legacyauthentifizierung basieren.

Richtlinien für den bedingten Zugriff werden durchgesetzt, nachdem die First-Factor-Authentifizierung abgeschlossen ist. Daher ist der bedingte Zugriff nicht als erste Abwehrmaßnahme für Szenarien wie Denial-of-Service-Angriffe (DoS) gedacht, sondern kann Signale von diesen Ereignissen (z. B. der Risikostufe für die Anmeldung, den Standort der Anforderung usw.) nutzen, um den Zugriff zu bestimmen.

## <a name="implementation"></a>Implementierung

In diesem Abschnitt wird erläutert, wie eine Richtlinie für bedingten Zugriff zum Blockieren der Legacyauthentifizierung konfiguriert wird. 

### <a name="messaging-protocols-that-support-legacy-authentication"></a>Messagingprotokolle, die Legacyauthentifizierung unterstützen

Die folgenden Messagingprotokolle unterstützen die Legacyauthentifizierung:

- Authentifiziertes SMTP: Dient zum Senden authentifizierter E-Mail-Nachrichten.
- AutoErmittlung: wird von Outlook und EAS-Clients verwendet, um Postfächer in Exchange Online zu suchen und diese zu verbinden
- Exchange ActiveSync (EAS): wird zum Herstellen einer Verbindung mit Postfächern in Exchange Online verwendet
- Exchange Online PowerShell: wird zum Herstellen einer Verbindung mit Exchange Online über Remote-PowerShell verwendet Wenn Sie die Standardauthentifizierung für Exchange Online PowerShell blockieren, müssen Sie das Exchange Online PowerShell-Modul verwenden, um eine Verbindung herzustellen. Anweisungen finden Sie unter [Herstellen einer Verbindung mit Exchange Online PowerShell mithilfe der mehrstufigen Authentifizierung](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS): eine Programmierschnittstelle, die von Outlook, Outlook für Mac und Drittanbieter-Apps verwendet wird
- IMAP4: wird von IMAP-E-Mail-Clients verwendet
- MAPI über HTTP (MAPI/HTTP): Primäres Postfachzugriffsprotokoll, das von Outlook 2010 SP2 und höher verwendet wird.
- Offlineadressbuch (OAB): eine Kopie der Adressenlistensammlungen, die von Outlook heruntergeladen und verwendet werden
- Outlook Anywhere (RPC über HTTP): Legacy-Postfachzugriffsprotokoll, das von allen aktuellen Outlook-Versionen unterstützt wird.
- POP3: wird von POP-E-Mail-Clients verwendet
- Berichtswebdienste: Werden zum Abrufen von Berichtsdaten in Exchange Online verwendet.
- Universelles Outlook: wird von der Mail- und Kalender-App für Windows 10 verwendet.
- Andere Clients: andere Protokolle, bei denen die Verwendung älterer Authentifizierungsmethoden identifiziert wird

Weitere Informationen zu diesen Authentifizierungsprotokollen und -diensten finden Sie unter[Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identifizieren der Verwendung der Legacyauthentifizierung

Bevor Sie die Legacyauthentifizierung in Ihrem Verzeichnis blockieren können, müssen Sie zuerst wissen, ob Ihre Benutzer über Apps verfügen, die die Legacyauthentifizierung verwenden, und wie sich dies auf Ihr gesamtes Verzeichnis auswirkt. Sie können Azure AD-Anmeldungsprotokolle verwenden, um herauszufinden, ob Sie die Legacyauthentifizierung verwenden.

1. Navigieren Sie zu **Azure-Portal** > **Azure Active Directory** > **Anmeldungen**.
1. Falls die Spalte „Client-App“ nicht angezeigt wird, fügen Sie sie durch Klicken auf **Spalten** > **Client-App** hinzu.
1. Klicken Sie auf **Filter hinzufügen** > **Client-App**, und wählen Sie alle älteren Authentifizierungsprotokolle aus. Klicken Sie auf eine Stelle außerhalb des Filterdialogfelds, um die Auswahl anzuwenden und das Dialogfeld zu schließen.
1. Wenn Sie die [neuen Berichte zu Anmeldeaktivitäten (Vorschau)](../reports-monitoring/concept-all-sign-ins.md) aktiviert haben, wiederholen Sie die obigen Schritte auch auf der Registerkarte **Benutzeranmeldungen (nicht interaktiv)** .

Durch das Filtern werden Ihnen nur Anmeldeversuche von Legacyauthentifizierungsprotokollen angezeigt. Wenn Sie auf die einzelnen Anmeldeversuche klicken, werden Ihnen weitere Details angezeigt. Das **Client-App**-Feld auf der Registerkarte **Grundlegende Informationen** gibt an, welche Legacyauthentifizierungsprotokolle verwendet wurden.

Diese Protokolle geben an, welche Benutzer weiterhin von der Legacyauthentifizierung abhängig sind, und welche Anwendungen ältere Protokolle für Authentifizierungsanforderungen verwenden. Implementieren Sie für Benutzer, die in diesen Protokollen nicht aufgeführt sind und nachweislich keine Legacyauthentifizierung verwenden, eine Richtlinie für bedingten Zugriff, die nur für diese Benutzer vorgesehen ist.

## <a name="block-legacy-authentication"></a>Blockieren älterer Authentifizierungsmethoden 

Es gibt zwei Möglichkeiten, mit Richtlinien für den bedingten Zugriff ältere Authentifizierungsmethoden zu blockieren.

- [Direktes Blockieren der Legacyauthentifizierung](#directly-blocking-legacy-authentication)
- [Indirektes Blockieren der Legacyauthentifizierung](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Direktes Blockieren der Legacyauthentifizierung

Die einfachste Möglichkeit, die Legacyauthentifizierung in ihrer gesamten Organisation zu blockieren, besteht darin, eine Richtlinie für bedingten Zugriff zu konfigurieren, die speziell für Legacyauthentifizierungs-Clients gilt und den Zugriff blockiert. Wenn Sie der Richtlinie Benutzer und Anwendungen zuweisen, müssen Sie sicherstellen, dass Benutzer und Dienstkonten ausgeschlossen werden, die sich weiterhin mit der Legacyauthentifizierung anmelden müssen. Wenn Sie die Cloud-Apps auswählen, in denen diese Richtlinie angewendet werden soll, wählen Sie alle Cloud-Apps, Ziel-Apps wie Office 365 (empfohlen) oder mindestens Office 365 Exchange Online aus. Konfigurieren Sie die Client-Apps-Bedingung, indem Sie **Exchange ActiveSync-Clients** und **Andere Clients** auswählen. Um den Zugriff für diese Client-Apps zu blockieren, konfigurieren Sie die Zugriffssteuerungen so, dass der Zugriff blockiert wird.

![Konfigurierte Client-Apps-Bedingung zum Blockieren der Legacyauthentifizierung](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Indirektes Blockieren der Legacyauthentifizierung

Auch wenn Ihre Organisation nicht bereit ist, die Legacyauthentifizierung für das gesamte Unternehmen zu blockieren, sollten Sie sicherstellen, dass Anmeldungen mit Legacyauthentifizierung keine Richtlinien umgehen, die Gewährungssteuerelemente erfordern, z. B. eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) oder kompatible/hybrid in Azure AD eingebundene Geräte. Legacyauthentifizierungs-Clients unterstützen bei der Authentifizierung das Senden von Informationen zur mehrstufigen Authentifizierung (MFA), zur Gerätekonformität oder zum Joinzustand an Azure AD nicht. Wenden Sie daher auf alle Clientanwendungen Richtlinien mit Gewährungssteuerelementen an. Dadurch werden Anmeldungen mit Legacyauthentifizierung blockiert, da sie die Gewährungssteuerelemente nicht bedienen können. Mit der allgemeinen Verfügbarkeit der Client-Apps-Bedingung im August 2020 gelten neu erstellte Richtlinien für bedingten Zugriff standardmäßig für alle Client-Apps.

![Standardkonfiguration der Client-Apps-Bedingung](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Wichtige Informationen

Es kann bis zu 24 Stunden dauern, bis die Richtlinie für bedingten Zugriff wirksam wird.

Das Blockieren des Zugriffs mithilfe der Bedingung **Andere Clients** blockiert auch Exchange Online PowerShell und Dynamics 365 mit Standardauthentifizierung.

Durch das Konfigurieren einer Richtlinie für **Andere Clients** wird die gesamte Organisation für bestimmte Clients blockiert, z.B. SPConnect. Dies tritt ein, weil sich ältere Clients auf unerwartete Weise authentifizieren. Dieses Problem gilt nicht für Office-Hauptanwendungen wie ältere Office-Clients.

Sie können alle verfügbaren Gewährungssteuerelemente für die Bedingung **Andere Clients** auswählen. Die Endbenutzererfahrung ist jedoch immer die gleiche: Der Zugriff ist blockiert.

### <a name="sharepoint-online"></a>SharePoint Online

Zum Blockieren des Benutzerzugriffs über die Legacyauthentifizierung auf SharePoint Online müssen Organisationen die Legacyauthentifizierung in SharePoint mit dem PowerShell-Befehl `Set-SPOTenant` deaktivieren und den `-LegacyAuthProtocolsEnabled`-Parameter auf `$false` festlegen. Weitere Informationen zum Festlegen dieses Parameters finden Sie im SharePoint-PowerShell-Referenzdokument zu [Set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant).

## <a name="next-steps"></a>Nächste Schritte

- [Bestimmen der Auswirkung durch Verwendung des reinen Berichtsmodus des bedingten Zugriffs](howto-conditional-access-insights-reporting.md)
- Wenn Sie noch nicht mit dem Konfigurieren von Richtlinien für bedingten Zugriff vertraut sind, sehen Sie sich das Beispiel unter [Anfordern der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für bestimmte Apps über den bedingten Zugriff von Azure Active Directory](../authentication/tutorial-enable-azure-mfa.md) an.
- Weitere Informationen zur Unterstützung der modernen Authentifizierung finden Sie unter [So funktioniert die moderne Authentifizierung für Office-Client-Apps](/office365/enterprise/modern-auth-for-office-2013-and-2016). 
- [Einrichten eines Multifunktionsgeräts oder einer -anwendung zum Senden von E-Mails mit Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)
