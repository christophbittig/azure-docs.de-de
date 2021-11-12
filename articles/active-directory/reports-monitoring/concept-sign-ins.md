---
title: Anmeldeprotokolle in Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die Anmeldeprotokolle in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/25/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad322032237d8e65fd82fea74bf359fcd65c3840
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997227"
---
# <a name="sign-in-logs-in-azure-active-directory"></a>Anmeldeprotokolle in Azure Active Directory

Als IT-Administrator müssen Sie wissen, wie Ihre IT-Umgebung funktioniert. Anhand der Informationen zur Integrität Ihres Systems können Sie bewerten, ob und wie Sie auf potenzielle Probleme reagieren müssen. 

Um Sie bei diesem Ziel zu unterstützen, bietet Ihnen das Azure Active Directory-Portal Zugriff auf drei Aktivitätsprotokolle:

- **[Anmeldungen](concept-sign-ins.md)** : Informationen zu Anmeldungen und zur Verwendung Ihrer Ressourcen durch Ihre Benutzer.
- **[Überwachung](concept-audit-logs.md)** : Informationen zu Änderungen, die auf Ihren Mandanten angewendet wurden, z. B. Benutzer- und Gruppenverwaltung oder Updates, die auf die Ressourcen Ihres Mandanten angewendet wurden.
- **[Bereitstellung](concept-provisioning-logs.md)** – Aktivitäten durch den Bereitstellungsdienst, z. B. die Erstellung einer Gruppe in ServiceNow oder eines aus Workday importierten Benutzers.

In diesem Artikel erhalten Sie einen Überblick über den Bericht zu Anmeldeaktivitäten.


## <a name="what-can-you-do-with-it"></a>Was können Sie damit machen?

Im Anmeldeprotokoll finden Sie Antworten auf Fragen wie die folgenden:

- Wie sieht das Anmeldemuster eines Benutzers aus?

- Wie viele Benutzer haben sich im Laufe einer Woche angemeldet?

- Wie lautet der Status dieser Anmeldungen?


## <a name="who-can-access-it"></a>Wer kann auf sie zugreifen?

Sie können jederzeit über diesen Link auf Ihren eigenen Anmeldeverlauf zugreifen: [https://mysignins.microsoft.com](https://mysignins.microsoft.com)

Um auf das Anmeldeprotokoll zugreifen zu können, müssen Sie Folgendes sein:

- Ein globaler Administrator:

- Ein Benutzer mit einer der folgenden Rollen:
    - Sicherheitsadministrator

    - Sicherheitsleseberechtigter

    - Globaler Leser

    - Berichtsleser



## <a name="what-azure-ad-license-do-you-need"></a>Welche Azure AD-Lizenz benötigen Sie?

Der Bericht mit den Anmeldeaktivitäten ist in [allen Editionen von Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) verfügbar. Wenn Sie über eine Azure Active Directory P1- oder P2-Lizenz verfügen, können Sie auch über die Microsoft Graph-API auf den Bericht zur Anmeldeaktivität zugreifen.


## <a name="where-can-you-find-it-in-the-azure-portal"></a>Wo finden Sie die Protokolle im Azure-Portal?

Das Azure-Portal bietet Ihnen mehrere Optionen für den Zugriff auf das Protokoll. Im Azure Active Directory-Menü können Sie beispielsweise im Abschnitt **Überwachung** öffnen.  

![Öffnen von Anmeldeprotokollen](./media/concept-sign-ins/sign-ins-logs-menu.png)

Zudem können Sie über diesen Link direkt zu den Anmeldeprotokollen gelangen: [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)


## <a name="what-is-the-default-view"></a>Was ist die Standansicht?

Ein Anmeldungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Anmeldedatum
- Zugehöriger Benutzer
- Die Anwendung, bei der sich der Benutzer angemeldet hat
- Anmeldestatus
- Status der Risikoerkennung
- Status der MFA-Anforderung (Multi-Factor Authentication)

![Screenshot: Anmeldungen bei Office 365 SharePoint Online](./media/concept-sign-ins/sign-in-activity.png "Anmeldeaktivität")

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

![Screenshot: Seite „Anmeldungen“ mit der Option „Spalten“](./media/concept-sign-ins/19.png "Anmeldeaktivität")

Im Dialogfeld **Spalten** erhalten Sie Zugriff auf die auswählbaren Attribute. In einem Bericht zu Anmeldeaktivitäten dürfen keine Felder enthalten sein, die mehr als einen Wert für eine beliebige Anmeldeanforderung als Spalte aufweisen. Dies gilt beispielsweise für Authentifizierungsdetails, Daten zum bedingten Zugriff und den Netzwerkspeicherort.   

![Screenshot: Dialogfeld „Spalten“ mit auszuwählenden Attributen](./media/concept-sign-ins/columns.png "Anmeldeaktivität")

Wählen Sie in der Listenansicht ein Element aus, um ausführlichere Informationen zu erhalten.

![Screenshot: Ansicht mit ausführlichen Informationen](./media/concept-sign-ins/basic-sign-in.png "Anmeldeaktivität")



## <a name="sign-in-error-code"></a>Anmeldefehler

Wenn bei der Anmeldung ein Fehler aufgetreten ist, erhalten Sie weitere Informationen zur Ursache im Abschnitt **Grundlegende Informationen** des zugehörigen Protokollelements. 

![Anmeldefehlercode](./media/concept-all-sign-ins/error-code.png)
 
Während das Protokollelement einen Fehlergrund bereitstellt, gibt es Fälle, in denen Sie möglicherweise weitere Informationen mit dem Tool für die [Fehlersuche bei der Anmeldung](https://login.microsoftonline.com/error) erhalten. Wenn verfügbar, stellt dieses Tool beispielsweise Schritte zur Problembehebung bereit.  

![Fehlercode-Suchtool](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Filtern von Anmeldeaktivitäten


Sie können die Daten in einem Protokoll filtern, um sie gemäß Ihren Anforderungen einzugrenzen:

![Screenshot: Option „Filter hinzufügen“](./media/concept-sign-ins/04.png "Anmeldeaktivität")

**Anforderungs-ID-** : ID der Anforderung, die Sie interessiert.

**Benutzer**: Name oder Benutzerprinzipalname des Benutzers, der Sie interessiert.

**Anwendung**: Name der Zielanwendung.
 
**Status**: der Anmeldestatus, der Sie interessiert:

- Erfolg

- Fehler

- Unterbrochen


**IP-Adresse**: IP-Adresse des Geräts, mit dem die Verbindung mit Ihrem Mandanten hergestellt wird.

**Standort**: Standort, von dem aus die Verbindung eingeleitet wurde:

- City

- Bundesland/Kanton

- Land/Region


**Ressource**: Name des Diensts, der für die Anmeldung verwendet wird.


**Ressourcen-ID**: ID des Diensts, der für die Anmeldung verwendet wird.


**Client-App**: Typ der Client-App, die zum Herstellen einer Verbindung mit Ihrem Mandanten verwendet wird:

![Client-App-Filter](./media/concept-sign-ins/client-app-filter.png)


> [!NOTE]
> Aufgrund von Datenschutzzusagen füllt Azure AD dieses Feld für den Basismandanten im Falle eines mandantenübergreifenden Szenarios nicht auf.


|Name|Moderne Authentifizierung|BESCHREIBUNG|
|---|:-:|---|
|Authentifiziertes SMTP| |Wird von POP- und IMAP-Clients zum Senden von E-Mails verwendet.|
|AutoErmittlung| |Wird von Outlook- und EAS-Clients verwendet, um Postfächer in Exchange Online zu suchen und eine Verbindung damit herzustellen.|
|Exchange ActiveSync| |Dieser Filter zeigt alle Anmeldeversuche, bei denen das EAS-Protokoll versucht wurde.|
|Browser|![Blaues Häkchen](./media/concept-sign-ins/check.png)|Zeigt alle Anmeldeversuche von Benutzern über Webbrowser|
|Exchange ActiveSync| | Zeigt alle Anmeldeversuche von Benutzern mit Client-Apps, die Exchange Active Sync zur Herstellung einer Verbindung mit Exchange Online verwenden|
|Exchange Online PowerShell| |Wird zum Herstellen einer Verbindung mit Exchange Online über Remote-PowerShell verwendet. Wenn Sie die Standardauthentifizierung für Exchange Online PowerShell blockieren, müssen Sie das Exchange Online PowerShell-Modul verwenden, um eine Verbindung herzustellen. Anweisungen finden Sie unter [Herstellen einer Verbindung mit Exchange Online PowerShell mithilfe der mehrstufigen Authentifizierung](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Exchange-Webdienste| |Eine Programmierschnittstelle, die von Outlook, Outlook für Mac und Drittanbieter-Apps verwendet wird.|
|IMAP4| |Älterer E-Mail-Client, der IMAP zum Abrufen von E-Mails verwendet.|
|MAPI über HTTP| |Wird von Outlook 2010 und höher verwendet.|
|Mobile Apps und Desktop-Apps|![Blaues Häkchen](./media/concept-sign-ins/check.png)|Zeigt alle Anmeldeversuche von Benutzern mithilfe von mobilen Anwendungen und Desktopclients.|
|Offlineadressbuch| |Eine Kopie der Adressenlistensammlungen, die von Outlook heruntergeladen und verwendet werden.|
|Outlook Anywhere (RPC über HTTP)| |Wird bis Outlook 2016 verwendet.|
|Outlook-Dienst| |Wird von der Mail- und Kalender-App für Windows 10 verwendet.|
|POP3| |Älterer E-Mail-Client, der POP3 zum Abrufen von E-Mails verwendet.|
|Reporting Web Services| |Wird zum Abrufen von Berichtsdaten in Exchange Online verwendet.|
|Andere Clients| |Zeigt alle Anmeldeversuche von Benutzern, bei denen die Client-App nicht inbegriffen oder unbekannt ist.|







**Betriebssystem**: das Betriebssystem, das auf dem Gerät ausgeführt wird, mit dem die Anmeldung bei Ihrem Mandanten erfolgt. 


**Gerätebrowser**: Wenn die Verbindung in einem Browser eingeleitet wurde, können Sie in diesem Feld nach Browsername filtern.


**Korrelations-ID**: Korrelations-ID der Aktivität.




**Bedingter Zugriff**: Status der angewendeten Regeln für bedingten Zugriff

- **Nicht angewendet**: Keine Richtlinie betraf den Benutzer und die Anwendung bei der Anmeldung.

- **Erfolg**: Eine oder mehrere Richtlinien für bedingten Zugriff betrafen den Benutzer und die Anwendung (aber nicht notwendigerweise die anderen Bedingungen) bei der Anmeldung. 

- **Fehler**: Die Anmeldung hat zwar die Benutzer- und Anwendungsbedingung mindestens einer Richtlinie für bedingten Zugriff erfüllt, die Gewährungssteuerelemente wurden jedoch nicht erfüllt oder sind auf Blockieren des Zugriffs eingestellt.









## <a name="download-sign-in-activities"></a>Herunterladen von Anmeldeaktivitäten

Durch Klicken auf die Option **Herunterladen** können Sie eine CSV- oder JSON-Datei der letzten 250.000 Datensätze erstellen. Beginnen Sie damit, [Daten zu Anmeldungen herunterzuladen](./howto-download-logs.md), wenn Sie sie außerhalb des Azure-Portals verwenden möchten.  

![Download](./media/concept-sign-ins/71.png "Download")

> [!IMPORTANT]
> Die Anzahl von Datensätzen, die Sie herunterladen können, ist durch die [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md) eingeschränkt.  


## <a name="sign-ins-data-shortcuts"></a>Tastenkombinationen für Anmeldedaten

Azure AD und das Azure-Portal enthalten weitere Einstiegspunkte für Anmeldedaten:

- Übersicht über Identitätssicherheit und Schutz
- Benutzer
- Gruppen
- Unternehmensanwendungen

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Benutzeranmeldedaten für Identitätssicherheit und Schutz

Mit dem Graphen für Benutzeranmeldungen auf der Übersichtsseite **Identitätssicherheit und Schutz** werden wöchentliche Aggregationen der Anmeldungen angezeigt. Die Standardeinstellung für den Zeitraum beträgt 30 Tage.

![Screenshot: Diagramm der Anmeldungen in einem Monat](./media/concept-sign-ins/06.png "Anmeldeaktivität")

Wenn Sie im Diagramm der Anmeldungen auf einen Tag klicken, wird eine Übersicht über die Anmeldeaktivitäten für den entsprechenden Tag angezeigt.

Jede Zeile in der Liste mit den Anmeldeaktivitäten zeigt Folgendes:

* Wer hat sich angemeldet?
* Welche Anwendung war das Ziel der Anmeldung?
* Welchen Status hat die Anmeldung?
* Welchen MFA-Status hat die Anmeldung?

Durch Klicken auf ein Element können Sie ausführlichere Informationen zum entsprechenden Anmeldevorgang anzeigen:

- Benutzer-ID
- Benutzer
- Username
- Anwendungs-ID
- Application
- Client
- Position
- IP-Adresse
- Date
- MFA erforderlich
- Anmeldestatus

> [!NOTE]
> IP-Adressen werden so ausgestellt, dass es keine definitive Verbindung zwischen einer IP-Adresse und dem physischen Standort des Computers mit dieser Adresse gibt. Das Zuordnen von IP-Adressen wird außerdem durch Faktoren wie Mobilfunkanbieter und VPNs verkompliziert, die IP-Adressen aus zentralen Pools zuweisen, die oft sehr weit von den Orten entfernt sind, an denen das Clientgerät tatsächlich verwendet wird. Derzeit ist das Konvertieren der IP-Adresse in einen physischen Speicherort eine optimale Lösung, die auf Ablaufverfolgungen, Registrierungsdaten, Reverse-Lookups und anderen Informationen basiert.

Wenn Sie auf der Seite **Benutzer** im Abschnitt **Aktivität** auf **Anmeldevorgänge** klicken, wird eine umfassenden Übersicht über alle Benutzeranmeldungen angezeigt.

![Screenshot: Abschnitt „Aktivität“ mit auszuwählenden Anmeldungen](./media/concept-sign-ins/08.png "Anmeldeaktivität")

## <a name="authentication-details"></a>Authentifizierungsdetails

Die Registerkarte **Authentifizierungsdetails** im Anmeldebericht enthält die folgenden Informationen für jeden Authentifizierungsversuch:

- Eine Liste der angewendeten Authentifizierungsrichtlinien (z. B. bedingter Zugriff, MFA pro Benutzer, Sicherheitsstandards)
- Eine Liste der angewendeten Richtlinien für die Sitzungslebensdauer (z. B. Anmeldehäufigkeit, MFA-Speicherung, konfigurierbare Tokengültigkeitsdauer)
- Die Abfolge der für die Anmeldung verwendeten Authentifizierungsmethoden
- Ob der Authentifizierungsversuch erfolgreich war oder nicht
- Ausführliche Informationen zu den Gründen, aus denen der Authentifizierungsversuch erfolgreich war oder nicht

Mit diesen Informationen können Administratoren die Problembehandlung für jeden Schritt bei der Anmeldung eines Benutzers und Nachverfolgungen durchführen:

- Umfang der durch mehrstufige Authentifizierung geschützten Anmeldungen 
- Grund für die Authentifizierungsaufforderung basierend auf den Richtlinien für die Sitzungslebensdauer
- Nutzungs- und Erfolgsraten für jede Authentifizierungsmethode 
- Verwendung kennwortloser Authentifizierungsmethoden (z. B. kennwortlose Anmeldung per Telefon, FIDO2 und Windows Hello for Business) 
- Wie häufig Authentifizierungsanforderungen durch Tokenansprüche erfüllt werden (wobei Benutzer nicht interaktiv zur Eingabe eines Kennworts, eines SMS-OTP usw. aufgefordert werden)

Wählen Sie beim Anzeigen des Anmeldeberichts die Registerkarte **Authentifizierungsdetails** aus: 

![Screenshot der Registerkarte „Authentifizierungsdetails“](media/concept-sign-ins/auth-details-tab.png)

>[!NOTE]
>Der **OATH-Überprüfungscode** wird sowohl für OATH-Hardware- als auch für Softwaretoken (z. B. die Microsoft Authenticator-App) als Authentifizierungsmethode protokolliert.

>[!IMPORTANT]
>Auf der Registerkarte **Authentifizierungsdetails** können zunächst unvollständige oder ungenaue Daten angezeigt werden, bis die Protokollinformationen vollständig aggregiert sind. Bekannte Beispiele sind: 
>- Eine **Erfüllt durch Anspruch im Token**-Meldung wird fälschlicherweise angezeigt, wenn Anmeldeereignisse anfänglich protokolliert werden. 
>- Die Zeile **Primäre Authentifizierung** wird anfänglich nicht protokolliert. 


## <a name="usage-of-managed-applications"></a>Nutzung von verwalteten Anwendungen

Mit einer anwendungsorientierten Ansicht Ihrer Anmeldedaten können Sie beispielsweise folgende Fragen beantworten:

* Wer verwendet meine Anwendungen?
* Welche drei Anwendungen sind in Ihrer Organisation am beliebtesten?
* Wie arbeitet meine neueste Anwendung?

Der Einstiegspunkt für diese Daten sind die drei wichtigsten Anwendungen in Ihrer Organisation. Die Daten sind im Bericht über die letzten 30 Tage im Abschnitt **Übersicht** unter **Unternehmensanwendungen** enthalten.

![Der Screenshot zeigt, wo Sie die Übersicht auswählen können.](./media/concept-sign-ins/10.png "Anmeldeaktivität")

Der Graph zur App-Nutzung gibt die wöchentlichen Aggregationen von Anmeldungen für Ihre beliebtesten drei Anwendungen in einem bestimmten Zeitraum an. Die Standardeinstellung für den Zeitraum beträgt 30 Tage.

![Screenshot: App-Nutzung für den Zeitraum von einem Monat](./media/concept-sign-ins/graph-chart.png "Anmeldeaktivität")

Wenn Sie möchten, können Sie den Fokus auf eine bestimmte Anwendung festlegen.

![Reporting](./media/concept-sign-ins/single-app-usage-graph.png "Berichterstellung")

Wenn Sie im Graph für die App-Nutzung auf einen Tag klicken, wird eine ausführliche Liste mit den Anmeldeaktivitäten angezeigt.

Mit der Option **Anmeldungen** können Sie eine vollständige Übersicht über alle Anmeldeereignisse für Ihre Anwendungen anzeigen.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365-Aktivitätsprotokolle

Sie können Microsoft 365-Aktivitätsprotokolle im [Microsoft 365 Admin Center](/office365/admin/admin-overview/about-the-admin-center) anzeigen. Beachten Sie, dass Microsoft 365- und Azure AD-Aktivitätsprotokolle eine beträchtliche Anzahl von Verzeichnisressourcen gemeinsam nutzen. Nur das Microsoft 365 Admin Center bietet eine vollständige Übersicht über die Microsoft 365-Aktivitätsprotokolle. 

Mithilfe der [Office 365-Verwaltungs-APIs](/office/office-365-management-api/office-365-management-apis-overview) können Sie auch programmgesteuert auf die Microsoft 365-Aktivitätsprotokolle zugreifen.

## <a name="next-steps"></a>Nächste Schritte

* [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md)
* [Latenzen bei Azure Active Directory-Berichten](reference-reports-latencies.md)
* [Microsoft-Erstanbieter-Anwendungen in Anmeldeberichten](/troubleshoot/azure/active-directory/verify-first-party-apps-sign-in#application-ids-for-commonly-used-microsoft-applications)
