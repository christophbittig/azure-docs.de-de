---
title: Einbinden eines neuen Windows 10-Geräts in Azure AD auf der Windows-Willkommensseite | Microsoft-Dokumentation
description: Es wird beschrieben, wie Benutzer Azure AD Join während des OOBE-Vorgangs (Out-of-Box Experience) einrichten können.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b76ade4b47417cb6c3d4f65daa335553c7a4b83
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179179"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Tutorial: Einbinden eines neuen Windows 10-Geräts in Azure AD auf der Windows-Willkommensseite

Mit der Geräteverwaltung in Azure Active Directory (Azure AD) können Sie sicherstellen, dass Benutzer auf Ihre Ressourcen über Geräte zugreifen, die Ihren Standards für Sicherheit und Konformität entsprechen. Weitere Informationen finden Sie in der [Einführung in die Geräteverwaltung in Azure Active Directory](overview.md).

Mit Windows 10 können Sie auf der Windows-Willkommensseite ein neues Gerät in Azure AD einbinden.  
Dadurch können Sie eingeschweißte Geräte an Ihre Mitarbeiter oder Schüler/Studenten verteilen.

Wenn Windows 10 Professional oder Windows 10 Enterprise auf einem Gerät installiert ist, wird standardmäßig der Setupprozess für firmeneigene Geräte übernommen.

Auf der *Windows-Willkommensseite* wird das Verknüpfen einer lokalen Active Directory-Domäne (AD) nicht unterstützt. Wenn Sie einen Computer mit einer AD-Domäne verknüpfen möchten, klicken Sie während des Setups auf den Link **Windows mit einem lokalen Konto einrichten**. Sie können dann die Domäne über die Einstellungen Ihres Computers verknüpfen.
 
In diesem Tutorial erfahren Sie, wie Sie auf der Windows-Willkommensseite ein Gerät in Azure AD einbinden:
 > [!div class="checklist"]
> * Voraussetzungen
> * Einbinden eines Geräts
> * Überprüfung

## <a name="prerequisites"></a>Voraussetzungen

Um ein Windows 10-Gerät einzubinden, muss der Geräteregistrierungsdienst so konfiguriert sein, dass Sie Geräte registrieren können. Sie benötigen Berechtigungen zum Einbinden von Geräten in den Azure AD-Mandanten. Darüber hinaus müssen weniger Geräte registriert sein als die konfigurierte maximale Anzahl. Weitere Informationen finden Sie unter [Configure device settings](device-management-azure-portal.md#configure-device-settings) (Konfigurieren von Geräteeinstellungen).

Falls Ihr Mandant einem Verbund angehört, MUSS Ihr Identitätsanbieter außerdem den WS-Fed- und WS-Trust-Endpunkt mit Benutzername/Kennwort unterstützen. Zulässige Versionen sind 1.3 und 2005. Dieses Protokoll muss unterstützt werden, um das Gerät mit Azure AD verknüpfen und sich mit einem Kennwort beim Gerät anmelden zu können.

## <a name="joining-a-device"></a>Einbinden eines Geräts

**So binden Sie ein Windows 10-Gerät auf der Windows-Willkommensseite in Azure AD ein:**

1. Wenn Sie das neue Gerät einschalten und den Setupprozess starten, sollte die Meldung **Vorbereitung** angezeigt werden. Befolgen Sie die Anweisungen, um Ihr Gerät einzurichten.
1. Passen Sie zuerst die Region und die Sprache an. Akzeptieren Sie anschließend die Microsoft-Software-Lizenzbedingungen.
 
    <!--![Customize for your region](./media/azuread-joined-devices-frx/01.png)-->

1. Wählen Sie das Netzwerk aus, das Sie für die Verbindung mit dem Internet verwenden möchten.
1. Klicken Sie auf **Dieses Gerät gehört meiner Organisation**. 

    <!--![Who owns this PC screen](./media/azuread-joined-devices-frx/02.png)-->

1. Geben Sie die Anmeldeinformationen ein, die von Ihrer Organisation bereitgestellt wurden, und klicken Sie auf **Anmelden**.

    <!--![Sign-in screen](./media/azuread-joined-devices-frx/03.png)-->

1. Ihr Gerät ermittelt einen übereinstimmenden Mandanten in Azure AD. Wenn Sie in einer Verbunddomäne sind, werden Sie an Ihren lokalen Sicherheitstokendienst-Server (STS) umgeleitet, beispielsweise Active Directory-Verbunddienste (AD FS).
1. Wenn Sie ein Benutzer in einer nicht verbundenen Domäne sind, geben Sie Ihre Anmeldeinformationen direkt auf der von Azure AD gehosteten Seite ein. 
1. Sie werden zu einer mehrstufigen Authentifizierung aufgefordert. 
1. Azure AD prüft, ob eine Anmeldung zur mobilen Geräteverwaltung erforderlich ist.
1. Windows registriert das Gerät im Verzeichnis der Organisation in Azure AD und in der Mobilgeräteverwaltung (falls zutreffend).
1. Wenn Sie…
   - ein verwalteter Benutzer sind, leitet Windows Sie über den automatischen Anmeldeprozess zum Desktop weiter.
   - ein Verbundbenutzer sind, werden Sie zum Windows-Anmeldebildschirm weitergeleitet und müssen Ihre Anmeldeinformationen eingeben.

## <a name="verification"></a>Überprüfung

Ob ein Gerät in Ihre Azure AD-Instanz eingebunden wurde, können Sie im Dialogfenster **Auf Arbeits- oder Schulkonto zugreifen** Ihres Windows-Geräts überprüfen. Im Dialogfenster sollte angegeben sein, dass Sie mit dem Azure AD-Verzeichnis verbunden sind.

![Auf Arbeits- oder Schulkonto zugreifen](./media/azuread-joined-devices-frx/13.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der [Einführung in die Geräteverwaltung in Azure Active Directory](overview.md).
- Weitere Informationen zur Verwaltung von Geräten im Azure AD-Portal finden Sie unter [Verwalten von Geräten mithilfe des Azure-Portals](device-management-azure-portal.md).
