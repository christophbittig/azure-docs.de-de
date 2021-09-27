---
title: Häufig gestellte Fragen zum bedingten Zugriff mit Azure Active Directory | Microsoft-Dokumentation
description: Enthält Antworten auf häufig gestellte Fragen zum bedingten Zugriff in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 906425fc1f221a39ae5523a96ebf4d358a54153c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637853"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Häufig gestellte Fragen zum bedingten Zugriff mit Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Welche Anwendungen arbeiten mit Richtlinien für den bedingten Zugriff?

Informationen zu Anwendungen, von denen Richtlinien für den bedingten Zugriff verwendet werden, finden Sie unter [Anwendungen und Browser, die Regeln für den bedingten Zugriff in Azure Active Directory verwenden](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Werden Richtlinien für den bedingten Zugriff für B2B Collaboration- und Gastbenutzer erzwungen?

Richtlinien werden für B2B-Kollaborationsbenutzer (Business-to-Business) erzwungen. In einigen Fällen kann es aber vorkommen, dass ein Benutzer die Richtlinienanforderungen nicht erfüllen kann. Beispielsweise kann es sein, dass die Organisation eines Gastbenutzers die mehrstufige Authentifizierung nicht unterstützt. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Gelten SharePoint Online-Richtlinien auch für OneDrive for Business?

Ja. Eine SharePoint Online-Richtlinie gilt auch für OneDrive for Business. Weitere Informationen finden Sie im Artikel [Dienstabhängigkeiten für den bedingten Zugriff](service-dependencies.md). Ziehen Sie außerdem in Betracht, stattdessen Richtlinien für die [Office 365-App](concept-conditional-access-cloud-apps.md#office-365) anzuwenden.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Warum kann ich keine Richtlinien direkt in Client-Apps wie Word oder Outlook festlegen?

Mit einer Richtlinie für bedingten Zugriff werden die Anforderungen für den Zugriff auf einen Dienst festgelegt. Die Richtlinie wird erzwungen, wenn die Authentifizierung für diesen Dienst durchgeführt wird. Die Richtlinie wird nicht direkt für eine Clientanwendung festgelegt. Stattdessen wird sie angewendet, wenn ein Client einen Dienst aufruft. Eine Richtlinie, die für SharePoint festgelegt wurde, gilt beispielsweise für Clients, die SharePoint aufrufen. Eine Richtlinie, die für Exchange festgelegt wurde, gilt für Outlook. Weitere Informationen finden Sie im Artikel [Dienstabhängigkeiten für den bedingten Zugriff](service-dependencies.md). Ziehen Sie außerdem in Betracht, stattdessen Richtlinien für die [Office 365-App](concept-conditional-access-cloud-apps.md#office-365) anzuwenden.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Gilt eine Richtlinie für bedingten Zugriff für Dienstkonten?

Richtlinien für bedingten Zugriff gelten für alle Benutzerkonten. Dies schließt auch Benutzerkonten ein, die als Dienstkonten verwendet werden. Häufig kann ein Dienstkonto, das unbeaufsichtigt ausgeführt wird, die Anforderungen einer Richtlinie für bedingten Zugriff nicht erfüllen. Beispielsweise ist unter Umständen die mehrstufige Authentifizierung erforderlich. Dienstkonten können mithilfe eines [Benutzer- oder Gruppenausschlusses](concept-conditional-access-users-groups.md#exclude-users) aus einer Richtlinie ausgeschlossen werden. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Wie lautet die standardmäßige Ausschlussrichtlinie für nicht unterstützte Geräteplattformen?

Derzeit werden Richtlinien für den bedingten Zugriff für Benutzer von iOS- und Android-Geräten selektiv erzwungen. Anwendungen auf anderen Geräteplattformen sind standardmäßig von der Richtlinie für den bedingten Zugriff für iOS- und Android-Geräte ausgenommen. Ein Mandantenadministrator kann die globale Richtlinie außer Kraft setzen, um Benutzern auf nicht unterstützten Plattformen den Zugriff zu verweigern.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Wie funktionieren Richtlinien für bedingten Zugriff für Microsoft Teams?

Microsoft Teams basiert in Bezug auf wichtige Produktivitätsszenarien, z.B. Besprechungen, Kalender und Dateifreigabe, vor allem auf Exchange Online und SharePoint Online. Richtlinien für bedingten Zugriff, die für diese Cloud-Apps festgelegt werden, gelten für Microsoft Teams, wenn sich ein Benutzer direkt bei Microsoft Teams anmeldet.

Microsoft Teams wird in Richtlinien für bedingten Zugriff auch separat als Cloud-App unterstützt. Richtlinien für bedingten Zugriff, die für eine Cloud-App festgelegt werden, gelten für Microsoft Teams, wenn sich ein Benutzer anmeldet. Allerdings können ohne korrekte Richtlinien für andere Apps wie Exchange Online und SharePoint Online Benutzer möglicherweise weiterhin direkt auf diese Ressourcen zugreifen.

Microsoft Teams-Desktopclients für Windows und Mac unterstützen die moderne Authentifizierung. Die moderne Authentifizierung ermöglicht die ADAL-basierte Anmeldung (Azure Active Directory Authentication Library) plattformübergreifend für Microsoft Office-Clientanwendungen.

Weitere Informationen finden Sie im Artikel [Dienstabhängigkeiten für den bedingten Zugriff](service-dependencies.md). Ziehen Sie außerdem in Betracht, stattdessen Richtlinien für die [Office 365-App](concept-conditional-access-cloud-apps.md#office-365) anzuwenden.

## <a name="why-are-some-tabs-not-working-in-microsoft-teams-after-enabling-conditional-access-policies"></a>Warum funktionieren in Microsoft Teams einige Registerkarten nach dem Aktivieren von Richtlinien für bedingten Zugriff nicht?

Nachdem einige Richtlinien für bedingten Zugriff für den Mandanten in Microsoft Teams aktiviert wurden, funktionieren möglicherweise im Desktopclient bestimmte Registerkarten nicht mehr wie erwartet. Die betreffenden Registerkarten funktionieren jedoch bei der Verwendung des Microsoft Teams-Webclients. Die betreffenden Registerkarten können Power BI, Forms, VSTS, Power Apps und SharePoint-Listen enthalten.

Um die betreffenden Registerkarten anzuzeigen, müssen Sie den Teams-Webclient in Microsoft Edge, Internet Explorer oder Chrome mit installierter Windows 10-Kontoerweiterung verwenden. Einige Registerkarten sind von der Webauthentifizierung abhängig, die im Microsoft Teams-Desktopclient nicht funktioniert, wenn der bedingte Zugriff aktiviert ist. Microsoft arbeitet zusammen mit den Partnern daran, diese Szenarios zu ermöglichen. Bisher konnten wir Szenarien mit Planner, OneNote und Stream realisieren.

## <a name="next-steps"></a>Nächste Schritte

- Zum Konfigurieren von Richtlinien für bedingten Zugriff für Ihre Umgebung lesen Sie den Artikel [Planen einer Bereitstellung für bedingten Zugriff](plan-conditional-access.md). 
