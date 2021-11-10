---
title: 'Azure Active Directory: Einlösen der Einladung in B2B Collaboration'
description: Beschreibt das Einlösen von Einladungen in Azure AD B2B-Zusammenarbeit für Endbenutzer (einschließlich der Zustimmung zu Datenschutzrichtlinien).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3d20d621b648fb84f2cedee08f206d6eb257f51
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227714"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen

Dieser Artikel beschreibt die Möglichkeiten, wie Gastbenutzer auf Ihre Ressourcen zugreifen können, und den Einwilligungsprozess, auf den sie stoßen werden. Wenn Sie eine Einladungs-E-Mail an den Gast senden, enthält die Einladung einen Link, den der Gast einlösen kann, um Zugriff auf Ihre App oder Ihr Portal zu erhalten. Die Einladungs-E-Mail ist nur eine der Möglichkeiten, wie Gäste Zugriff auf Ihre Ressourcen erhalten können. Alternativ können Sie Ihrem Verzeichnis Gäste hinzufügen und ihnen einen direkten Link zu dem Portal oder der App zur Verfügung stellen, das bzw. die Sie freigeben möchten. Unabhängig von der verwendeten Methode werden Gäste schrittweise durch einen erstmaligen Einwilligungsprozess geführt. Dieser Prozess stellt sicher, dass Ihre Gäste den Datenschutzbestimmungen zustimmen und alle [Nutzungsbedingungen](../conditional-access/terms-of-use.md) akzeptieren, die Sie eingerichtet haben.

Wenn Sie Ihrem Verzeichnis einen Gastbenutzer hinzufügen, hat das Gastbenutzerkonto einen Zustimmungsstatus (in PowerShell einsehbar), der zunächst auf **PendingAcceptance** festgelegt ist. Diese Einstellung bleibt bestehen, bis der Gast Ihre Einladung annimmt und Ihrer Datenschutzerklärung und Ihren Nutzungsbedingungen zustimmt. Danach ändert sich der Zustimmungsstatus in **Accepted** (Akzeptiert), und die Zustimmungsseiten werden dem Gast nicht mehr angezeigt.

> [!IMPORTANT]
> - Ab dem **12. Juli 2021** gilt Folgendes: Wenn Azure AD B2B-Kunden neue Google-Integrationen für die Self-Service-Registrierung für ihre benutzerdefinierten oder branchenspezifischen Anwendungen einrichten, funktioniert die Authentifizierung mit Google-Identitäten nur, wenn Authentifizierungen in Systemwebansichten verlagert werden. [Weitere Informationen](google-federation.md#deprecation-of-web-view-sign-in-support)
> - Ab dem **30. September 2021** wird Google die [Unterstützung für die Anmeldung in der eingebetteten Webansicht einstellen](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Wenn Ihre Apps Benutzer mit einer eingebetteter Webansicht authentifizieren und Sie den Google-Verbund mit [ Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) oder Azure AD B2B für [externe Benutzereinladungen](google-federation.md) oder die [Self-Service-Registrierung](identity-providers.md) verwenden, werden sich Google Gmail-Benutzer nicht authentifizieren können. [Weitere Informationen](google-federation.md#deprecation-of-web-view-sign-in-support)
> - **Ab 1. November 2021** wird mit der Aktivierung des Features „Einmalkennung per E-Mail“ für alle bestehenden Mandanten begonnen. Bei neuen Mandanten wird es standardmäßig aktiviert. Im Rahmen dieser Änderung erstellt Microsoft während der Einlösung von Einladungen für die B2B-Zusammenarbeit keine neuen, nicht verwalteten („viralen“) Azure AD-Konten und -Mandanten mehr. Um Unterbrechungen während Feiertagen und Bereitstellungssperren zu minimieren, findet das Rollout der Änderungen für die meisten Mandanten im Januar 2022 statt. Wir aktivieren die Funktion „Einmalkennung per E-Mail“, da sie eine nahtlose alternative Authentifizierungsmethode für Ihre Gastbenutzer bietet. Wenn Sie jedoch nicht möchten, dass dieses Feature automatisch aktiviert wird, können Sie es [deaktivieren](one-time-passcode.md#disable-email-one-time-passcode). 

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Einlösen und Anmelden über einen gemeinsamen Endpunkt

Gastbenutzer können sich jetzt über einen gemeinsamen Endpunkt (URL, z. B. `https://myapps.microsoft.com`) bei Ihren mehrinstanzenfähigen Anwendungen oder Microsoft-Erstanbieter-Apps anmelden. Bisher wäre ein Gastbenutzer über eine gemeinsame URL zur Authentifizierung an seinen Basismandanten und nicht an Ihren Ressourcenmandanten geleitet worden, sodass ein mandantenspezifischer Link erforderlich wurde (z. B. `https://myapps.microsoft.com/?tenantid=<tenant id>`). Jetzt kann ein Gastbenutzer zur gemeinsamen URL der Anwendung wechseln, dann **Anmeldeoptionen** und danach **Bei einer Organisation anmelden** auswählen. Der Benutzer gibt dann den Namen Ihrer Organisation ein.

![Anmelden über gemeinsamen Endpunkt](media/redemption-experience/common-endpoint-flow-small.png)

Der Benutzer wird dann an den Endpunkt Ihres Mandanten umgeleitet, wo er sich entweder mit seiner E-Mail-Adresse anmelden oder einen von Ihnen konfigurierten Identitätsanbieter auswählen kann.

## <a name="redemption-through-a-direct-link"></a>Einlösung über einen direkten Link

Als Alternative zur Einladungs-E-Mail oder zur gemeinsamen URL einer Anwendung können Sie einem Gast einen direkten Link zu Ihrer App oder Ihrem Portal zur Verfügung stellen. Zunächst müssen Sie den Gastbenutzer über das [Azure-Portal](./b2b-quickstart-add-guest-users-portal.md) oder [PowerShell](./b2b-quickstart-invite-powershell.md) zu Ihrem Verzeichnis hinzufügen. Dann können Sie eine der [anpassbaren Möglichkeiten zum Bereitstellen von Anwendungen für Benutzer](../manage-apps/end-user-experiences.md) verwenden, einschließlich direkter Anmeldelinks. Wenn ein Gast anstelle der Einladungs-E-Mail einen direkten Link verwendet, wird er dennoch schrittweise durch die Benutzeroberfläche für die erste Zustimmung geführt.

> [!NOTE]
> Ein direkter Link ist mandantenspezifisch. Mit anderen Worten: Er enthält eine Mandanten-ID oder eine überprüfte Domäne, damit sich der Gast bei Ihrem Mandanten, in dem sich die freigegebene App befindet, authentifizieren kann. Hier sind einige Beispiele für direkte Links mit Mandantenkontext:
 > - App-Zugriffsbereich: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - App-Zugriffsbereich für eine überprüfte Domäne: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure-Portal: `https://portal.azure.com/<tenant id>`
 > - Einzelne App: Lesen Sie, wie ein [direkter Anmeldelink](../manage-apps/end-user-experiences.md#direct-sign-on-links) verwendet wird.

Es gibt einige Fälle, in denen die Einladungs-E-Mail über einen direkten Link empfohlen wird. Wenn diese Sonderfälle für Ihre Organisation relevant sind, empfiehlt es sich, für die Einladung von Benutzern eine Methode zu verwenden, bei der weiterhin eine Einladungs-E-Mail gesendet wird:
 - Der Benutzer verfügt nicht über ein Azure AD-Konto, ein MSA oder ein E-Mail-Konto in einer Verbundorganisation. Sofern Sie nicht die Funktion für einen einmaligen Passcode verwenden, muss der Gast die Einladungs-E-Mail einlösen, um durch die Schritte zur Erstellung eines MSA geführt zu werden.
 - Manchmal verfügt das eingeladene Benutzerobjekt aufgrund eines Konflikts mit einem Kontaktobjekt (beispielsweise ein Outlook-Kontaktobjekt) möglicherweise über keine E-Mail-Adresse. In diesem Fall muss der Benutzer auf die Einlösungs-URL in der Einladungs-E-Mail klicken.
 - Der Benutzer meldet sich möglicherweise mit einem Alias für die eingeladene E-Mail-Adresse an. (Ein Alias ist eine zusätzliche E-Mail-Adresse, die einem E-Mail-Konto zugeordnet ist.) In diesem Fall muss der Benutzer auf die Einlösungs-URL in der Einladungs-E-Mail klicken.

## <a name="redemption-through-the-invitation-email"></a>Einlösung über die Einladungs-E-Mail

Wenn Sie Ihrem Verzeichnis [über das Azure-Portal](./b2b-quickstart-add-guest-users-portal.md) einen Gastbenutzer hinzufügen, wird dabei eine Einladungs-E-Mail an den Gast gesendet. Sie können auch Einladungs-E-Mails senden, wenn Sie Ihrem Verzeichnis [mit PowerShell](./b2b-quickstart-invite-powershell.md) Gastbenutzer hinzufügen. Hier ist eine Beschreibung der Erfahrungen des Gasts, wenn er den Link in der E-Mail einlöst.

1. Der Gast erhält eine [Einladungs-E-Mail](./invitation-email-elements.md), die über **Microsoft-Einladungen** gesendet wird.
2. Der Gast wählt **Einladung annehmen** in der E-Mail aus.
3. Der Gast verwendet seine eigenen Anmeldeinformationen, um sich bei Ihrem Verzeichnis anzumelden. Wenn der Gast kein Konto hat, das über einen Verbund in Ihrem Verzeichnis verwendet werden kann, und die Funktion für die [E-Mail-Einmalkennung](./one-time-passcode.md) (One-Time Passcode, OTP) nicht aktiviert ist, wird der Gast aufgefordert, ein persönliches [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) oder ein [Azure AD-Self-Service-Konto](../enterprise-users/directory-self-service-signup.md) zu erstellen. Ausführliche Informationen finden Sie unter [Flow beim Einlösen der Einladung](#invitation-redemption-flow).
4. Der Gast wird schrittweise durch die im Folgenden beschriebene [Zustimmungsbenutzeroberfläche](#consent-experience-for-the-guest) geführt.

## <a name="redemption-limitation-with-conflicting-contact-object"></a>Einlösungseinschränkung mit in Konflikt kommendem Contact-Objekt
Manchmal kann die E-Mail des eingeladenen externen Gastbenutzers mit einem vorhandenen [Contact-Objekt](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true) in Konflikt treten, was dazu führt, dass der Gastbenutzer ohne proxyAddress erstellt wird. Dies ist eine bekannte Einschränkung, die verhindert, dass Gastbenutzer Folgendes tun: 
- Einlösen einer Einladung über einen direkten Link mit [saml/WS-Fed IdP,](./direct-federation.md) [Microsoft-Konten,](./microsoft-account.md) [Google-Verbund](./google-federation.md)oder [E-Mail-One-Time Kennungskonten](./one-time-passcode.md). 
- Einlösen einer Einladung über einen Link zum Einlösen von Einladungs-E-Mails mit [saml/WS-Fed IdP](./direct-federation.md) und [E-Mail One-Time Kennungskonten](./one-time-passcode.md).
- Anmelden bei einer Anwendung nach dem Einlösen mit [saml/WS-Fed IdP](./direct-federation.md) und [Google-Verbundkonten](./google-federation.md).

Führen Sie die folgenden Schritte aus, um die Blockierung von Benutzern aufzuheben, die eine Einladung aufgrund eines in Konflikt stehenden [Contact-Objekts](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true) nicht einlösen können:
1. Löschen Sie das in Konflikt stehende Contact-Objekt.
2. Löschen Sie den Gastbenutzer im Azure-Portal (die Eigenschaft „Einladung akzeptiert“ des Benutzers sollte den Status Ausstehend haben).
3. Laden Sie den Gastbenutzer erneut ein.
4. Warten Sie, bis der Benutzer die Einladung eingelöst hat.
5. Fügen Sie die Kontakt-E-Mail des Benutzers wieder zu Exchange hinzu, und fügen Sie alle DLs hinzu, zu denen er gehören sollte.

## <a name="invitation-redemption-flow"></a>Flow beim Einlösen der Einladung

Wenn ein Benutzer in einer [Einladungs-E-Mail](invitation-email-elements.md) auf den Link **Einladung annehmen** klickt, löst Azure AD die Einladung automatisch ein, und zwar auf Grundlage des nachstehend gezeigten Einlösungsflows:

![Screenshot mit Diagramm des Einlösungsflows](media/redemption-experience/invitation-redemption-flow.png)

**Wenn der Benutzerprinzipalname (User Principal Name, UPN) des Benutzers mit einem vorhandenen Azure AD-Konto und einem persönlichen MSA-Konto übereinstimmt, wird der Benutzer aufgefordert, das Konto für das Einlösen auszuwählen.*

1. Azure AD führt eine benutzerbasierte Ermittlung durch, um festzustellen, ob der Benutzer in einem [bestehenden Azure AD-Mandanten](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal) vorhanden ist.

2. Wenn ein Administrator [SAML/WS-Fed IdP-Verbund](./direct-federation.md) aktiviert hat, prüft Azure AD, ob das Domänensuffix des Benutzers mit der Domäne eines konfigurierten SAML/WS-Fed-Identitätsanbieters übereinstimmt und leitet den Benutzer zum vorkonfigurierten Identitätsanbieter um.

3. Wenn ein Administrator [Verbund mit Google](./google-federation.md) aktiviert hat, prüft Azure AD, ob das Domänensuffix des Benutzers gmail.com oder googlemail.com ist und leitet den Benutzer zu Google um.

4. Beim Einlösungsprozess wird überprüft, ob der Benutzer über ein vorhandenes persönliches [Microsoft-Konto (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) für Just-In-Time-Einlösungen (JIT), aber nicht für die Einlösung über Einladungs-E-Mail-Links verfügt. Verfügt der Benutzer bereits über ein vorhandenes MSA, meldet er sich mit dem vorhandenen MSA an.

5. Sobald das **Basisverzeichnis** des Benutzers bestimmt wurde, wird der Benutzer zur Anmeldung an den entsprechenden Identitätsanbieter weitergeleitet.  

6. Wenn in den Schritten 1 bis 4 kein Basisverzeichnis für den eingeladenen Benutzer gefunden wird, ermittelt Azure AD, ob der einladende Mandant das Feature [Einmalkennung per E-Mail für Gastbenutzer](./one-time-passcode.md) aktiviert hat.

7. Wenn [Einmalkennung per E-Mail für Gastbenutzer](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) aktiviert ist, wird in der Einladungs-E-Mail eine Kennung an den Benutzer gesendet. Der Benutzer ruft diese Kennung ab und gibt sie auf der Anmeldeseite von Azure AD ein.

8. Wenn „Einmalkennung per E-Mail für Gastbenutzer“ deaktiviert ist, überprüft Azure AD, ob das Domänensuffix zu einem Consumerkonto gehört. In diesem Fall wird der Benutzer aufgefordert, ein persönliches [Microsoft-Konto](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) zu erstellen. Andernfalls wird der Benutzer aufgefordert, ein [Azure AD-Self-Service-Konto](../enterprise-users/directory-self-service-signup.md) zu erstellen.

9. Azure AD versucht, ein [Azure AD-Self-Service-Konto](../enterprise-users/directory-self-service-signup.md) zu erstellen, indem der Zugriff auf die E-Mail-Adresse überprüft wird. Die Überprüfung des Kontos erfolgt, indem ein Code an die E-Mail-Adresse gesendet wird, den der Benutzer abruft und an Azure AD sendet. Wenn der Mandant des eingeladenen Benutzers jedoch zu einem Verbund gehört oder das Feld AllowEmailVerifiedUsers im Mandanten des eingeladenen Benutzers auf FALSE festgelegt ist, kann der Benutzer die Einlösung nicht abschließen, wodurch der Flow mit einem Fehler endet. Weitere Informationen finden Sie unter [Problembehandlung für die Azure Active Directory B2B Collaboration](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Der Benutzer wird aufgefordert, ein persönliches [Microsoft-Konto (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) zu erstellen.

11. Nach Authentifizierung beim richtigen Identitätsanbieter wird der Benutzer zu Azure AD umgeleitet, um den [Einwilligungsvorgang](#consent-experience-for-the-guest) abzuschließen.  

Für Just-in-Time-Einlösungen (JIT), bei denen die Einlösung über einen Link zur Mandantenanwendung erfolgt, sind die Schritte 8 bis 10 nicht verfügbar. Wenn ein Benutzer Schritt 6 erreicht und das Feature „Einmalkennung per E-Mail“ nicht aktiviert ist, erhält der Benutzer eine Fehlermeldung und kann die Einladung nicht einlösen. Um diesen Fehler zu vermeiden, müssen Administratoren entweder [Einmalkennung per E-Mail aktivieren](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) oder sicherstellen, dass der Benutzer auf einen Einladungslink klickt.

## <a name="consent-experience-for-the-guest"></a>Einwilligungsbenutzeroberfläche für den Gast

Wenn sich ein Gast zum ersten Mal anmeldet, um auf Ressourcen in einer Partnerorganisation zuzugreifen, wird er schrittweise durch die folgenden Seiten geführt. 

1. Der Gast überprüft die Seite **Berechtigungen überprüfen**, die die Datenschutzerklärung der einladenden Organisation beschreibt. Der Benutzer muss der Nutzung seiner Informationen gemäß den Datenschutzrichtlinien der Organisation **zustimmen**, um fortfahren zu können.

   ![Screenshot: Seite „Berechtigungen überprüfen“](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Informationen dazu, wie Sie als Mandantenadministrator einen Link zu den Datenschutzbestimmungen Ihrer Organisation einrichten, finden Sie unter [Vorgehensweise: Hinzufügen der Datenschutzinformationen mit Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Wenn Nutzungsbedingungen konfiguriert sind, öffnet und überprüft der Gast die Nutzungsbedingungen und wählt dann **Ich stimme zu** aus. 

   ![Screenshot mit neuen Nutzungsbedingungen](media/redemption-experience/terms-of-use-accept.png) 

   Sie können [Nutzungsbedingungen](../conditional-access/terms-of-use.md) unter **Externe Identitäten** > **Nutzungsbedingungen** konfigurieren.

3. Sofern nicht anders angegeben, wird der Gast zum Zugriffsbereich für Apps weitergeleitet, der die Anwendungen auflistet, auf die der Gast zugreifen kann.

   ![Screenshot: Zugriffsbereich für Apps](media/redemption-experience/myapps.png) 

> [!NOTE]
> Die Zustimmungsoberfläche wird erst nach der Anmeldung des Benutzers angezeigt und nicht vorher. Es gibt einige Szenarien, in denen die Zustimmungsoberfläche für den Benutzer nicht angezeigt wird. Beispiele:
> - Der Benutzer hat den Bedingungen bereits zugestimmt.
> - Der Administrator [erteilt eine mandantenweite Administratoreinwilligung für eine Anwendung](../manage-apps/grant-admin-consent.md).

In Ihrem Verzeichnis ändert sich der Wert **Invitation accepted** (Einladung angenommen) des Gasts in **Yes** (Ja). Wenn ein MSA erstellt wurde, wird als **Quelle** des Gasts **Microsoft-Konto** angezeigt. Weitere Informationen zu den Eigenschaften des Gastbenutzerkontos finden Sie unter [Eigenschaften eines Benutzers von Azure AD B2B-Zusammenarbeit](user-properties.md). Wenn beim Zugreifen auf eine Anwendung eine Fehlermeldung angezeigt wird, die besagt, dass eine Administratoreinwilligung erforderlich ist, lesen Sie [Erteilen der Administratoreinwilligung für Apps](../develop/v2-admin-consent.md).

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Add Azure Active Directory B2B collaboration users in the Azure portal](add-users-administrator.md) (Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal)
- [How do information workers add B2B collaboration users to Azure Active Directory?](add-users-information-worker.md) (Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer zu Azure Active Directory hinzu?)
- [Azure Active Directory B2B collaboration API and customization](customize-invitation-api.md#powershell) (Azure Active Directory B2B-Zusammenarbeit: API und Anpassung)
- [Leave an organization as a guest user](leave-the-organization.md) (Verlassen einer Organisation als Gastbenutzer)