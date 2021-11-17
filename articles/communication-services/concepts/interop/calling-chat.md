---
title: Interoperabilität von Teamanrufen und Chats
titleSuffix: An Azure Communication Services concept document
description: Interoperabilität von Teamanrufen und Chats
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 4bb4cf91aed5ae4784511416d3f30dd9d6a20986
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579253"
---
# <a name="teams-interoperability-calling-and-chat"></a>Teams Interoperabilität: Anrufe und Chats

> [!IMPORTANT]
> Die Interoperabilität von Anrufen und Chats befindet sich in einer privaten Vorschau und ist auf eine begrenzte Anzahl von Azure Communication Services-Frühanwendern beschränkt. Sie können [mit diesem Formular die Teilnahme an der Vorschau beantragen](https://forms.office.com/r/F3WLqPjw0D) und wir werden Ihr(e) Szenario(s) prüfen und Ihre Teilnahme an der Vorschau bewerten.
>
> Private Preview APIs und SDKs werden ohne Service-Level-Vereinbarung zur Verfügung gestellt. Sie sind nicht für die Produktion geeignet und sollten nur mit Testbenutzern und Testdaten verwendet werden. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Wenn Sie Unterstützung benötigen, Fragen haben, Feedback geben oder Probleme melden möchten, nutzen Sie bitte den [Teams Interop Ad-hoc-Anruf- und Chat-Kanal](https://teams.microsoft.com/l/channel/19%3abfc7d5e0b883455e80c9509e60f908fb%40thread.tacv2/Teams%2520Interop%2520ad%2520hoc%2520calling%2520and%2520chat?groupId=d78f76f3-4229-4262-abfb-172587b7a6bb&tenantId=72f988bf-86f1-41af-91ab-2d7cd011db47). Sie müssen ein Mitglied des Azure Communication Service TAP-Teams sein.

Im Rahmen dieser Vorschau können die Azure Communication Services SDKs verwendet werden, um Anwendungen zu erstellen, die es BYOI-Nutzern ermöglichen, 1:1-Anrufe oder 1:n-Chats mit Teams-Nutzern zu starten. [Für diese Nutzer gelten die üblichen ACS-Preise](https://azure.microsoft.com/pricing/details/communication-services/), aber für die Interoperabilitätsfunktion selbst fallen keine zusätzlichen Gebühren an.



## <a name="enabling-calling-and-chat-interoperability-in-your-teams-tenant"></a>Aktivieren der Interoperabilität von Anrufen und Chats in Ihrem Teams-Tenant
Um Anrufe und Chats zwischen Ihren Communication Services-Benutzern und Ihrem Teams-Tenant zu aktivieren, verwenden Sie das neue Teams PowerShell-Cmdlet [Set-CsTeamsAcsFederationConfiguration](/powershell/module/teams/set-csteamsacsfederationconfiguration). Dieses Cmdlet ist nur für Teilnehmer der privaten Vorschau verfügbar. 

Benutzerdefinierte Anwendungen, die mit Azure Communication Services erstellt wurden, um sich mit Teams-Benutzern zu verbinden und mit ihnen zu kommunizieren, können von Endbenutzern oder Bots verwendet werden, und es gibt keinen Unterschied in der Darstellung für Teams-Benutzer, es sei denn, der Entwickler der Anwendung hat dies ausdrücklich angegeben.

Um einen Anruf oder einen Chat mit einem Teams-Benutzer zu starten, ist die Azure Active Directory (AAD) Objekt-ID des Benutzers erforderlich. Diese kann über [Microsoft Graph API](/graph/api/resources/users) oder von Ihrem lokalen Verzeichnis bezogen werden, wenn Sie [Azure AD Connect](../../../active-directory/hybrid/how-to-connect-sync-whatis.md) (oder einen anderen Mechanismus) zur Synchronisierung zwischen Ihrem lokalen Verzeichnis und AAD verwenden.

## <a name="calling"></a>Aufrufen
Mit dem Calling SDK kann ein Communication Services-Benutzer oder -Endpunkt einen 1:1-Anruf mit Teams-Benutzern starten, die durch ihre Azure Active Directory (AAD) Objekt-ID identifiziert werden. Sie können eine bestehende Anwendung, die andere Benutzer der Kommunikationsdienste anruft, leicht ändern und stattdessen einen Teams-Benutzer anrufen.
 
[Anrufe verwalten - Eine Anleitung zu Azure Communication Services | Microsoft Docs](../../how-tos/calling-sdk/manage-calls.md?pivots=platform-web)

Aufrufen eines anderen Communication Services Endpunkts mit [communicationUserId](/javascript/api/@azure/communication-common/communicationuseridentifier?view=azure-node-latest#communicationUserId):
```js
const acsCallee = { communicationUserId: '<ACS User ID>' }
const call = callAgent.startCall([acsCallee]);
```

Aufrufen eines Teams-Benutzers mit [microsoftTeamsUserId](/javascript/api/@azure/communication-common/microsoftteamsuseridentifier?view=azure-node-latest#microsoftTeamsUserId):
```js
const teamsCallee = { microsoftTeamsUserId: '8:orgid:<Teams User AAD Object ID>' }
const call = callAgent.startCall([teamsCallee]);
```
 
**Einschränkungen und bekannte Probleme**
- Teams-Benutzer müssen sich im Modus "TeamsOnly" befinden. Skype for Business-Nutzer können keine 1:1-Anrufe von Nutzern der Kommunikationsdienste entgegennehmen.
- Die Eskalation zu einem Gruppenruf wird nicht unterstützt.
- Die Gesprächsaufzeichnung der Kommunikationsdienste ist nicht für 1:1-Gespräche verfügbar.
- Erweiterte Anrufweiterleitungsfunktionen wie Anrufweiterleitung, Gruppenrufübernahme, Simulierung und Voicemail werden nicht unterstützt.
- Teams-Benutzer können keine Kommunikationsdienste-Benutzer als Weiterleitungs-/Übertragungsziele festlegen.
- Es gibt eine Reihe von Features im Teams-Client, die bei 1:1-Aufrufen mit Communication Services-Benutzern nicht wie erwartet funktionieren.
- [Drittanbietergeräte für Teams](/MicrosoftTeams/devices/teams-ip-phones) und [Skype IP-Telefone](/skypeforbusiness/certification/devices-ip-phones) werden nicht unterstützt.

## <a name="chat"></a>Chat
Mit dem Chat SDK können Communication Services-Benutzer oder -Endpunkte einen 1:n-Chat mit Teams-Benutzern starten, die durch ihre Azure Active Directory (AAD) Objekt-ID identifiziert werden. Sie können eine bestehende Anwendung, die Chats mit anderen Nutzern von Kommunikationsdiensten erstellt, leicht ändern, um stattdessen Chats mit Nutzern von Teams zu erstellen:
                                            
[Schnellstart: Hinzufügen von Chatfunktionen zu Ihrer App](../../quickstarts/chat/get-started.md?pivots=programming-language-javascript)

Erstellen eines Chats mit einem Teams-Benutzer:
```js
async function createChatThread() { 
const createChatThreadRequest = {  topic: "Hello, World!"  }; 
const createChatThreadOptions = {
    participants: [ { 
        id: { microsoftTeamsUserId: '<TEAMS_USER_ID>' }, 
        displayName: '<USER_DISPLAY_NAME>' }
    ] }; 
const createChatThreadResult = await chatClient.createChatThread( 
createChatThreadRequest, createChatThreadOptions ); 
const threadId = createChatThreadResult.chatThread.id; return threadId; }
```                                         

**Unterstützte Funktionen**
-   Senden/Empfangen von Nachrichten (Typ: Text, Rich Text, Emoticons) 
-   Benutzer der Kommunikationsdienste können gesendete Nachrichten bearbeiten
-   Gesendete Nachrichten löschen
-   Empfang von Echtzeit-Benachrichtigungen (Thread- und Nachrichten-bezogene Ereignisse werden derzeit von ACS unterstützt)
-   Senden und empfangen Tippen von Indikatoren
-   Senden und Empfangen von Lesebestätigungen
-   Teilnehmer hinzufügen und Nachrichtenverlauf teilen: Teams-Benutzer können nur Teams-Benutzer hinzufügen. Benutzer der Kommunikationsdienste können Teams und Benutzer der Kommunikationsdienste hinzufügen.
-   Vorhandenen Teilnehmer aus dem Chat entfernen
-   Chat verlassen
-   Chat-Thema aktualisieren
-   Der Benutzer der Kommunikationsdienste kann den Chat löschen.


**Einschränkungen und bekannte Probleme**
- Die Bearbeitung von Nachrichten durch den Teams-Benutzer schlägt fehl.
- Durch das Löschen eines Threads durch den Benutzer der Kommunikationsdienste wird der Nachrichtenverlauf für den Teams-Benutzer gelöscht und der Teams-Benutzer wird aus dem Thread entfernt.
- Die Teams-Client-Benutzeroberfläche für externe Benutzer ist inkonsistent.
- Mit dem Teams-Client kann kein Gespräch mit den Chat-Teilnehmern geführt werden


## <a name="privacy"></a>Datenschutz
Die Interoperabilität zwischen Azure Communication Services und Microsoft Teams ermöglicht Ihren Anwendungen und Benutzern, an Teams-Anrufen, -Besprechungen und -Chats teilzunehmen. Sie sind dafür verantwortlich, dass die Benutzer Ihrer Anwendung benachrichtigt werden, wenn die Aufzeichnung oder Transkription in einem Teams-Anruf oder einer Teams-Besprechung aktiviert ist.

Microsoft zeigt Ihnen über die API der Azure-Kommunikationsdienste an, dass die Aufzeichnung oder Transkription begonnen hat, und Sie müssen dies Ihren Benutzern in der Benutzeroberfläche Ihrer Anwendung in Echtzeit mitteilen. Sie erklären sich damit einverstanden, Microsoft für alle Kosten und Schäden freizustellen, die aufgrund der Nichteinhaltung dieser Verpflichtung durch Sie entstehen.
