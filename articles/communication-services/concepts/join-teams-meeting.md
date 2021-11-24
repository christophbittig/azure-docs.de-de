---
title: Interoperabilität von Teams-Besprechungen
titleSuffix: An Azure Communication Services concept document
description: Beitreten zu Teams-Besprechungen
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 74a39ff953c1ef8e549dbace0c7fc495976fae2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325522"
---
# <a name="join-a-teams-meeting"></a>Teilnehmen an einer Teams-Besprechung

> [!IMPORTANT]
> Die BYOI-Interoperabilität befindet sich in der Public Preview-Phase und ist für alle Communication Services-Anwendungen und Teams-Organisationen verfügbar.
>
> Preview-APIs und -SDKs werden ohne Vereinbarung zum Servicelevel bereitgestellt und sind nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mithilfe von Azure Communication Services können Sie Anwendungen erstellen, mit denen Benutzer Teams-Besprechungen beitreten und daran teilnehmen können. Für diese Benutzer gelten die [ACS-Standardpreise](https://azure.microsoft.com/pricing/details/communication-services/), es fallen jedoch keine zusätzlichen Gebühren für die Interoperabilitätsfunktion selbst an. Mit dem BYOI-Modell (Bring Your Own Identity) steuern Sie die Benutzerauthentifizierung, und Benutzer Ihrer Anwendungen benötigen für die Teilnahme an Teams-Besprechungen keine Teams-Lizenzen. Dieses Modell eignet sich ideal für B2C-Lösungen, die lizenzierten Teams-Benutzern (z. B. Gesundheitsdienstleistern oder Finanzberatern) und externen Benutzern (z. B. Patienten oder Kunden) über eine benutzerdefinierte Anwendung die Möglichkeit bieten, einer virtuellen Beratungsoberfläche beizutreten.

Es ist auch möglich, Microsoft 365 Teams-Identitäten mit den Azure Communication Services SDKs zu verwenden. Weitere Informationen sind [hier](./teams-interop.md) verfügbar.

Zurzeit ist es für einen Teams-Benutzer nicht möglich, einem Anruf beizutreten, der über das Anruf-SDK von Azure Communication Services initiiert wurde.

## <a name="enabling-anonymous-meeting-join-in-your-teams-tenant"></a>Aktivieren des anonymen Beitritts zu Besprechungen in Ihrem Teams-Mandanten

Wenn ein BYOI-Benutzer einer Teams-Besprechung beitritt, wird er wie ein anonymer externer Benutzer behandelt, ähnlich wie Benutzer, die einer Teams-Besprechung anonym über die Teams-Webanwendung beitreten. Die Möglichkeit für BYOI-Benutzer, Teams-Besprechungen als anonyme Benutzer beizutreten, wird durch die bereits vorhandene Konfiguration zum Zulassen des anonymen Besprechungsbeitritts gesteuert. Diese steuert auch den vorhandenen anonymen Besprechungsbeitritt von Teams. Diese Einstellung kann in [Teams Admin Center](https://admin.teams.microsoft.com/meetings/settings) oder mithilfe des Teams-PowerShell-Cmdlets [Set-CsTeamsMeetingConfiguration](/powershell/module/skype/set-csteamsmeetingconfiguration) aktualisiert werden.  

Benutzerdefinierte Anwendungen, die mit Azure Communication Services erstellt wurden, um mit Teams-Benutzern eine Verbindung herzustellen und zu kommunizieren, können von Endbenutzern oder Bots verwendet werden. In ihrer Darstellung für Teams-Benutzer unterscheiden sie sich nur dann, wenn der Entwickler der Anwendung dies explizit im Rahmen der Kommunikation angibt. Ihre benutzerdefinierte Anwendung sollte die Benutzerauthentifizierung und andere Sicherheitsmaßnahmen zum Schutz von Teams-Besprechungen berücksichtigen. Seien Sie sich der Auswirkungen auf die Sicherheit bewusst, wenn Sie anonymen Benutzern die Teilnahme an Besprechungen ermöglichen, und verwenden Sie den [Teams-Sicherheitsleitfaden](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings), um die für anonyme Benutzer verfügbaren Funktionen zu konfigurieren.

## <a name="meeting-experience"></a>Benutzererfahrung während der Besprechung

Genau wie beim anonymen Besprechungsbeitritt von Teams muss Ihre Anwendung über den Besprechungslink für den Beitritt verfügen. Dieser kann über die Graph-API oder aus dem Kalender in Microsoft Teams abgerufen werden. Die Namen von in Teams angezeigten BYOI-Benutzern können über das Anruf-SDK von Communication Services konfiguriert werden und sind als „extern“ gekennzeichnet, um Teams-Benutzern mitzuteilen, dass sie nicht über Azure Active Directory authentifiziert wurden. Wenn der erste ACS-Benutzer einer Teams-Besprechung beitritt, zeigt der Teams-Client eine Meldung an, die angibt, dass einige Features möglicherweise nicht verfügbar sind, da einer der Teilnehmer einen benutzerdefinierten Client verwendet.

Während einer Besprechung können Communication Services-Benutzer grundlegende Audio-, Video-, Bildschirmfreigabe- und Chatfunktionen über Azure Communication Services SDKs verwenden. Sobald ein Communication Services-Benutzer die Besprechung verlässt oder die Besprechung beendet wird, kann er keine neuen Chatnachrichten mehr senden oder empfangen. Er kann jedoch weiterhin auf Nachrichten zugreifen, die während der Besprechung gesendet und empfangen wurden. Anonyme Communication Services-Benutzer können der Besprechung keine zusätzlichen Teilnehmer hinzufügen oder sie aus ihr entfernen, und sie können für die Besprechung keine Aufzeichnung oder Transkription starten.

Weitere Informationen zu erforderlichen Dataflows für den Beitritt zu Teams-Besprechungen finden Sie auf der Seite [Client- und Serverarchitektur](client-and-server-architecture.md). Das [Hero-Beispiel für Gruppenanrufe](../samples/calling-hero-sample.md) enthält Beispielcode für den Beitritt zu einer Teams-Besprechung über eine Webanwendung.

## <a name="privacy"></a>Datenschutz
Die Interoperabilität zwischen Azure Communication Services und Microsoft Teams ermöglicht Ihren Anwendungen und Benutzern, an Teams-Anrufen, -Besprechungen und -Chats teilzunehmen. Sie sind dafür verantwortlich, dass die Benutzer Ihrer Anwendung benachrichtigt werden, wenn die Aufzeichnung oder Transkription in einem Teams-Anruf oder einer Teams-Besprechung aktiviert ist.

Microsoft gibt Ihnen über die Azure Communication Services-API einen Hinweis, dass die Aufzeichnung oder Transkription begonnen hat. Sie müssen dies Ihren Benutzer*innen auf der Benutzeroberfläche Ihrer Anwendung in Echtzeit mitteilen. Sie erklären sich damit einverstanden, Microsoft für alle Kosten und Schäden freizustellen, die aufgrund der Nichteinhaltung dieser Verpflichtung durch Sie entstehen.

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

- Ein BYOI-Benutzer darf an einer Teams-Besprechung teilnehmen, die für einen Teams-Kanal geplant ist, sowie Audio- und Videodaten verwenden. Er kann aber keine Chatnachrichten senden oder empfangen, weil er kein Mitglied des Kanals ist.
- Wenn Sie Microsoft Graph verwenden, um [die Teilnehmer einer Teams-Besprechung aufzulisten](/graph/api/call-list-participants), werden derzeit keine Details für Communication Services-Benutzer berücksichtigt.
- PowerPoint-Präsentationen werden für Nutzer der Kommunikationsdienste nicht wiedergegeben.
- Teams-Besprechungen unterstützen bis zu 1.000 Teilnehmer, aber das Anruf-SDK von Azure Communication Services unterstützt derzeit nur 350 Teilnehmer.
- Bei [Cloud Video Interop für Microsoft Teams](/microsoftteams/cloud-video-interop) wurden auf einigen Geräten Probleme festgestellt, wenn ein Communication Services-Benutzer den Bildschirm teilte.
- Features wie das Heben der Hand, der Zusammen-Modus und Gruppenräume stehen nur Teams-Benutzern zur Verfügung.
- Untertitel für Teams-Besprechungen werden vom Anruf-SDK derzeit nicht unterstützt.
- Communication Services-Benutzer können [Teams-Liveereignissen](/microsoftteams/teams-live-events/what-are-teams-live-events) nicht beitreten
- [Teams-Aktivitätshandlerereignisse](/microsoftteams/platform/bots/bot-basics?tabs=csharp) für Bots werden nicht ausgelöst, wenn Communication Services-Benutzer einer Teams-Besprechung beitreten.

## <a name="next-steps"></a>Nächste Schritte

- [Anleitung: An einer Teambesprechung teilnehmen](../how-tos/calling-sdk/teams-interoperability.md)
- [Schnellstart: Beitritt einer BYOI-Anruf-App zu einer Teams-Besprechung](../quickstarts/voice-video-calling/get-started-teams-interop.md)
- [Schnellstart: Beitritt einer BYOI-Chat-App zu einer Teams-Besprechung](../quickstarts/chat/meeting-interop.md)
