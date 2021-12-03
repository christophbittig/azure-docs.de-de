---
title: 'Anmeldeprotokolle in Azure Active Directory: Vorschau | Microsoft-Dokumentation'
description: Übersicht über die Anmeldeprotokolle in Azure Active Directory, einschließlich neuer Features in der Vorschauversion.
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
ms.date: 06/23/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 209b9f66a9d7d8d004162e465c85311886b423fa
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997208"
---
# <a name="sign-in-logs-in-azure-active-directory---preview"></a>Anmeldeprotokolle in Azure Active Directory: Vorschau

Als IT-Administrator müssen Sie wissen, wie Ihre IT-Umgebung funktioniert. Anhand der Informationen zur Integrität Ihres Systems können Sie bewerten, ob und wie Sie auf potenzielle Probleme reagieren müssen. 

Um Sie bei diesem Ziel zu unterstützen, bietet Ihnen das Azure Active Directory-Portal Zugriff auf drei Aktivitätsprotokolle:

- **[Anmeldung:](concept-sign-ins.md)** Informationen zu Anmeldungen und zur Verwendung Ihrer Ressourcen durch Ihre Benutzer
- **[Überwachung](concept-audit-logs.md)** : Informationen zu Änderungen, die auf Ihren Mandanten angewendet wurden, z. B. Benutzer- und Gruppenverwaltung oder Updates, die auf die Ressourcen Ihres Mandanten angewendet wurden.
- **[Bereitstellung](concept-provisioning-logs.md)** : Vom Bereitstellungsdienst ausgeführte Aktivitäten, z. B. die Erstellung einer Gruppe in ServiceNow oder der Import eines Benutzers aus Workday.


Das klassische Protokoll zu Anmeldeaktivitäten in Azure Active Directory bietet Ihnen einen Überblick über interaktive Benutzeranmeldungen. Außerdem haben Sie jetzt Zugriff auf drei zusätzliche Anmeldeprotokolle, die sich zurzeit in der Vorschau befinden:

- Nicht interaktive Benutzeranmeldungen

- Dienstprinzipalanmeldungen

- Verwaltete Identitäten für Azure-Ressourcenanmeldungen

In diesem Artikel erhalten Sie einen Überblick über den Bericht zu Anmeldeaktivitäten mit der Vorschau von nicht interaktiven Identitäten, Anwendungsidentitäten und verwalteten Identitäten für Azure-Ressourcenanmeldungen. Informationen zum Anmeldebericht ohne die Vorschaufeatures finden Sie unter [Anmeldeprotokolle in Azure Active Directory](concept-sign-ins.md).



## <a name="what-can-you-do-with-it"></a>Was können Sie damit machen?

Das Protokoll zu Anmeldeaktivitäten enthält etwa Antworten auf die folgenden Fragen:

- Wie sieht das Anmeldemuster eines Benutzers, einer Anwendung oder eines Dienstes aus?

- Wie viele Benutzer, Apps oder Dienste haben sich im Laufe einer Woche angemeldet?

- Wie lautet der Status dieser Anmeldungen?


## <a name="who-can-access-the-data"></a>Wer kann auf die Daten zugreifen?

- Benutzer mit den Rollen „Sicherheitsadministrator“, „Sicherheitsleseberechtigter“ und „Berichtsleser“

- Globale Administratoren

- Jeder Benutzer (Nicht-Administratoren) kann auf seine eigenen Anmeldungen zugreifen. 

## <a name="what-azure-ad-license-do-you-need"></a>Welche Azure AD-Lizenz benötigen Sie?

Ihrem Mandanten muss eine Azure AD Premium-Lizenz zugeordnet sein, damit Anmeldeaktivitäten angezeigt werden. Unter [Erste Schritte mit Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) erfahren Sie, wie Sie ein Upgrade für Ihre Azure Active Directory-Edition durchführen. Wenn Sie vor dem Upgrade über keine Datenaktivitäten verfügten, dauert es ein paar Tage, bis die Daten in den Protokollen angezeigt werden, nachdem Sie ein Upgrade auf eine Premium-Lizenz durchgeführt haben.




## <a name="where-can-you-find-it-in-the-azure-portal"></a>Wo finden Sie die Protokolle im Azure-Portal?

Das Azure-Portal bietet Ihnen mehrere Optionen für den Zugriff auf das Protokoll. Im Azure Active Directory-Menü können Sie beispielsweise im Abschnitt **Überwachung** öffnen.  

![Öffnen von Anmeldeprotokollen](./media/concept-sign-ins/sign-ins-logs-menu.png)

Außerdem können Sie über diesen Link direkt zum Anmeldeprotokoll gelangen: [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

Auf der Anmeldeseite können Sie zwischen Folgendem wechseln:

- **Interaktive Benutzeranmeldungen** – Anmeldungen, bei denen ein Benutzer einen Authentifizierungsfaktor angibt, z. B. ein Kennwort, eine Antwort über eine MFA-App, einen biometrischen Faktor oder einen QR-Code.

- **Nicht interaktive Benutzeranmeldungen** – Anmeldungen, die von einem Client im Namen eines Benutzers vorgenommen werden. Diese Anmeldungen erfordern keinerlei Interaktion oder keinen Authentifizierungsfaktor vom Benutzer. So muss ein Benutzer beispielsweise bei seiner Authentifizierung und Autorisierung mithilfe von Aktualisierungs- und Zugriffstoken keine Anmeldeinformationen eingeben.

- **Dienstprinzipalanmeldungen** – Anmeldungen durch Apps und Dienstprinzipale, bei denen kein Benutzer beteiligt ist. Bei diesen Anmeldungen stellt die App oder der Dienst eigene Anmeldeinformationen für die Authentifizierung oder den Zugriff auf Ressourcen bereit.

- **Verwaltete Identitäten für Azure-Ressourcenanmeldungen** – Anmeldungen durch Azure-Ressourcen mit Geheimnissen, die von Azure verwaltet werden. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../managed-identities-azure-resources/overview.md). 


![Anmeldeprotokolltypen](./media/concept-all-sign-ins/sign-ins-report-types.png)



Jede Registerkarte auf der Anmeldeseite enthält die unten aufgeführten Standardspalten. Einige Registerkarten enthalten zusätzliche Spalten:

- Sign-in date (Anmeldedatum)

- Anfrage-ID

- Benutzername oder Benutzer-ID

- Anwendungsname oder Anwendungs-ID

- Status der Anmeldung

- IP-Adresse des für die Anmeldung verwendeten Geräts



### <a name="interactive-user-sign-ins"></a>Interaktive Benutzeranmeldungen


Interaktive Benutzeranmeldungen sind Anmeldungen, bei denen ein Benutzer einen Authentifizierungsfaktor für Azure AD angibt oder aber mit Azure AD oder einer Hilfsanwendung (z. B. der Microsoft Authenticator-App) direkt interagiert. Zu den von Benutzern angegebenen Faktoren gehören Kennwörter, Antworten auf MFA-Anfragen, biometrische Faktoren oder QR-Codes, die ein Benutzer für Azure AD oder eine Hilfsanwendung angibt.

> [!NOTE]
> Dieses Protokoll enthält auch Verbundanmeldungen von Identitätsanbietern, die im Verbund mit Azure AD sind.  



> [!NOTE] 
> Das Protokoll mit interaktiven Benutzeranmeldungen enthält einige nicht interaktive Anmeldungen von Microsoft Exchange-Clients. Obwohl diese Anmeldungen nicht interaktiv waren, werden sie zur besseren Sichtbarkeit in das Protokoll zu interaktiven Benutzeranmeldungen eingeschlossen. Seitdem das Protokoll zu nicht interaktiven Benutzeranmeldungen im November 2020 als öffentliche Vorschauversion veröffentlicht wurde, werden die Protokolle zu nicht interaktiven Anmeldeereignissen richtigerweise dort erfasst. 


**Berichtsgröße:** klein <br> 
**Beispiele:**

- Ein Benutzer gibt seinen Benutzernamen und sein Kennwort auf dem Azure AD-Anmeldebildschirm ein.

- Ein Benutzer übergibt eine SMS MFA-Abfrage.

- Ein Benutzer gibt eine biometrische Geste zum Entsperren seines Windows-PCs mit Windows Hello for Business an.

- Ein Benutzer ist im Verbund mit Azure AD mit einer AD FS SAML-Assertion.


Zusätzlich zu den Standardfeldern werden im Protokoll zu interaktiven Anmeldungen auch folgende Informationen angezeigt: 

- Der Standort der Anmeldung

- Ob bedingter Zugriff angewendet wurde



Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

![Spalten für interaktive Benutzeranmeldungen](./media/concept-all-sign-ins/columns-interactive.png "Spalten für interaktive Benutzeranmeldungen")





Durch Anpassen der Ansicht können Sie weitere Felder anzeigen oder Felder entfernen, die bereits angezeigt werden.

![Alle interaktiven Spalten](./media/concept-all-sign-ins/all-interactive-columns.png)


Wählen Sie ein Element in der Listenansicht aus, um ausführlichere Informationen zur zugehörigen Anmeldung zu erhalten.

![Anmeldeaktivität](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Interaktive Benutzeranmeldungen")



### <a name="non-interactive-user-sign-ins"></a>Nicht interaktive Benutzeranmeldungen

Nicht interaktive Benutzeranmeldungen sind Anmeldungen, die von einer Client-App oder von Betriebssystemkomponenten im Namen eines Benutzers vorgenommen wurden. Wie bei interaktiven Benutzeranmeldungen erfolgen diese Anmeldungen im Namen eines Benutzers. Anders als bei interaktiven Benutzeranmeldungen muss der Benutzer bei diesen Anmeldungen aber keinen Authentifizierungsfaktor angeben. Stattdessen verwendet das Gerät oder die Client-App ein Token oder einen Code für die Authentifizierung oder den Zugriff einer Ressource im Namen eines Benutzers. Generell wird der Benutzer diese Anmeldungen als Geschehen im Hintergrund seiner Aktivität wahrnehmen.


**Berichtsgröße:** Groß <br>
**Beispiele:** 

- Eine Client-App verwendet ein OAuth 2.0-Aktualisierungstoken zum Abrufen eines Zugriffstokens.

- Ein Client verwendet einen OAuth 2.0-Autorisierungscode zum Abrufen eines Zugriffstokens und Aktualisierungstokens.

- Ein Benutzer führt einmaliges Anmelden (Single Sign-On, SSO) bei einer Web- oder Windows-App auf einem in Azure AD beigetretenen PC aus (ohne dass ein Authentifizierungsfaktor bereitgestellt oder mit einer Azure AD-Eingabeaufforderung interagiert wird).

- Ein Benutzer meldet sich bei einer zweiten Microsoft Office-App an, während er auf einem mobilen Gerät mit FOCI (Family of Client IDs) an einer Sitzung teilnimmt.




Zusätzlich zu den Standardfeldern werden im Protokoll zu nicht interaktiven Anmeldungen auch folgende Informationen angezeigt: 

- Ressourcen-ID

- Anzahl von gruppierten Anmeldungen




Sie können die in diesem Bericht gezeigten Felder nicht anpassen.


![Deaktivierte Spalten](./media/concept-all-sign-ins/disabled-columns.png "Deaktivierte Spalten")

Um die Verarbeitung der Daten zu vereinfachen, werden nicht interaktive Anmeldeereignisse gruppiert. Clients erstellen oft innerhalb eines kurzen Zeitraums viele nicht interaktive Anmeldungen im Namen desselben Benutzers, die alle dieselben Merkmale haben – außer der Uhrzeit, zu der die Anmeldung versucht wurde. Beispielsweise kann ein Client im Namen eines Benutzers einmal pro Stunde ein Zugriffstoken abrufen. Wenn der Benutzer oder Client den Status nicht ändert, sind die IP-Adresse, die Ressource und alle anderen Informationen bei jeder Zugriffstokenanforderung identisch. Wenn Azure AD mehrere Anmeldungen protokolliert, die – bis auf Uhrzeit und Datum – identisch sind, werden diese Anmeldungen aus derselben Entität in eine einzige Zeile aggregiert. In einer Zeile mit mehreren identischen Anmeldungen (mit Ausnahme von Datum und Uhrzeit der Ausstellung) enthält die Spalte „Anzahl von Anmeldungen“ einen Wert größer als  1. Sie können die Zeile erweitern, um alle unterschiedlichen Anmeldungen und deren unterschiedliche Zeitstempel anzuzeigen. Anmeldungen werden in den nicht interaktiven Benutzern aggregiert, wenn die folgenden Daten übereinstimmen:


- Application

- Benutzer

- IP-Adresse

- Status

- Ressourcen-ID


Sie können:

- Einen Knoten erweitern, um die einzelnen Elemente einer Gruppe anzuzeigen  

- Auf ein einzelnes Element klicken, um alle Details anzuzeigen 


![Details zur nicht interaktiven Benutzeranmeldung](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Dienstprinzipalanmeldungen

Anders als interaktive und nicht interaktive Benutzeranmeldungen enthalten Dienstprinzipalanmeldungen keinen Benutzer. Stattdessen sind dies Anmeldungen durch ein beliebiges Nichtbenutzerkonto, z. B. Apps oder Dienstprinzipale (außer bei der Anmeldung von verwalteten Identitäten, die nur im Protokoll zu Anmeldungen verwalteter Identitäten enthalten sind). Bei diesen Anmeldungen stellt die App oder der Dienst eigene Anmeldeinformationen bereit, z. B. ein Zertifikat oder ein App-Geheimnis für die Authentifizierung oder den Zugriff auf Ressourcen.


**Berichtsgröße:** Groß <br>
**Beispiele:**

- Ein Dienstprinzipal verwendet ein Zertifikat zur Authentifizierung von Microsoft Graph und für den Zugriff darauf. 

- Eine Anwendung verwendet ein Clientgeheimnis zur Authentifizierung im Flow für OAuth-Clientanmeldeinformationen. 


Dieser Bericht enthält eine Standardlistenansicht mit folgenden Informationen:

- Sign-in date (Anmeldedatum)

- Anfrage-ID

- Name oder ID des Dienstprinzipals

- Status

- IP-Adresse

- Ressourcenname

- Ressourcen-ID

- Anzahl von Anmeldungen

Sie können die in diesem Bericht gezeigten Felder nicht anpassen.

![Deaktivierte Spalten](./media/concept-all-sign-ins/disabled-columns.png "Deaktivierte Spalten")

Um die Verarbeitung der Daten in den Anmeldeprotokollen des Dienstprinzipals zu vereinfachen, werden dessen Anmeldeereignisse gruppiert. Anmeldungen aus derselben Entität unter denselben Bedingungen werden in eine einzige Zeile aggregiert. Sie können die Zeile erweitern, um alle unterschiedlichen Anmeldungen und deren unterschiedliche Zeitstempel anzuzeigen. Anmeldungen werden im Dienstprinzipalbericht aggregiert, wenn die folgenden Daten übereinstimmen:

- Name oder ID des Dienstprinzipals

- Status

- IP-Adresse

- Name oder ID der Ressource

Sie können:

- Einen Knoten erweitern, um die einzelnen Elemente einer Gruppe anzuzeigen  

- Auf ein einzelnes Element klicken, um alle Details anzuzeigen 


![Spaltendetails](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Spaltendetails")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Anmeldungen mit verwalteter Identität für Azure-Ressourcen 

Anmeldungen mit verwalteter Identität für Azure-Ressourcen sind Anmeldungen, die durch Ressourcen vorgenommen wurden, deren Geheimnisse von Azure verwaltet werden. Auf diese Weise wird die Verwaltung von Anmeldeinformationen vereinfacht.

**Berichtsgröße:** Klein <br> 
**Beispiele:**

Ein virtueller Computer mit verwalteten Anmeldeinformationen verwendet Azure AD zum Abrufen eines Zugriffstokens.   


Dieser Bericht enthält eine Standardlistenansicht mit folgenden Informationen:


- ID der verwalteten Identität

- Name der verwalteten Identität

- Resource

- Ressourcen-ID

- Anzahl von gruppierten Anmeldungen

Sie können die in diesem Bericht gezeigten Felder nicht anpassen.

Um die Verarbeitung der Daten zu vereinfachen, werden verwaltete Identitäten für Anmeldeprotokolle zu Azure-Ressourcen und nicht interaktive Anmeldeereignisse gruppiert. Anmeldungen aus derselben Entität werden in eine einzige Zeile aggregiert. Sie können die Zeile erweitern, um alle unterschiedlichen Anmeldungen und deren unterschiedliche Zeitstempel anzuzeigen. Anmeldungen werden im Bericht zu verwalteten Identitäten aggregiert, wenn alle folgenden Daten übereinstimmen:

- Name oder ID der verwalteten Identität

- Status

- Name oder ID der Ressource

Wählen Sie ein Element in der Listenansicht aus, um alle unter einem Knoten gruppierten Anmeldungen anzuzeigen.

Wählen Sie ein gruppiertes Element aus, um alle Details der Anmeldung anzuzeigen. 


## <a name="sign-in-error-code"></a>Anmeldefehler

Wenn bei der Anmeldung ein Fehler aufgetreten ist, erhalten Sie weitere Informationen zur Ursache im Abschnitt **Grundlegende Informationen** des zugehörigen Protokollelements. 

![Screenshot: Ansicht mit ausführlichen Informationen](./media/concept-all-sign-ins/error-code.png)
 
Während das Protokollelement einen Fehlergrund bereitstellt, gibt es Fälle, in denen Sie möglicherweise weitere Informationen mit dem Tool für die [Fehlersuche bei der Anmeldung](https://login.microsoftonline.com/error) erhalten. Wenn verfügbar, stellt dieses Tool beispielsweise Schritte zur Problembehebung bereit.  

![Fehlercode-Suchtool](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Filtern von Anmeldeaktivitäten

Durch Festlegen eines Filters können Sie den Bereich der zurückgegebenen Anmeldedaten eingrenzen. Azure AD bietet eine breite Palette an zusätzlichen Filtern, die Sie festlegen können. Beim Festlegen eines Filters sollten Sie immer besonders auf Ihren konfigurierten Filter **Datumsbereich** achten. Durch einen richtigen Datumsbereichsfilter wird sichergestellt, dass Azure AD nur die für Sie wirklich relevanten Daten zurückgibt.     

Mit dem Filter **Datumsbereich** können Sie einen Zeitrahmen für die zurückgegebenen Daten festlegen.
Mögliche Werte:

- Einen Monat

- Sieben Tage

- Vierundzwanzig Stunden

- Benutzerdefiniert

![Datumsbereichsfilter](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtern von Benutzeranmeldungen

Der Filter für interaktive und nicht interaktive Anmeldungen ist identisch. Daher wird der Filter, den Sie für interaktive Anmeldungen konfiguriert haben, für nicht interaktive Anmeldungen beibehalten und umgekehrt. 






## <a name="access-the-new-sign-in-activity-logs"></a>Zugreifen auf die neuen Protokolle zu Anmeldeaktivitäten 

Der Bericht zu Anmeldeaktivitäten im Azure-Portal bietet Ihnen eine einfache Methode zum Aktivieren und Deaktivieren der Berichtsvorschau. Wenn Sie die Vorschauprotokolle aktiviert haben, wird ein neues Menü angezeigt, über das Sie auf alle Arten von Berichten zu Anmeldeaktivitäten zugreifen können.     


So greifen Sie auf die neuen Anmeldeprotokolle mit nicht interaktiven Anmeldungen und Anwendungsanmeldungen zu: 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.

    ![Azure AD auswählen](./media/concept-all-sign-ins/azure-services.png)

2. Klicken Sie im Abschnitt **Überwachung** auf **Anmeldungen**.

    ![Anmeldungen auswählen](./media/concept-all-sign-ins/sign-ins.png)

3. Klicken Sie auf die Leiste **Vorschau**.

    ![Neue Ansicht aktivieren](./media/concept-all-sign-ins/enable-new-preview.png)

4. Zum Zurückwechseln zur Standardansicht klicken Sie erneut auf die Leiste **Vorschau**. 

    ![Klassische Ansicht wiederherstellen](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-logs"></a>Herunterladen von Protokollen zu Anmeldeaktivitäten

Wenn Sie einen Bericht für Anmeldeaktivitäten herunterladen, gilt Folgendes:

- Sie können den Anmeldebericht als CSV- oder JSON-Datei herunterladen.

- Sie können bis zu 100.000 Datensätze herunterladen. Wenn Sie weitere Daten herunterladen möchten, verwenden Sie die Berichterstellungs-API.

- Ihr Download basiert auf der von Ihnen getroffenen Filterauswahl.

- Die Anzahl von Datensätzen, die Sie herunterladen können, ist durch die [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md) eingeschränkt. 


![Herunterladen von Protokollen](./media/concept-all-sign-ins/download-reports.png "Herunterladen von Protokollen")


Jeder CSV-Download besteht aus sechs verschiedenen Dateien:

- Interaktive Anmeldungen

- Authentifizierungsdetails der interaktiven Anmeldungen

- Nicht interaktive Anmeldungen

- Authentifizierungsdetails der nicht interaktiven Anmeldungen

- Dienstprinzipalanmeldungen

- Anmeldungen mit verwalteter Identität für Azure-Ressourcen

Jeder JSON-Download besteht aus vier verschiedenen Dateien:

- Interaktive Anmeldungen (einschließlich Authentifizierungsdetails)

- Nicht interaktive Anmeldungen (einschließlich Authentifizierungsdetails)

- Dienstprinzipalanmeldungen

- Anmeldungen mit verwalteter Identität für Azure-Ressourcen

![Herunterladen von Dateien](./media/concept-all-sign-ins/download-files.png "Herunterladen von Dateien")


## <a name="return-log-data-with-microsoft-graph"></a>Zurückgeben von Protokolldaten mit Microsoft Graph

Zusätzlich zur Verwendung des Azure-Portals können Sie Anmeldeprotokolle mithilfe der Microsoft Graph-API abfragen, um verschiedene Arten von Anmeldeinformationen zurückzugeben. Um potenzielle Leistungsprobleme zu vermeiden, sollten Sie ihre Abfrage auf die Daten beschränken, mit denen Sie arbeiten. 

Im folgenden Beispiel wird der Bereich der Abfrage durch die Anzahl der Datensätze, einen bestimmten Zeitraum und den Typ des Anmeldeereignisses beschränkt:

```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/signIns?$top=100&$filter=createdDateTime ge 2020-09-10T06:00:00Z and createdDateTime le 2020-09-17T06:00:00Z and signInEventTypes/any(t: t eq 'nonInteractiveUser')
```

Die Abfrageparameter im Beispiel liefern die folgenden Ergebnisse:

- Der [$top](/graph/query-parameters#top-parameter)-Parameter gibt die ersten 100 Ergebnisse zurück.
- Der [$filter](/graph/query-parameters#filter-parameter)-Parameter schränkt den Zeitrahmen für die Rückgabe von Ergebnissen ein und verwendet die signInEventTypes-Eigenschaft, um nur nicht interaktive Benutzeranmeldungen zurückzugeben.

Die folgenden Werte stehen zum Filtern nach verschiedenen Anmeldetypen zur Verfügung: 

- interactiveUser
- nonInteractiveUser
- servicePrincipal 
- managedIdentity

## <a name="next-steps"></a>Nächste Schritte

* [Fehlercodes des Berichts mit den Anmeldeaktivitäten](./concept-sign-ins.md)
* [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md)
* [Latenzen bei Azure Active Directory-Berichten](reference-reports-latencies.md)
