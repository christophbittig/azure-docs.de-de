---
title: Konfigurieren von „Angemeldet bleiben?“ Eingabeaufforderung für Azure Active Directory-Konten
description: Lernen Sie die Option „Angemeldet bleiben“ kennen, über die die Eingabeaufforderung „Angemeldet bleiben?“ angezeigt wird. Sie erfahren außerdem, wie Sie sie im Azure Active Directory-Portal konfigurieren und Anmeldeprobleme behandeln.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: e89eb124b9cfb4100ddfca0d949331508fce398f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052387"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Konfigurieren von „Angemeldet bleiben?“ Eingabeaufforderung für Azure AD-Konten

Über die Option „Angemeldet bleiben“ wird die Eingabeaufforderung **Angemeldet bleiben?** angezeigt, nachdem sich ein Benutzer erfolgreich angemeldet hat. Wenn ein Benutzer diese Eingabeaufforderung mit **Ja** beantwortet, erhält er vom Dienst „Angemeldet bleiben“ ein permanentes [Aktualisierungstoken](../develop/developer-glossary.md#refresh-token). Bei Verbundmandanten wird die Eingabeaufforderung angezeigt, nachdem sich der Benutzer erfolgreich beim Verbundidentitätsdienst authentifiziert hat.

Das folgende Diagramm zeigt den Benutzeranmeldeflow bei einem verwalteten Mandanten und einem Verbundmandanten und die neue Eingabeaufforderung „Angemeldet bleiben?“. Dieser Flow enthält intelligente Logik, sodass die Option **Angemeldet bleiben?** nicht angezeigt wird, wenn das Machine Learning-System eine Anmeldung mit hohem Risiko oder eine Anmeldung von einem gemeinsam genutzten Gerät erkennt.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagramm mit dem Benutzeranmeldeflow bei einem verwalteten Mandanten im Vergleich zu einem Verbundmandanten":::

> [!NOTE]
> Zum Konfigurieren der Option „Angemeldet bleiben“ müssen Sie die Azure Active Directory (Azure AD)-Editionen „Premium 1“, „Premium 2“ oder „Basic“ verwenden oder eine Microsoft 365-Lizenz haben. Weitere Informationen zu Lizenzierung und Editionen finden Sie unter [Registrieren für Azure AD Premium](active-directory-get-started-premium.md).<br><br>Die Azure AD-Editionen „Premium“ und „Basic“ sind für Kunden in China verfügbar, die mit der weltweiten Instanz von Azure AD arbeiten. Allerdings werden die Azure AD-Editionen „Premium“ und „Basic“ derzeit nicht durch den Azure-Dienst unterstützt, der in China von 21Vianet betrieben wird. Sollten Sie weitere Informationen benötigen, können Sie sich über das [Azure AD-Forum](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) mit uns in Verbindung setzen.

## <a name="configure-kmsi"></a>Konfigurieren von „Angemeldet bleiben?“

1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie **Azure Active Directory**, dann **Unternehmensbranding** und schließlich **Konfigurieren** aus.
1. Suchen Sie im Abschnitt **Erweiterte Einstellungen** die Einstellung **Option „Angemeldet bleiben“ anzeigen**.

   Mit dieser Einstellung können Sie auswählen, ob Ihre Benutzer bei Azure AD angemeldet bleiben, bis sie sich explizit abmelden.
   * Wenn Sie **Nein** auswählen, wird die Option **Angemeldet bleiben?** ausgeblendet, nachdem sich der Benutzer erfolgreich angemeldet hat. Der Benutzer muss sich jedes Mal anmelden, wenn der Browser geschlossen und erneut geöffnet wird.
   * Wenn Sie **Ja** auswählen, wird dem Benutzer die Option **Angemeldet bleiben?** angezeigt.

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Screenshot mit der Einstellung „Option ‚Angemeldet bleiben‘ anzeigen“":::

## <a name="troubleshoot-sign-in-issues"></a>Beheben von Problemen bei der Anmeldung

Wenn ein Benutzer nicht auf die Eingabeaufforderung **Angemeldet bleiben?** reagiert (wie im folgenden Diagramm gezeigt), sondern den Anmeldeversuch abbricht, wird über einen Eintrag im Anmeldeprotokoll der Interrupt angezeigt.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Zeigt die Eingabeaufforderung „Angemeldet bleiben?“":::

Einzelheiten zum Anmeldefehler lauten wie folgt und sind im Beispiel hervorgehoben.

* **Anmeldefehlercode**: 50140
* **Fehlerursache**: Dieser Fehler ist beim Anmelden des Benutzers aufgrund des Interrupts bei „Angemeldet bleiben?“ aufgetreten.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Beispieleintrag im Anmeldeprotokoll mit dem Interrupt bei „Angemeldet bleiben?“":::

Sie können verhindern, dass Benutzern der Interrupt angezeigt wird, indem Sie unter „Unternehmensbranding“ in den erweiterten Einstellungen die Einstellung **Option „Angemeldet bleiben“ anzeigen** auf **Nein** festlegen. Dadurch wird die Eingabeaufforderung „Angemeldet bleiben?“ für alle Benutzer im Azure AD-Verzeichnis deaktiviert.

Sie können auch die Steuerelemente für die persistente Browsersitzung im bedingten Zugriff verwenden, um zu verhindern, dass Benutzern die Eingabeaufforderung „Angemeldet bleiben?“ angezeigt wird. Mit dieser Option können Sie die Eingabeaufforderung „Angemeldet bleiben?“ für eine ausgewählte Gruppe von Benutzern (z. B. globale Administratoren) deaktivieren, ohne dass sich dies auf das Anmeldeverhalten der übrigen Benutzer im Verzeichnis auswirkt. Weitere Informationen finden Sie unter [Anmeldehäufigkeit von Benutzern](../conditional-access/howto-conditional-access-session-lifetime.md). 

Um sicherzustellen, dass die KMSI-Eingabeaufforderung nur angezeigt wird, wenn der Benutzer davon profitieren kann, wird die KMSI-Eingabeaufforderung in den folgenden Szenarien absichtlich nicht angezeigt:

* Der Benutzer ist über nahtloses SSO und integrierte Windows-Authentifizierung (Integrated Windows Authentication, IWA) angemeldet.
* Der Benutzer ist über Active Directory-Verbunddienste (AD FS) und IWA angemeldet.
* Der Benutzer ist ein Gast im Mandanten.
* Die Risikobewertung des Benutzers ist hoch.
* Die Anmeldung erfolgt während des Benutzer- oder Administratoreinwilligungsflows.
* Persistente Browsersitzungssteuerung ist in einer Richtlinie für bedingten Zugriff konfiguriert.

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie weitere Einstellungen kennen, die sich auf das Timeout der Anmeldesitzung auswirken:

* Microsoft 365 – [Abmelden von inaktiven Benutzern](/sharepoint/sign-out-inactive-users)
* Bedingter Azure AD-Zugriff – [Anmeldehäufigkeit von Benutzern](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure-Portal – [Festlegen des Inaktivitätstimeouts auf Verzeichnisebene](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)