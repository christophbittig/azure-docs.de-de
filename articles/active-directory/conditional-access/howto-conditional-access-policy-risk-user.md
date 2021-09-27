---
title: 'Auf dem Benutzerrisiko basierender bedingter Zugriff: Azure Active Directory'
description: Erstellen von Richtlinien für bedingten Zugriff mithilfe der Azure AD Identity Protection-Funktion für den auf dem Benutzerrisiko basierender bedingter Zugriff
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/04/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae788ea2ee96ea059e919a9e720216162618312
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596427"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Bedingter Zugriff: Auf dem Benutzerrisiko basierender bedingter Zugriff

Microsoft arbeitet mit Ermittlern, Strafverfolgungsbehörden, mehreren eigenen Sicherheitsteams und anderen vertrauenswürdigen Quellen zusammen, um kompromittierte Paare aus Benutzername und Kennwort zu finden. Organisationen mit Azure AD Premium P2-Lizenzen können Richtlinien für bedingten Zugriff erstellen, die [das Erkennen des Benutzerrisikos mit Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md#user-linked-detections) umfassen. 

Es gibt zwei Orte, an denen diese Richtlinie konfiguriert werden kann: bedingter Zugriff und Identity Protection. Die Konfiguration mithilfe einer Richtlinie für bedingten Zugriff ist die bevorzugte Methode, da sie mehr Kontext bietet, z. B. erweiterte Diagnosedaten, die Integration nur im Berichtsmodus, Graph-API-Unterstützung und die Möglichkeit, andere Attribute für bedingten Zugriff in der Richtlinie zu verwenden.

## <a name="enable-with-conditional-access-policy"></a>Aktivieren mit einer Richtlinie für bedingten Zugriff

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen. Weitere Informationen finden Sie unter [Festlegen von Benennungsstandards für Ihre Richtlinien](./plan-conditional-access.md#set-naming-standards-for-your-policies).
1. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen**.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** aus.
   1. Wählen Sie unter **Ausschließen** die Option **Benutzer und Gruppen** und dann die Konten für den Notfallzugriff Ihres Unternehmens aus. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Alle Cloud-Apps** aus.
1. Legen Sie unter **Bedingungen** > **Benutzerrisiko** die Option **Konfigurieren** auf **Ja** fest. Wählen Sie unter **Hiermit konfigurieren Sie die Benutzerrisikostufen, die für die Erzwingung der Richtlinie erforderlich sind** die Option **Hoch** und dann **Fertig** aus.
1. Wählen Sie unter **Zugriffssteuerungen** > **Erteilen** die Option **Zugriff erteilen**, dann **Kennwortänderung erforderlich** und anschließend **Auswählen** aus.
1. Bestätigen Sie die Einstellungen, und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

## <a name="enable-through-identity-protection"></a>Aktivieren über Identity Protection

1. Melden Sie sich beim **Azure-Portal** an.
1. Wählen Sie **Alle Dienste** aus, und navigieren Sie zu **Azure AD Identity Protection**.
1. Wählen Sie **Benutzerrisiko-Richtlinie** aus.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** aus.
   1. Wählen Sie unter **Ausschließen** die Option **Ausgeschlossene Benutzer auswählen** und dann die Konten für den Notfallzugriff Ihres Unternehmens aus. Abschließend wählen Sie **Auswählen** aus.
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Bedingungen** die Option **Benutzerrisiko** und dann **Hoch** aus.
   1. Wählen Sie **Auswählen** und anschließend **Fertig** aus.
1. Wählen Sie unter **Kontrollen** > **Zugriff** die Option **Zugriff zulassen** und dann **Kennwortänderung anfordern** aus.
   1. Wählen Sie **Auswählen**.
1. Legen Sie **Richtlinie erzwingen** auf **Ein** fest.
1. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

[Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)

[Risikobasierter bedingter Zugriff beim Anmelden](howto-conditional-access-policy-risk.md)

[Bestimmen der Auswirkung durch Verwendung des reinen Berichtsmodus des bedingten Zugriffs](howto-conditional-access-insights-reporting.md)

[Simulieren des Anmeldeverhaltens mit dem Was-wäre-wenn-Tool für den bedingten Zugriff](troubleshoot-conditional-access-what-if.md)

[Was ist Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)