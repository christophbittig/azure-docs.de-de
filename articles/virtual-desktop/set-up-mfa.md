---
title: 'Azure Multifactor Authentication für Azure Virtual Desktop: Azure'
description: Erfahren Sie, wie Sie die mehrstufige Azure-Authentifizierung einrichten, um Azure Virtual Desktop sicherer zu machen.
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2e86597f0307699c2fc4cac9f48eac5b884e8df3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363608"
---
# <a name="enable-azure-multifactor-authentication-for-azure-virtual-desktop"></a>Aktivieren der mehrstufigen Authentifizierung von Azure für Azure Virtual Desktop

>[!IMPORTANT]
> Wenn Sie diese Seite von der Dokumentation zu Azure Virtual Desktop (klassisch) aus besuchen, stellen Sie sicher, dass Sie [zur Dokumentation zu Azure Virtual Desktop (klassisch) zurückkehren](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md), wenn Sie fertig sind.

Der Windows-Client für Azure Virtual Desktop ist eine hervorragende Option für die Integration von Azure Virtual Desktop in Ihren lokalen Computer. Wenn Sie jedoch Ihr Azure Virtual Desktop-Konto für den Windows-Client konfigurieren, müssen Sie bestimmte Maßnahmen ergreifen, um sich selbst und Ihre Benutzer zu schützen.

Wenn Sie sich zum ersten Mal anmelden, fragt der Client nach Benutzernamen, Kennwort und Azure Multi-Factor Authentication. Wenn Sie sich das nächste Mal anmelden, speichert der Client Ihr Token von ihrer Azure Active Directory-Unternehmensanwendung. Wenn Sie in der Eingabeaufforderung für Anmeldeinformationen für den Sitzungshost **Speichern** auswählen, können sich Ihre Benutzer nach dem Neustart des Clients anmelden, ohne ihre Anmeldeinformationen erneut eingeben zu müssen.

Das Speichern von Anmeldeinformationen ist zwar praktisch, kann aber auch die Sicherheit von Bereitstellungen in Unternehmensszenarien oder auf persönlichen Geräten beeinträchtigen. Zum Schutz der Benutzer können Sie sicherstellen, dass der Client häufiger zur Eingabe von Anmeldeinformationen für Azure Multi-Factor Authentication auffordert. In diesem Artikel erfahren Sie, wie Sie die Richtlinie für bedingten Zugriff für Azure Virtual Desktop konfigurieren, um diese Einstellung zu aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

Voraussetzungen für die ersten Schritte:

- Weisen Sie Benutzern eine Lizenz zu, die Azure Active Directory Premium P1 oder P2 umfasst.
- Eine Azure Active Directory-Gruppe, bei der Ihre Benutzer als Gruppenmitglieder zugewiesen sind
- Aktivieren Sie die Azure Multi-Factor Authentication für all Ihre Benutzer. Weitere Informationen dazu finden Sie unter [Vorgehensweise zum Erzwingen einer zweistufigen Überprüfung für einen Benutzer](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Die folgende Einstellung gilt auch für den [Azure Virtual Desktop-Webclient](https://rdweb.wvd.microsoft.com/arm/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Erstellen der Richtlinie für bedingten Zugriff

Hier erfahren Sie, wie Sie eine Richtlinie für bedingten Zugriff erstellen, die zur Verbindung mit Azure Virtual Desktop mehrstufige Authentifizierung erfordert:

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
2. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
3. Wählen Sie **Neue Richtlinie**.
4. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
5. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen**.
6. Wählen Sie unter **Einschließen** **Benutzer und Gruppen auswählen** > **Benutzer und Gruppen** aus, und wählen Sie die Gruppe aus, die Sie in der [Voraussetzungsphase](#prerequisites) erstellt haben.
7. Wählen Sie **Fertig** aus.
8. Wählen Sie unter **Cloud-Apps oder -Aktionen** > **Einschließen** die Option **Apps auswählen** aus.
9. Wählen Sie eine der folgenden Apps aus, je nach der verwendeten Version von Azure Virtual Desktop.
   
   - Wenn Sie Azure Virtual Desktop (klassisch) verwenden, wählen Sie diese Apps aus:
       
       - **Azure Virtual Desktop** (App ID 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Azure Virtual Desktop Client** (App-ID fa4345a4-a730-4230-84a8-7d9651b86739), mit der Sie Richtlinien für den Webclient festlegen können
       
        Fahren Sie danach mit Schritt 11 fort.

   - Wenn Sie Azure Virtual Desktop verwenden, wählen Sie stattdessen diese App aus:
       
       -  **Azure Virtual Desktop** (App-ID 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        Fahren Sie anschließend mit Schritt 10 fort.

   >[!IMPORTANT]
   > Wählen Sie nicht die App namens Azure Virtual Desktop Azure Resource Manager-Anbieter (50e95039-b200-4007-bc97-8d5790743a63) aus. Diese App wird nur zum Abrufen des Benutzerfeeds verwendet und sollte keine Multi-Factor Authentication erfordern.
   > 
   > Wenn Sie Azure Virtual Desktop (klassisch) verwenden und die Richtlinie für bedingten Zugriff den gesamten Zugriff blockiert und nur Azure Virtual Desktop-App-IDs ausnimmt, können Sie das Problem beheben, indem Sie der Richtlinie die App-ID „9cdead84-a844-4324-93f2-b2e6bb768d07“ hinzufügen. Wenn Sie diese App-ID nicht hinzufügen, wird die Feedermittlung für Ressourcen von Azure Virtual Desktop (klassisch) blockiert.

10. Wechseln Sie zu **Bedingungen** > **Client-Apps**. Wählen Sie unter **Konfigurieren** die Option **Ja** aus, und wählen Sie dann aus, wo die Richtlinie angewendet werden soll:
    
    - Aktivieren Sie **Browser**, wenn die Richtlinie auf den Webclient angewandt werden soll.
    - Aktivieren Sie **Mobile Apps und Desktopclients**, wenn die Richtlinie auf andere Clients angewandt werden soll.
    - Aktivieren Sie beide Kontrollkästchen, wenn die Richtlinie auf alle Clients angewandt werden soll.
   
    > [!div class="mx-imgBorder"]
    > ![Screenshot des Seite „Apps“ des Clients. Der Benutzer hat das Kontrollkästchen „Mobile Apps und Desktopclients“ aktiviert.](media/select-apply.png)

11. Nachdem Sie Ihre App ausgewählt haben, wählen Sie **Auswählen** und dann **Fertig** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Seite „Cloud-Apps oder -aktionen“. Die Azure Virtual Desktop- und Azure Virtual Desktop-Client-Apps werden rot hervorgehoben.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >Um die App-ID der auszuwählenden App zu finden, wechseln Sie zu **Unternehmensanwendungen**, und wählen Sie **Microsoft-Anwendungen** aus dem Dropdownmenü für den Anwendungstyp aus.

12. Wählen Sie unter **Zugriffssteuerung** > **Erteilen** die Option **Zugriff erteilen**, dann **Mehrstufige Authentifizierung erforderlich** und anschließend **Auswählen** aus.
13. Wählen Sie unter **Zugriffssteuerung** > **Sitzung** die Option **Anmeldehäufigkeit** aus, und legen Sie den Wert auf die gewünschte Zeitspanne zwischen Eingabeaufforderungen fest. Klicken Sie dann auf **Auswählen**. Durch Festlegen des Werts auf **1** und der Einheit auf **Stunden** wird eine mehrstufige Authentifizierung erforderlich, wenn eine Verbindung eine Stunde nach der letzten Verbindung gestartet wird.
14. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
15. Wählen Sie **Erstellen** aus, um die Richtlinie zu aktivieren.

>[!NOTE]
>Wenn Sie den Webclient verwenden, um sich über Ihren Browser bei Azure Virtual Desktop anzumelden, listet das Protokoll die Client-App-ID als a85cf173-4192-42f8-81fa-777a763e6e2c (Azure Virtual Desktop-Client) auf. Dies liegt daran, dass die Client-App intern mit der Server-App-ID verknüpft ist, in der die Richtlinie für bedingten Zugriff festgelegt wurde. 

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu Richtlinien für bedingten Zugriff](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Weitere Informationen zur Benutzeranmeldehäufigkeit](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
