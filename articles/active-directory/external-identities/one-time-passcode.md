---
title: Authentifizierung mit Einmalkennung für B2B-Gastbenutzer – Azure AD
description: Verwenden der Einmalkennung per E-Mail zum Authentifizieren von B2B-Gastbenutzern ohne Einrichten eines Microsoft-Kontos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/26/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019, contperf-fy21q4-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2778e2e08c416837de78567fde5dbfb61d04804
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067294"
---
# <a name="email-one-time-passcode-authentication"></a>Authentifizierung mit Einmalkennung per E-Mail

Das Feature „Einmalkennung per E-Mail“ ist eine Möglichkeit zum Authentifizieren von Benutzern für die B2B-Zusammenarbeit, wenn sie nicht auf andere Weise – z. B. Azure AD, Microsoft-Konto (MSA) oder soziales Netzwerk als Identitätsanbieter – authentifiziert werden können. Wenn ein B2B-Gastbenutzer versucht, Ihre Einladung einzulösen oder sich bei Ihren freigegebenen Ressourcen anzumelden, kann er eine temporäre Kennung anfordern, die an seine E-Mail-Adresse gesendet wird. Dann gibt er diesen so genannten „Passcode“ ein, um den Anmeldevorgang fortzusetzen.

Sie können dieses Feature im Azure-Portal jederzeit aktivieren, indem Sie den Identitätsanbieter für die Einmalkennung per E-Mail unter den Einstellungen für „External Identities“ Ihres Mandanten konfigurieren. Sie können dieses Feature aktivieren, deaktivieren oder auf die automatische Aktivierung ab 1. November 2021 warten.

![Einmalkennung per E-Mail, Übersichtsdiagramm](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
>
> - **Ab 1. November 2021** wird mit der Aktivierung des Features „Einmalkennung per E-Mail“ für alle bestehenden Mandanten begonnen. Bei neuen Mandanten wird es standardmäßig aktiviert. Im Rahmen dieser Änderung erstellt Microsoft während der Einlösung von Einladungen für die B2B-Zusammenarbeit keine neuen, nicht verwalteten („viralen“) Azure AD-Konten und -Mandanten mehr. Um Unterbrechungen während der Feiertage und Bereitstellungssperren zu minimieren, findet das Rollout der Änderungen für die meisten Mandanten im Januar 2022 statt. Wir aktivieren die Funktion „Einmalkennung per E-Mail“, da sie eine nahtlose alternative Authentifizierungsmethode für Ihre Gastbenutzer bietet. Wenn Sie jedoch nicht möchten, dass dieses Feature automatisch aktiviert wird, können Sie es [deaktivieren](one-time-passcode.md#disable-email-one-time-passcode).
> - Die Einstellungen für die Einmalkennung per E-Mail wurden im Azure-Portal aus den **Einstellungen für externe Zusammenarbeit** in **Alle Identitätsanbieter** verschoben.

> [!NOTE]
> Benutzer mit Einmalkennung müssen sich über einen Link anmelden, der den Mandantenkontext enthält (z.B. `https://myapps.microsoft.com/?tenantid=<tenant id>` oder `https://portal.azure.com/<tenant id>` bzw. `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` bei einer überprüften Domäne). Direkte Links zu Anwendungen und Ressourcen funktionieren ebenfalls, sofern sie den Mandantenkontext enthalten. Gastbenutzer können sich derzeit nicht über Endpunkte, die keinen Mandantenkontext aufweisen, anmelden. Bei der Verwendung von `https://myapps.microsoft.com` tritt bei `https://portal.azure.com` ein Fehler auf.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Benutzeroberfläche für Gastbenutzer mit Einmalkennung

Wenn die Funktion „Einmalkennung per E-Mail“ aktiviert ist, wird für neu eingeladene Benutzer, [die bestimmte Bedingungen erfüllen](#when-does-a-guest-user-get-a-one-time-passcode), die Authentifizierung per Einmalkennung verwendet. Gastbenutzer, die ihre Einladung schon vor dem Aktivieren der Einmalkennung per E-Mail eingelöst haben, werden weiterhin mit der bisherigen Methode authentifiziert.

Bei der Authentifizierung mit Einmalkennung kann der Gastbenutzer seine Einladung über einen direkten Link oder mithilfe der Einladungs-E-Mail einlösen. In beiden Fällen gibt eine Nachricht im Browser an, dass ein Code an die E-Mail-Adresse des Gastbenutzers gesendet wird. Der Gastbenutzer klickt auf **Code senden**:

   ![Screenshot mit der Schaltfläche „Code senden“](media/one-time-passcode/otp-send-code.png)

Eine Kennung wird an die E-Mail-Adresse des Benutzers gesendet. Der Benutzer ruft die Kennung aus der E-Mail ab und gibt sie im Browserfenster ein:

   ![Screenshot mit der Schaltfläche „Code eingeben“](media/one-time-passcode/otp-enter-code.png)

Der Gastbenutzer wird jetzt authentifiziert und kann entweder die freigegebene Ressource anzeigen oder seinen Anmeldevorgang fortsetzen.

> [!NOTE]
> Einmalkennungen sind 30 Minuten gültig. Nach 30 Minuten ist die jeweilige Einmalkennung nicht mehr gültig, und der Benutzer muss eine neue Kennung anfordern. Benutzersitzungen laufen nach 24 Stunden ab. Danach erhält der Gastbenutzer eine neue Kennung, wenn er auf die Ressource zugreift. Der Ablauf der Sitzung sorgt für zusätzliche Sicherheit, besonders wenn ein Gastbenutzer das Unternehmen verlässt oder den Zugriff nicht mehr benötigt.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Wann erhält ein Gastbenutzer eine Einmalkennung?

Wenn ein Gastbenutzer eine Einladung einlöst oder einen Link zu einer Ressource verwendet, die für ihn freigegeben wurde, erhält er unter folgenden Bedingungen eine Einmalkennung:

- Er hat kein Azure AD-Konto
- Er hat kein Microsoft-Konto
- Der einladende Mandant hat keinen Verbund mit sozialen Netzwerken (wie [Google](google-federation.md)) oder anderen Identitätsanbietern eingerichtet.

Zum Zeitpunkt der Einladung gibt es keinen Hinweis darauf, dass der eingeladene Benutzer die Authentifizierung mit Einmalkennung verwendet. Wenn sich der Gastbenutzer jedoch anmeldet, wird die Authentifizierung mit Einmalkennung als alternative Methode verwendet, wenn keine anderen Authentifizierungsmethoden eingesetzt werden können.


> [!NOTE]
> Wenn ein Benutzer eine Einmalkennung einlöst und später ein MSA, ein Azure AD-Konto oder ein anderes Verbundkonto erhält, wird er weiterhin mit einer Einmalkennung authentifiziert. Wenn Sie die Authentifizierungsmethode des Benutzers aktualisieren möchten, können Sie den [Einlösungsstatus zurücksetzen](reset-redemption-status.md).

### <a name="example"></a>Beispiel

Gastbenutzer teri@gmail.com wird von Fabrikam eingeladen. Das Unternehmen hat keinen Verbund mit Google eingerichtet. Teri hat kein Microsoft-Konto. Er erhält für die Authentifizierung eine Einmalkennung.

## <a name="enable-email-one-time-passcode"></a>Aktivieren der Einmalkennung per E-Mail

1. Melden Sie sich als globaler Azure AD-Administrator im [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.

3. Wählen Sie **Externe Identitäten** > **Alle Identitätsanbieter** aus.

4. Wählen Sie **Einmalkennung per E-Mail** aus, um den Konfigurationsbereich zu öffnen.

5. Wählen Sie unter **Einmalkennung per E-Mail für Gastbenutzer** eine der folgenden Möglichkeiten aus:

   - **Aktivieren Sie die Einmalkennung per E-Mail für Gastbenutzer*innen automatisch ab \<date\>** , wenn Sie das Feature nicht sofort aktivieren und auf das automatische Aktivierungsdatum am 1. November 2021 warten möchten.
   - **Enable email one-time passcode for guests effective now** (Einmalkennung per E-Mail für Gastbenutzer mit sofortiger Gültigkeit aktivieren), um das Feature jetzt zu aktivieren.
   - **Ja**, um das Feature jetzt zu aktivieren, wenn die Umschaltfläche „Ja/Nein“ angezeigt wird (diese Umschaltfläche wird angezeigt, wenn das Feature zuvor deaktiviert wurde).

   ![Deaktivierte Umschaltfläche für „Einmalkennung per E-Mail“](media/one-time-passcode/enable-email-otp-options.png)

5. Wählen Sie **Speichern** aus.

## <a name="disable-email-one-time-passcode"></a>Deaktivieren der Einmalkennung per E-Mail

Ab 1. November 2021 wird mit der Aktivierung des Features „Einmalkennung per E-Mail“ für alle bestehenden Mandanten begonnen. Bei neuen Mandanten wird es standardmäßig aktiviert.  Ab diesem Zeitpunkt wird das Einlösen von Einladungen durch die Erstellung nicht verwalteter Azure AD-Konten und -Mandanten („viral“ oder „Just-In-Time“) für B2B-Zusammenarbeitsszenarien von Microsoft nicht mehr unterstützt. Wir aktivieren die Funktion „Einmalkennung per E-Mail“, da sie eine nahtlose alternative Authentifizierungsmethode für Ihre Gastbenutzer bietet. Sie können diese Funktion jedoch deaktivieren, wenn Sie sie nicht verwenden möchten.

> [!NOTE]
>
> Wenn Sie die in Ihrem Mandanten aktivierte Funktion „Einmalkennung per E-Mail“ deaktivieren, können sich alle Gastbenutzer, die eine Einmalkennung eingelöst haben, nicht anmelden. Sie können [deren Einlösungsstatus zurücksetzen](reset-redemption-status.md), damit sie sich mit einer anderen Authentifizierungsmethode erneut anmelden können.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>So deaktivieren Sie die Funktion „Einmalkennung per E-Mail“

1. Melden Sie sich als globaler Azure AD-Administrator im [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.

3. Wählen Sie **Externe Identitäten** > **Alle Identitätsanbieter** aus.

4. Wählen Sie **Einmalkennung per E-Mail** und dann unter **Einmalkennung per E-Mail für Gastbenutzer** die Option **Einmalkennung per E-Mail für Gastbenutzer deaktivieren** aus (oder wählen Sie **Nein** aus, wenn dieses Feature zuvor aktiviert, deaktiviert oder während der Vorschau aktiviert wurde).

   ![Deaktivierte Umschaltfläche für Einmalkennung per E-Mail](media/one-time-passcode/disable-email-otp-options.png)

   > [!NOTE]
   > Die Einstellungen für die Einmalkennung per E-Mail wurden im Azure-Portal aus den **Einstellungen für externe Zusammenarbeit** in **Alle Identitätsanbieter** verschoben.
   > Sollte anstelle der Optionen für die Einmalkennung per E-Mail eine Umschaltfläche angezeigt werden, bedeutet das, dass Sie die Funktion zuvor aktiviert oder deaktiviert bzw. die Vorschauversion genutzt haben. Wählen Sie **Nein** aus, um die Funktion zu deaktivieren.

5. Wählen Sie **Speichern** aus.

## <a name="note-for-public-preview-customers"></a>Hinweis für Kunden der öffentlichen Vorschauversion

Wenn Sie die öffentliche Vorschauversion der Funktion „Einmalkennung per E-Mail“ genutzt haben, gilt die automatische Funktionsaktivierung am 1. November 2021 für Sie nicht, sodass Ihre dazugehörigen Geschäftsprozesse nicht beeinträchtigt werden. Außerdem wird im Azure-Portal in den Eigenschaften **Einmalkennung per E-Mail für Gastbenutzer** die Option **Einmalkennung per E-Mail für Gastbenutzer ab Oktober 2021 automatisch aktivieren\<date\>** nicht angezeigt. Stattdessen wird die folgende Umschaltfläche (**Ja**/**Nein**) angezeigt:

![Nutzung der Einmalkennung per E-Mail](media/one-time-passcode/enable-email-otp-opted-in.png)

Wenn Sie die Vorschauversion nicht mehr nutzen und das automatische Aktivieren am 1. November 2021 zulassen möchten, können Sie die Standardeinstellungen wiederherstellen. Verwenden Sie dazu in der Microsoft Graph-API den [Ressourcentyp „emailAuthenticationMethodConfiguration“](/graph/api/resources/emailauthenticationmethodconfiguration). Nachdem Sie die Standardeinstellungen wiederhergestellt haben, stehen unter **Einmalkennung per E-Mail für Gastbenutzer** die folgenden Optionen zur Verfügung:

![Aktivieren der Einmalkennung per E-Mail für Nutzer der Vorschauversion](media/one-time-passcode/email-otp-options.png)

- **Einmalkennung per E-Mail für Gastnutzer automatisch aktivieren ab \<date\>** . (Standardeinstellung): Wenn das Feature „Einmalkennung per E-Mail“ für Ihren Mandanten noch nicht aktiviert ist, wird es ab 1. November 2021 automatisch aktiviert. Wenn die Funktion zu diesem Zeitpunkt aktiviert werden soll, ist keine weitere Aktion erforderlich. Wenn Sie die Funktion bereits aktiviert oder deaktiviert haben, ist diese Option nicht verfügbar.

- **Einmalkennung per E-Mail für Gastbenutzer sofort aktivieren**. Aktiviert die Funktion „Einmalkennung per E-Mail“ für Ihren Mandanten.

- **Einmalkennung per E-Mail für Gastbenutzer deaktivieren**. Deaktiviert das Feature „Einmalkennung per E-Mail“ für Ihren Mandanten und verhindert, dass das Feature am 1. November 2021 aktiviert wird.

## <a name="note-for-azure-us-government-customers"></a>Hinweis für Azure US Government-Kunden

Das Feature „Einmalkennung per E-Mail“ ist in der Azure US Government-Cloud standardmäßig deaktiviert. Ihre Partner können sich erst anmelden, wenn dieses Feature aktiviert ist. Im Gegensatz zur öffentlichen Azure-Cloud unterstützt die Azure US Government-Cloud nicht das Einlösen von Einladungen über Azure Active Directory-Self-Service-Konten.

 ![Einmalkennung per E-Mail deaktiviert](media/one-time-passcode/enable-email-otp-disabled.png)

So aktivieren Sie das Feature „Einmalkennung per E-Mail“ in der Azure US Government-Cloud:

1. Melden Sie sich als globaler Azure AD-Administrator im [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
3. Wählen Sie **Organisationsbeziehungen** > **Alle Identitätsanbieter** aus.

   > [!NOTE]
   > - Sollte die Option **Organisationsbeziehungen** nicht angezeigt werden, suchen Sie über die Suchleiste im oberen Bereich nach „External Identities“.

4. Wählen Sie **Einmalkennung per E-Mail** und anschließend **Ja** aus.
5. Wählen Sie **Speichern** aus.

Weitere Informationen zu aktuellen Einschränkungen finden Sie unter [Microsoft Azure Cloud for US Government (Azure-Cloud für US-Behörden)](current-limitations.md#azure-us-government-clouds).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Warum wird in meinen Einstellungen zur Einmalkennung per E-Mail weiterhin die Option „Einmalkennung für E-Mail für Gastbenutzer ab Oktober 2021 automatisch aktivieren“ ausgewählt?**

Aufgrund der Bereitstellungszeitpläne wird am 1. November 2021 damit begonnen, die Einmalkennung per E-Mail standardmäßig global zu aktivieren. Bis dahin kann in den Einstellungen zur Einmalkennung per E-Mail weiterhin die Option „Einmalkennung für E-Mail für Gastbenutzer ab Oktober 2021 automatisch aktivieren“ ausgewählt sein.

**Was geschieht mit meinen vorhandenen Gastbenutzern/-benutzerinnen, wenn ich die einmalige E-Mail-Kennung aktiviere?**

Ihre vorhandenen Gastbenutzer*innen sind nicht betroffen, wenn Sie eine einmal gesendete E-Mail-Kennung aktivieren, da Ihre vorhandenen Benutzer*innen den Zeitpunkt der Einlösung bereits hinter sich haben. Das Aktivieren der einmaligen E-Mail-Kennung wirkt sich nur auf zukünftige Einlösungsaktivitäten aus, bei denen neue Gastbenutzer*innen Einlösungsaktivitäten in den Mandanten durchführen.

**Wie erleben Gastbenutzer*innen den globalen Rollout?**

Während des globalen Rollouts hängt das Benutzererlebnis der Gastbenutzer*innen von der Konfiguration Ihrer Einmalkennung per E-Mail und dem Szenario der Gastbenutzer*innen ab. 

Bevor die Änderung Ihre Region erreicht, erleben Gastbenutzer*innen folgendes Verhalten.

- Mit aktivierter Einmalkennung per E-Mail:

  - Wenn Gastbenutzer*innen über ein vorhandenes, nicht verwaltetes Azure AD-Konto verfügen, melden sie sich weiterhin mit dem nicht verwalteten Azure AD-Konto an.
  - Wenn Gastbenutzer*innen zuvor eine Einladung zu Ihrem Mandanten mit einem nicht verwalteten Azure AD-Konto eingelöst haben, Sie ihren Einlösungsstatus zurücksetzen und sie erneut einladen, melden sie sich weiterhin mit dem nicht verwalteten Azure AD-Konto an.
  - Wenn Gastbenutzer*innen nicht über ein nicht verwaltetes Azure AD-Konto verfügen, lösen sie die Einladung mit der Authentifizierung für die Einmalkennung per E-Mail ein.

- Mit deaktivierter Einmalkennung per E-Mail:

  - Wenn Gastbenutzer*innen über ein vorhandenes, nicht verwaltetes Azure AD-Konto verfügen, melden sie sich weiterhin mit dem nicht verwalteten Azure AD-Konto an.
  - Wenn Gastbenutzer*innen zuvor eine Einladung zu Ihrem Mandanten mit einem nicht verwalteten Azure AD-Konto eingelöst haben, Sie ihren Einlösungsstatus zurücksetzen und sie erneut einladen, melden sie sich weiterhin mit dem nicht verwalteten Azure AD-Konto an.
  - Wenn Gastbenutzer*innen kein nicht verwaltetes Azure AD-Konto besitzt, lösen sie die Einladung mithilfe eines nicht verwalteten Azure AD-Kontos ein, doch möglicherweise kommt es zu einem Anmeldefehler, wenn sie zuvor nicht zu dem Azure-Portal hinzugefügt wurden und die Einlösungsaktivität über einen direkten Anwendungslink durchführen.

Nachdem die Änderung Ihre Region erreicht, erleben Gastbenutzer*innen folgendes Verhalten.

- Mit aktivierter Einmalkennung per E-Mail:

  - Wenn Gastbenutzer*innen über ein vorhandenes, nicht verwaltetes Azure AD-Konto verfügen, melden sie sich weiterhin mit dem nicht verwalteten Azure AD-Konto an.
  - Wenn Gastbenutzer*innen zuvor eine Einladung zu Ihrem Mandanten mithilfe eines nicht verwalteten Azure AD-Kontos eingelöst haben, Sie ihren Einlösungsstatus zurücksetzen und sie erneut einladen, verwenden sie die Einmalkennung per E-Mail, um die Einladung einzulösen und sich in Zukunft anzumelden.
  - Wenn Gastbenutzer*innen nicht über ein nicht verwaltetes Azure AD-Konto verfügen, nutzen sie die Einmalkennung per E-Mail, um die Einladung einzulösen und sich in Zukunft anzumelden.

- Mit deaktivierter Einmalkennung per E-Mail:

  - Wenn Gastbenutzer*innen über ein vorhandenes, nicht verwaltetes Azure AD-Konto verfügen, melden sie sich weiterhin mit dem nicht verwalteten Azure AD-Konto an.
  - Wenn Gastbenutzer*innen zuvor eine Einladung zu Ihrem Mandanten mithilfe eines nicht verwalteten Azure AD-Kontos eingelöst haben, Sie ihren Einlösungsstatus zurücksetzen und sie erneut einladen, verwenden sie ein Microsoft-Konto, um die Einladung einzulösen und sich dann anzumelden.
  - Wenn Gastbenutzer*innen nicht über ein nicht verwaltetes Azure AD-Konto verfügen, nutzen sie ein Microsoft-Konto, um die Einladung einzulösen und sich in Zukunft anzumelden.

Weitere Informationen zu den verschiedenen Möglichkeiten, die Einladung einzulösen, finden Sie unter [B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md).

**Bedeutet dies, die Self-Service-Registrierung ist nicht mehr möglich?**

Es ist einfach, die [Self-Service-Registrierung im Kontext von External Identities](self-service-sign-up-overview.md) mit der Self-Service-Registrierung für E-Mail-verifizierte Benutzer zu verwechseln, aber es handelt sich um zwei verschiedene Features. Das Feature, das abgeschafft wird, ist die [Self-Service-Registrierung für E-Mail-verifizierte Benutzer](../enterprise-users/directory-self-service-signup.md), was dazu führt, dass Ihre Gastbenutzer*innen ein nicht verwaltetes Azure AD-Konto erstellen. Die Self-Service-Registrierung für External Identities ist jedoch weiterhin verfügbar, was dazu führt, dass sich Ihre Gastbenutzer*innen bei Ihrer Organisation mit einer [Vielzahl von Identitätsanbietern](identity-providers.md) registrieren.  

**Was empfiehlt Microsoft für vorhandene Microsoft-Konten (MSA)?**

Wenn die Möglichkeit zum Deaktivieren des Microsoft-Kontos in den Einstellungen für Identitätsanbieter (derzeit nicht verfügbar) unterstützt wird, wird dringend empfohlen, das Microsoft-Konto zu deaktivieren und die Einmalkennung per E-Mail zu aktivieren. Anschließend sollten Sie den Einlösungsstatus vorhandener Gastbenutzer*innen mit Microsoft-Konten zurücksetzen, sodass sie die Einladung mit der Authentifizierung der Einmalkennung per E-Mail erneut einlösen und die Einmalkennung per E-Mail in Zukunft zur Anmeldung nutzen können.

**Betrifft diese Änderung die Integration von SharePoint und OneDrive in Azure AD B2B?**

Nein, der globale Rollout, der standardmäßig die Einmalkennung per E-Mail aktiviert und am 1. November 2021 beginnt, betrifft die Integration von SharePoint und OneDrive in Azure AD B2B nicht. Informationen zum Aktivieren der Integration, sodass die Zusammenarbeit bei SharePoint und OneDrive B2B-Funktionen verwendet, oder zum Deaktivieren der Integration finden Sie unter [Integration mit SharePoint und OneDrive in Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration).
