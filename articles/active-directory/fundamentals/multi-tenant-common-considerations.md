---
title: Allgemeine Überlegungen zur mehrinstanzenfähigen Benutzerverwaltung in Azure Active Directory
description: Erfahren Sie mehr über die allgemeinen Entwurfsüberlegungen für den Benutzerzugriff in Azure Active Directory-Mandanten mit Gastkonten.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a93eb869cd3135f548331a82de9593497829c0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270449"
---
# <a name="common-considerations-for-multi-tenant-user-management"></a>Allgemeine Überlegungen zur mehrinstanzenfähigen Benutzerverwaltung

Es gibt viele Überlegungen, die für mehrere Zusammenarbeitsmuster relevant sind. 

## <a name="directory-object-considerations"></a>Überlegungen zu Verzeichnisobjekten

Sie können die Konsole verwenden, um manuell eine Einladung für ein Gastbenutzerkonto zu erstellen. In diesem Fall wird das Benutzerobjekt mit dem Benutzertyp *Gast* erstellt. Mit anderen Verfahren zum Erstellen von Einladungen können Sie den Benutzertyp auf ein anderes als ein Gastkonto festlegen. Wenn Sie beispielsweise die API verwenden, können Sie konfigurieren, ob es sich um ein Mitgliedskonto oder ein Gastkonto handelt.  

* Einige der [Einschränkungen für Gastfunktionen können entfernt werden](../external-identities/user-properties.md#remove-guest-user-limitations).

* [Sie können Gastkonten in den Benutzertyp „Mitglied“ konvertieren](../external-identities/user-properties.md#can-azure-ad-b2b-users-be-added-as-members-instead-of-guests).

> **WICHTIG** Wenn Sie ein Gastkonto in ein Benutzerkonto konvertieren, können Probleme bei der Verarbeitung von B2B-Konten durch Exchange Online auftreten. Konten, die als Gastmitglieder eingeladen werden, können nicht für E-Mail aktiviert werden. Um ein E-Mail-aktiviertes Gastmitgliedskonto zu erhalten, wird folgender Ansatz empfohlen:
>* Laden Sie die organisationsübergreifenden Benutzer als Gastkonten ein.
>* Zeigen Sie die Konten in der GAL an.
>* Legen Sie „UserType“ auf „Member“ fest.

Bei diesem Ansatz werden die Konten in Exchange Online als „MailUser“ angezeigt.

### <a name="issues-with-using-mail-contact-objects-instead-of-external-users-or-members"></a>Probleme bei der Verwendung von E-Mail-Kontaktobjekten anstelle von externen Benutzern oder Mitgliedern

Sie können Benutzer aus einem anderen Mandanten mithilfe einer herkömmlichen GAL-Synchronisierung darstellen. Bei Durchführung einer GAL-Synchronisierung anstelle von Azure AD B2B Collaboration wird ein E-Mail-Kontaktobjekt erstellt. 

* Ein E-Mail-Kontaktobjekt und ein E-Mail-aktivierter Gastbenutzer (Mitglied oder Gast) können nicht gleichzeitig in demselben Mandanten mit derselben E-Mail-Adresse vorhanden sein. 

* Wenn ein E-Mail-Kontaktobjekt für dieselbe E-Mail-Adresse wie der eingeladene Gastbenutzer vorliegt, wird der Gastbenutzer erstellt, ist aber NICHT E-Mail-aktiviert. 

* Wenn der E-Mail-aktivierte Gastbenutzer mit derselben E-Mail vorhanden ist, löst ein Versuch, ein E-Mail-Kontaktobjekt zu erstellen, zum Erstellungszeitpunkt eine Ausnahme aus.

Im Folgenden werden die Ergebnisse verschiedener E-Mail-Kontaktobjekte und Gastbenutzerzustände angezeigt.

| Vorhandener Zustand| Bereitstellungsszenario| Effektives Ergebnis |
| - |-|-|
| Ohne| B2B-Mitglied einladen| Kein E-Mail-fähiger Mitgliedsbenutzer. Informationen finden Sie oben im Hinweis „Wichtig“. |
| Ohne| B2B-Gastbenutzer einladen| E-Mail-aktivierter Gastbenutzer. |
| E-Mail-Kontaktobjekt vorhanden| B2B-Mitglied einladen| Fehler: Konflikt von Proxyadressen. |
| E-Mail-Kontaktobjekt vorhanden| B2B-Gastbenutzer einladen| E-Mail-Kontakt und nicht E-Mail-aktivierter Gastbenutzer. Informationen finden Sie oben im Hinweis „Wichtig“. |
| E-Mail-fähiger B2B-Gastbenutzer| E-Mail-Kontaktobjekt erstellen| Fehler |
| E-Mail-aktivierter B2B-Mitgliedsbenutzer vorhanden| E-Mail-Kontakt erstellen| Fehler |


**Microsoft rät von der herkömmlichen GAL-Synchronisierung ab**. Verwenden Sie stattdessen Azure AD B2B Collaboration, um Folgendes zu erstellen:

* Externe Gastbenutzer, die Sie für die Anzeige in der GAL aktivieren

* Externe Mitgliedsbenutzer, die standardmäßig in der GAL angezeigt werden, aber nicht E-Mail-aktiviert sind

Einige Organisationen verwenden das E-Mail-Kontaktobjekt, um Benutzer in der GAL anzuzeigen. Bei diesem Ansatz wird eine GAL integriert, ohne weitere Berechtigungen bereitzustellen, weil E-Mail-Kontakte keine Sicherheitsprinzipale sind. 

Der folgende Ansatz eignet sich besser zum Erreichen dieses Ziels:
* Einladen von Gastbenutzern
* Anzeigen der Gastbenutzer in der GAL
* Deaktivieren der Gastbenutzer durch [Verhindern ihrer Anmeldung](/powershell/module/azuread/set-azureaduser&preserve-view=true)

Ein E-Mail-Kontaktobjekt kann nicht in ein Benutzerobjekt konvertiert werden. Daher können Eigenschaften, die einem E-Mail-Kontaktobjekt zugeordnet sind, nicht übertragen werden. Beispielsweise werden Gruppenmitgliedschaften und anderer Ressourcenzugriff nicht übertragen.  

Die Verwendung eines E-Mail-Kontaktobjekts zur Darstellung eines Benutzers beinhaltet folgende Herausforderungen.

* **Office 365-Gruppen**: Office 365-Gruppen unterstützen Richtlinien zur Regelung der Benutzertypen, die Gruppen angehören und mit Inhalten interagieren dürfen, die Gruppen zugeordnet sind. Beispielsweise lässt eine Gruppe möglicherweise nicht den Beitritt von Gastkonten zu. Diese Richtlinien können keine E-Mail-Kontaktobjekte steuern.

* **Azure AD-Self-Service-Gruppenverwaltung (SSGM)** : E-Mail-Kontaktobjekte dürfen keinen Gruppen angehören, die das SSGM-Feature verwenden. Möglicherweise sind zusätzliche Tools erforderlich, um Gruppen mit Empfängern zu verwalten, die als Kontakte statt als Benutzerobjekte dargestellt werden.

* **Azure AD Identity Governance – Zugriffsüberprüfungen**: Das Feature „Zugriffsüberprüfungen“ kann verwendet werden, um die Mitgliedschaft in Office 365-Gruppen zu überprüfen und zu nachzuweisen. Zugriffsüberprüfungen basieren auf Benutzerobjekten. Mitglieder, die durch E-Mail-Kontaktobjekte dargestellt werden, liegen außerhalb des Gültigkeitsbereichs von Zugriffsüberprüfungen.

* **Azure AD Identity Governance – Berechtigungsverwaltung**: Bei Verwendung der Berechtigungsverwaltung zum Aktivieren von Self-Service-Zugriffsanforderungen für externe Benutzer über das Berechtigungsverwaltungsportal des Unternehmens wird zum Zeitpunkt der Anforderung ein Benutzerobjekt erstellt. E-Mail-Kontaktobjekte werden nicht unterstützt. 

## <a name="azure-ad-conditional-access-considerations"></a>Überlegungen zum bedingten Zugriff in Azure AD

Der Zustand des Benutzers, Geräts oder Netzwerks im Stammmandanten des Benutzers wird nicht an den Ressourcenmandanten übermittelt. Daher erfüllt ein Gastbenutzerkonto möglicherweise keine Richtlinien für bedingten Zugriff, die die folgenden Kontrollen verwenden.

* **Mehrstufige Authentifizierung erforderlich**: Gastbenutzer müssen MFA auch dann im Ressourcenmandanten registrieren bzw. darauf reagieren, wenn die MFA im Basismandanten erfüllt wurde. Dies führt zu mehreren MFA-Abfragen. Wenn Gastbenutzer ihre MFA-Nachweise zurücksetzen müssen, sind ihnen möglicherweise die mehrfachen mandantenübergreifenden MFA-Nachweisregistrierungen nicht bekannt. Aus diesem Grund muss sich der Benutzer möglicherweise an einen Administrator im Basismandanten, Ressourcenmandanten oder in beiden Mandanten wenden.

* **Markieren des Geräts als kompatibel erforderlich**: Die Geräteidentität ist nicht im Ressourcenmandanten registriert, sodass der Gastbenutzer nicht auf Ressourcen zugreifen kann, die diese Kontrolle erfordern.

* **Gerät mit Hybrid-Azure AD-Einbindung erforderlich**: Die Geräteidentität ist nicht im Ressourcenmandanten registriert (oder in der lokalen Active Directory-Instanz, die mit dem Ressourcenmandanten verbunden ist), sodass der Gastbenutzer nicht auf Ressourcen zugreifen kann, die diese Kontrolle erfordern.

* **Genehmigte Client-App erforderlich oder App-Schutzrichtlinie erforderlich**: Externe Gastbenutzer können die Intune-MAM-Richtlinie (Mobile App Management, Verwaltung mobiler Anwendungen) des Ressourcenmandanten nicht anwenden, weil dafür auch eine Geräteregistrierung erforderlich ist. Die Ressourcenmandantenrichtlinie für bedingten Zugriff, die diese Kontrolle verwendet, lässt nicht zu, dass der MAM-Schutz des Basismandanten die Richtlinie erfüllt. Externe Gastbenutzer sollten von allen MAM-basierten Richtlinien für bedingten Zugriff ausgeschlossen werden. 

Die folgenden Bedingungen für bedingten Zugriff können zwar verwendet werden, aber beachten Sie die möglichen Auswirkungen.

* **Anmelderisiko und Benutzerrisiko**: Das Anmelderisiko und das Benutzerrisiko werden teilweise durch das Benutzerverhalten im Basismandanten bestimmt. Die Daten und die Risikobewertung werden im Basismandanten gespeichert.  
‎Wenn die Richtlinien eines Ressourcenmandanten einen Gastbenutzer blockieren, kann ein Ressourcenmandantenadministrator den Zugriff möglicherweise nicht aktivieren. Weitere Informationen finden Sie unter [Identity Protection und B2B-Benutzer](../identity-protection/concept-identity-protection-b2b.md).

* **Standorte**: Die im Ressourcenmandanten festgelegten Definitionen benannter Standorte werden zum Bestimmen des Richtlinienbereichs verwendet. Im Basismandanten verwaltete vertrauenswürdige Standorte werden im Rahmen der Richtlinie nicht ausgewertet. Es gibt Szenarien, in denen Organisationen vertrauenswürdige Standorte mandantenübergreifend freigeben möchten. Um vertrauenswürdige Standorte gemeinsam zu nutzen, müssen die Standorte in jedem Mandanten definiert werden, in dem die Ressourcen und Richtlinien für bedingten Zugriff definiert sind.

## <a name="other-access-control-considerations"></a>Weitere Überlegungen zur Zugriffssteuerung

Hier sind einige weitere Überlegungen zum Konfigurieren der Zugriffssteuerung aufgeführt.
* Definieren Sie [Zugriffssteuerungsrichtlinien](../external-identities/conditional-access.md) zum Steuern des Zugriffs auf Ressourcen.
* Entwerfen Sie Richtlinien für bedingten Zugriff unter Berücksichtigung von Gastbenutzern. 
* Erstellen Sie Richtlinien speziell für Gastbenutzer. 
* Wenn Ihre Organisation die Bedingung [Alle Benutzer] in Ihrer vorhandenen Zertifizierungsstellenrichtlinie verwendet, wirkt sich diese Richtlinie auf Gastbenutzer aus, weil [Gast]-Benutzer im Bereich [Alle Benutzer] enthalten sind.
* Erstellen Sie dedizierte Richtlinien für bedingten Zugriff für [Gast]-Konten. 

Informationen zum Schützen dynamischer Gruppen, die den Ausdruck [Alle Benutzer] verwenden, finden Sie unter [Dynamische Gruppen und Azure AD B2B Collaboration](../external-identities/use-dynamic-groups.md).

### <a name="require-user-assignment"></a>Anfordern der Benutzerzuweisung

Wenn für eine Anwendung die Eigenschaft [Benutzerzuweisung erforderlich?] auf [Nein] festgelegt ist, können Gastbenutzer auf die Anwendung zugreifen. Anwendungsadministratoren müssen die Auswirkungen der Zugriffssteuerung kennen, insbesondere dann, wenn die Anwendung vertrauliche Informationen enthält. Weitere Informationen finden Sie unter [Einschränken Ihrer Azure AD-App auf eine Gruppe von Benutzern](../develop/howto-restrict-your-app-to-a-set-of-users.md). 

### <a name="terms-and-conditions"></a>Geschäftsbedingungen

Die [Nutzungsbedingungen für Azure Active Directory](../conditional-access/terms-of-use.md) bieten Organisationen eine einfache Möglichkeit, Informationen für Endbenutzer anzuzeigen. So können Sie von Gastbenutzern die Bestätigung von Nutzungsbedingungen einfordern, bevor diese auf Ihre Ressourcen zugreifen.

### <a name="licensing-considerations-for-guest-users-with-azure-ad-premium-features"></a>Lizenzierungsüberlegungen für Gastbenutzer mit Azure AD Premium-Features

Preise für Azure AD External Identities (Gastbenutzer) basieren auf monatlich aktiven Benutzern (MAU). Die Angabe der aktiven Benutzer entspricht der Anzahl eindeutiger Benutzer mit Authentifizierungsaktivität innerhalb eines Kalendermonats. Das MAU-Abrechnungsmodell hilft Ihnen, Ihre Kosten zu senken, da es einen kostenlosen Tarif sowie eine flexible, vorhersehbare Preisgestaltung bietet. Zudem sind Ihre ersten 50.000 MAUs pro Monat sowohl für Premium P1- als auch für Premium P2-Features kostenlos. Premium-Features umfassen Richtlinien für bedingten Zugriff und Azure MFA für Gastbenutzer.

Weitere Informationen finden Sie unter [MAU-Abrechnungsmodell für Azure AD External Identities](../external-identities/external-identities-pricing.md).

## <a name="office-365-considerations"></a>Überlegungen zu Office 365

Die folgenden Informationen gelten für Office 365 im Kontext der Szenarien in diesem Artikel. Ausführliche Informationen finden Sie unter [Mandantenübergreifende Zusammenarbeit in Office 365](/office365/enterprise/office-365-inter-tenant-collaboration).

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

Exchange Online schränkt bestimmte Funktionen für Gastbenutzer ein. Die Einschränkungen können durch das Erstellen externer Mitglieder anstelle externer Gäste verringert werden. Allerdings wird für externe Benutzer derzeit keine der folgenden Funktionen unterstützt. 

* Einem Gastbenutzer kann eine Exchange Online-Lizenz zugewiesen werden. Allerdings kann ihm kein Token für Exchange Online ausgestellt werden. Demzufolge kann er nicht auf die Ressource zugreifen.

   * Gastbenutzer können keine freigegebenen oder delegierten Exchange Online-Postfächer im Ressourcen-Mandanten verwenden.

   * Ein Gastbenutzer kann einem freigegebenen Postfach zugewiesen werden, aber nicht darauf zugreifen.

* Gastbenutzer müssen eingeblendet werden, damit sie in die GAL aufgenommen werden. Standardmäßig sind sie ausgeblendet.

   * Ausgeblendete Gastbenutzer werden zum Zeitpunkt der Einladung erstellt. Die Erstellung ist unabhängig davon, ob der Benutzer seine Einladung eingelöst hat. Wenn also alle Gastbenutzer eingeblendet werden, enthält die Liste Benutzerobjekte von Gastbenutzern, die ihre Einladung nicht eingelöst haben. Je nach Szenario empfiehlt es sich, die Objekte aufzulisten oder ausgeblendet zu lassen.

   * Gastbenutzer können nur mithilfe von [Exchange Online-PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true) eingeblendet werden. Sie können das PowerShell-Cmdlet [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) ausführen, um die Eigenschaft „HiddenFromAddressListsEnabled“ auf den Wert $false festzulegen.  
‎  
`‎Set-MailUser [GuestUserUPN] -HiddenFromAddressListsEnabled:$false` ‎  
‎Dabei entspricht [GuestUserUPN] dem berechneten UserPrincipalName. Beispiel:  
‎  
`‎Set-MailUser guestuser1_contoso.com#EXT#@fabricam.onmicrosoft.com -HiddenFromAddressListsEnabled:$false`

* Aktualisierungen an Exchange-spezifischen Eigenschaften wie PrimarySmtpAddress, ExternalEmailAddress, EmailAddresses und MailTip können nur mithilfe von [Exchange Online-PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true) festgelegt werden. Im Exchange Online Admin Center können Sie die Attribute nicht mithilfe der grafischen Benutzeroberfläche ändern. 

Wie oben gezeigt, können Sie das PowerShell-Cmdlet [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) für E-Mail-spezifische Eigenschaften verwenden. Viele zusätzliche Benutzereigenschaften können Sie mit dem PowerShell-Cmdlet [Set-User](/powershell/module/exchange/set-user?view=exchange-ps&preserve-view=true) ändern. Die meisten Eigenschaften können auch über die Azure AD-Graph-APIs geändert werden.

### <a name="microsoft-sharepoint-online"></a>Microsoft SharePoint Online

SharePoint Online verfügt über eigene dienstspezifische Berechtigungen, je nachdem, ob der Benutzer Mitglied oder Gast im Azure Active Directory-Mandanten ist. 

Weitere Informationen hierzu finden Sie unter [Externe Office 365-Freigaben und Azure Active Directory B2B Collaboration](../external-identities/o365-external-user.md).

Nach dem Aktivieren der externen Freigabe in SharePoint Online ist die Option, in der SharePoint Online-Personenauswahl (SPO) nach Gastbenutzern zu suchen, standardmäßig deaktiviert. Diese Einstellung verhindert, dass Gastbenutzer auffindbar sind, wenn sie aus der Exchange Online-GAL ausgeblendet werden. Sie können Gastbenutzer auf zwei Arten sichtbar machen (die sich nicht gegenseitig ausschließen):

* Sie können die Möglichkeit zum Suchen nach Gastbenutzern auf verschiedene Weise aktivieren:
   * Ändern Sie die Einstellung „ShowPeoplePickerSuggestionsForGuestUsers“ auf Mandanten- und Websitesammlungsebene. 
   * Legen Sie das Feature mithilfe der Cmdlets [Set-SPOTenant](/powershell/module/sharepoint-online/Set-SPOTenant?view=sharepoint-ps&preserve-view=true) und [Set-SPOSite](/powershell/module/sharepoint-online/set-sposite?view=sharepoint-ps&preserve-view=true) in [SharePoint Online-PowerShell](/powershell/sharepoint/sharepoint-online/connect-sharepoint-online?view=sharepoint-ps&preserve-view=true) fest.  
‎

* Gastbenutzer, die in der Exchange Online-GAL sichtbar sind, werden auch in der SharePoint Online-Personenauswahl aufgeführt. Die Konten sind unabhängig von der Einstellung für „ShowPeoplePickerSuggestionsForGuestUsers“ sichtbar.

### <a name="microsoft-teams"></a>Microsoft Teams

Microsoft Teams verfügt über Features zum Einschränken des Zugriffs basierend auf dem Benutzertyp. Änderungen am Benutzertyp können sich auf den Inhaltszugriff und die verfügbaren Features auswirken. 

* Für den Mechanismus zum Mandantenwechsel für Microsoft Teams ist es möglicherweise erforderlich, dass Benutzer den Kontext ihres Teams-Clients manuell wechseln, wenn sie in Teams außerhalb ihres eigenen Mandanten arbeiten.

* Mithilfe eines Teams-Verbunds können Sie Teams-Benutzern aus einer anderen externen Domäne die Möglichkeit geben, Ihre Benutzern zu suchen, anzurufen, mit ihnen zu chatten und Besprechungen einzurichten. Weitere Informationen hierzu finden Sie unter [Verwalten des externen Zugriffs in Microsoft Teams](/microsoftteams/manage-external-access). 

 

### <a name="licensing-considerations-for-guest-users-in-teams"></a>Lizenzierungsüberlegungen für Gastbenutzer in Teams

Bei Verwendung von Azure B2B mit Office 365-Workloads sind einige wichtige Aspekte zu beachten. Es gibt Fälle, in denen Gastkonten nicht dieselbe Funktionalität wie ein Mitgliedskonto aufweisen. 

**Microsoft-Gruppen**. Weitere Informationen zur Gastkontofunktionalität in Microsoft-Gruppen finden Sie unter [Hinzufügen von Gästen zu Office 365-Gruppen](https://support.office.com/article/adding-guests-to-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6). 

**Microsoft Teams**. Weitere Informationen zur Gastkontofunktionalität in Microsoft Teams finden Sie unter [Funktionen für Teambesitzer, Mitglieder und Gäste in Teams](https://support.office.com/article/team-owner-member-and-guest-capabilities-in-teams-d03fdf5b-1a6e-48e4-8e07-b13e1350ec7b?ui=en-US&rs=en-US&ad=US). 

Sie können mithilfe externer B2B-Mitglieder eine originaltreue Teams-Oberfläche aktivieren. Für Office 365 wurde vor Kurzem die zugehörige Lizenzierungsrichtlinie für Organisationen mit mehreren Mandanten geklärt.

* Benutzer, die in ihrem Basismandanten lizenziert sind, können auf Ressourcen in einem anderen Mandanten innerhalb derselben juristischen Person zugreifen. Der Zugriff wird ohne zusätzliche Lizenzgebühren über die Einstellung **Externe Mitglieder** gewährt. Die Einstellung gilt für SharePoint, OneDrive for Business, Teams und Gruppen. 

   * Es werden Entwicklungsarbeiten ausgeführt, um den Lizenzstatus eines Benutzers in seinem Basismandanten automatisch zu überprüfen und die Teilnahme als Mitglied ohne zusätzliche Lizenzzuweisung oder Konfiguration zu ermöglichen. Für Kunden, die externe Mitglieder sofort verwenden möchten, gibt es jedoch eine Problemumgehung zur Lizenzierung, für die sich der Kundenbetreuer an den Microsoft Business Desk wenden muss. 

   * Bis zur Aktivierung der entwickelten Lizenzierungslösung können Kunden ab jetzt eine *Teams-Testlizenz* verwenden. Die Lizenz kann jedem Benutzer im fremden Mandanten zugewiesen werden. Die Lizenz hat eine Dauer von einem Jahr und ermöglicht alle oben aufgeführten Workloads.

   * Für Kunden, die B2B-Gäste in B2B-Mitglieder konvertieren möchten, gibt es mehrere bekannte Probleme mit Microsoft Teams. Beispielsweise können keine neuen Kanäle erstellt werden, und Anwendungen können einem vorhandenen Team hinzugefügt werden. 

* **Identity Governance**-Features (Berechtigungsverwaltung, Zugriffsüberprüfungen) erfordern möglicherweise andere Lizenzen für Gastbenutzer oder externe Mitglieder. Arbeiten Sie mit dem Kundenteam oder dem Business Desk zusammen, um die richtige Antwort für Ihre Organisation zu erhalten.

**Andere Produkte** (z. B. Dynamics CRM) erfordern möglicherweise eine Lizenzierung in jedem Mandanten, in dem ein Benutzer dargestellt wird. Arbeiten Sie mit Ihrem Kundenteam zusammen, um die richtige Antwort für Ihre Organisation zu erhalten.

## <a name="next-steps"></a>Nächste Schritte
[Einführung in die mehrstufige Benutzerverwaltung](multi-tenant-user-management-introduction.md)

[Szenarien für die mehrinstanzenfähige Endbenutzerverwaltung](multi-tenant-user-management-scenarios.md)

[Allgemeine Lösungen für mehrere Mandanten](multi-tenant-common-solutions.md)