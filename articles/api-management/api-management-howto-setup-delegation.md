---
title: Delegieren von Benutzerregistrierung und Produktabonnierung
description: Erfahren Sie, wie Sie die Benutzerregistrierung und Produktabonnements an einen Dritten in Azure API Management delegieren.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2021
ms.author: danlep
ms.openlocfilehash: dbb948e45473bf091cb2d69e82ad2adf5fcfe8a2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058633"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Delegieren von Benutzerregistrierung und Produktabonnierung

Delegierung ermöglicht, dass Ihre Website die Benutzerdaten besitzt und eine benutzerdefinierte Validierung durchführen kann. Mit der Delegierung können Sie Anmeldung/Registrierung und Produktabonnierung von Entwicklern mithilfe Ihrer bestehenden Website verarbeiten, anstatt die integrierte Funktion im Entwicklerportal zu verwenden. 

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegieren der Anmeldung und Registrierung für Entwickler

Um die Anmeldung und Registrierung für Entwickler bei Ihrer vorhandenen Website zu delegieren, erstellen Sie einen speziellen Delegierungsendpunkt für Ihre Website. Diese spezielle Delegierung fungiert als Einstiegspunkt für alle über das API Management-Entwicklerportal initiierte Anmelde-/Registrierungsanforderungen.

Der endgültige Workflow sieht wie folgt aus:

1. Ein Entwickler klickt auf den Anmeldungs- oder Registrierungslink im API Management-Entwicklerportal.
2. Der Browser leitet zum Delegierungsendpunkt um.
3. Der Delegierungsendpunkt wiederum leitet den Benutzer zur Anmeldung/Registrierung um oder zeigt diese an. 
4. Nach erfolgreicher Anmeldung/Registrierung wird der Benutzer wieder an die Stelle im API Management-Entwicklerportal zurückgeleitet, von der er ursprünglich gekommen ist.

### <a name="set-up-api-management-to-route-requests-via-delegation-endpoint"></a>Einrichten von API Management zur Weiterleitung von Anforderungen über einen Delegierungsendpunkt

1. Suchen Sie im Azure-Portal in Ihrer API Management-Ressource nach dem **Entwicklerportal**.
2. Klicken Sie auf das Element **Delegierung**. 
3. Klicken Sie auf das Kontrollkästchen, um **Anmeldung und Registrierung delegieren** zu aktivieren.

:::image type="content" source="media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png" alt-text="Übersicht über Delegierungen":::

4. Legen Sie die URL für Ihren speziellen Delegierungsendpunkt fest, und geben Sie sie in das Feld **Delegierungsendpunkt-URL** ein. 
5. Führen Sie im Feld **Schlüssel für die Überprüfung der Delegierung** eine der folgenden Optionen durch:
    * Geben Sie ein Geheimnis ein, das zum Berechnen einer Signatur verwendet wird, die präsentiert wird, um zu verifizieren, dass die Anforderung von API Management stammt. 
    * Klicken Sie auf die Schaltfläche **Generieren**, damit API Management nach einen zufälligen Schlüssel für Sie generiert.
6. Klicken Sie auf **Speichern**.

### <a name="create-your-delegation-endpoint"></a>Erstellen Ihres Delegierungsendpunkts

>[!NOTE]
> Das folgende Verfahren bietet Ihnen zwar Beispiele für den Vorgang **SignIn** (Anmeldung), doch Sie können die Kontenverwaltung mit jedem der verfügbaren Vorgänge anhand der folgenden Schritte durchführen. 

Empfohlene Schritte zum Erstellen eines neuen Delegierungsendpunkts zur Implementierung in Ihren Standort:

1. Empfang einer Anforderung in der folgenden Form:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL der Herkunftsseite}&salt={Zeichenfolge}&sig={Zeichenfolge}*
   
    Abfrageparameter für den Anmeldungs-/Registrierungsfall:

   | Parameter | Beschreibung |
   | --------- | ----------- |
   | **operation** | Identifiziert den Typ der Delegierungsanforderung. Verfügbare Vorgänge: **SignIn** (Anmeldung), **ChangePassword** (Kennwort ändern), **ChangeProfile** (Profil ändern), **CloseAccount** (Konto schließen) und **SignOut** (Abmelden). |
   | **returnUrl** | Die URL der Seite, auf der der Benutzer auf einen Anmeldungs- oder Registrierungslink geklickt hat. |
   | **Salt** | Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheitshash berechnet wird. |
   | **sig** | Ein berechneter Sicherheitshash, der für den Vergleich mit dem von Ihnen generierten Hash verwendet wird. |
   
3. Überprüfen, ob die Anforderung von Azure API Management stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen).
   
   * Berechnen Sie einen HMAC-SHA512-Hash für eine Zeichenfolge, basierend auf den Abfrageparametern **returnUrl** und **salt**. Weitere Informationen finden Sie in unserem [Beispielcode].
     
     ```
         HMAC(salt + '\n' + returnUrl)
     ```
   * Vergleichen Sie den generierten Hash mit dem Wert des **sig**-Abfrageparameters. Wenn die beiden Hashes übereinstimmen, fahren Sie mit dem nächsten Schritt fort. Lehnen Sie die Anforderung andernfalls ab.
4. Verifizieren, dass Sie eine Anforderung für eine Anmeldung/Registrierung empfangen.
    * Der Abfrageparameter **operation** (Vorgang) wird auf „**SignIn**“ festgelegt.
5. Zeigen Sie dem Benutzer die Benutzeroberfläche für die Anmeldung/Registrierung an.
    * Wenn sich der Benutzer registriert, erstellen Sie ein entsprechendes Konto für ihn in API Management. 
      * [Erstellen Sie einen Benutzer] mit der REST-API für API Management. 
      * Legen Sie die Benutzer-ID entweder auf denselben Wert wie in Ihrem Benutzerspeicher fest oder auf eine neue, leicht verfolgbare ID.
6. Nach erfolgreicher Authentifizierung des Benutzers:
   
   * [Fordern Sie ein Token für gemeinsamen Zugriff] über die REST-API von API Management an.
   * Fügen Sie der SSO-URL, die Sie aus dem obigen API-Aufruf erhalten haben, einen **returnUrl**-Abfrageparameter an. Zum Beispiel:
     
     > `https://contoso.developer.azure-api.net/signin-sso?token=<URL-encoded token>&returnUrl=%2Freturn%2Furl` 
     
   * Leiten Sie den Benutzer an die oben erzeugte URL um.

>[!NOTE]
> Übergeben Sie für Kontoverwaltungsvorgänge (**ChangePassword**, **ChangeProfile** und **CloseAccount**) die folgenden Abfrageparameter:
> 
>    | Parameter | Beschreibung |
>    | --------- | ----------- |
>    | **operation** | Identifiziert den Typ der Delegierungsanforderung. |
>    | **userId** | Die Benutzer-ID des Kontos, das Sie verwalten möchten. |
>    | **Salt** | Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheitshash berechnet wird. |
>    | **sig** | Ein berechneter Sicherheitshash, der für den Vergleich mit dem von Ihnen generierten Hash verwendet wird. |

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegieren der Produktabonnierung

Die Delegierung von Produktabonnements funktioniert genauso wie die Delegierung der Benutzeranmeldung/-registrierung. Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler wählt ein Produkt im API Management-Entwicklerportal aus und klickt auf die Schaltfläche **Abonnieren**.
2. Der Browser leitet zum Delegierungsendpunkt um.
3. Der Delegierungsendpunkt führt die erforderlichen Schritte für die Produktabonnierung aus, die Sie entwerfen. Diese können umfassen: 
   * Umleiten an eine andere Seite zur Anforderung von Abrechnungsinformationen.
   * Stellen weiterer Fragen.
   * Speichern der Informationen ohne Erfordernis einer Benutzeraktion.

### <a name="enable-the-api-management-functionality"></a>Aktivieren der API Management-Funktionalität

Klicken Sie auf der Seite **Delegierung** auf **Produktabonnement delegieren**.

### <a name="create-your-delegation-endpoint"></a>Erstellen Ihres Delegierungsendpunkts

Empfohlene Schritte zum Erstellen eines neuen Delegierungsendpunkts zur Implementierung in Ihren Standort:

1. Empfangen Sie eine Anforderung in der folgenden Form.
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={Vorgang}&productId={zu abonnierendes Produkt}&userId={anfordernder Benutzer}&salt={Zeichenfolge}&sig={Zeichenfolge}*
   >
   
    Abfrageparameter für die Produktabonnierung:

   | Parameter | Beschreibung |
   | --------- | ----------- |
   | **operation** | Identifiziert den Typ der Delegierungsanforderung. Gültige Optionen für die Produktabonnementanforderung sind: <ul><li>**Subscribe**: Eine Anforderung für ein Benutzerabonnement eines Produkts mit der angegebenen ID (siehe unten).</li><li>**Unsubscribe**: Eine Anforderung zur Kündigung eines Benutzerabonnements für ein Produkt.</li><li>**Renew**: Eine Anforderung zur Verlängerung eines Abonnements (weil dieses z. B. demnächst abläuft).</li></ul> |
   | **productId** | Bei *Subscribe* die ID des Produkts, dessen Abonnement der Benutzer angefordert hat. |
   | **subscriptionId** | Bei *Unsubscribe* und *Renew* das Produktabonnement. |
   | **userId** | Bei *Subscribe* die ID des anfordernden Benutzers. |
   | **Salt** | Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheitshash berechnet wird. |
   | **sig** | Ein berechneter Sicherheitshash, der für den Vergleich mit dem von Ihnen generierten Hash verwendet wird. |

2. Überprüfen, ob die Anfrage von Azure API Management stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)
   
   * Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge aus den Abfrageparametern **productId**, **userId** und **salt**:
     ```
     HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     ```
   * Vergleichen Sie den generierten Hash mit dem Wert des **sig**-Abfrageparameters. Wenn die beiden Hashes übereinstimmen, fahren Sie mit dem nächsten Schritt fort. Lehnen Sie die Anforderung andernfalls ab.
3. Verarbeiten Sie das Produktabonnement basierend auf dem im Parameter **operation** angeforderten Vorgangstyp (z. B. Abrechnung, weitere Fragen usw.).
4. Nach erfolgreichem Abonnement des Produkts durch den Benutzer auf Ihrer Seite abonnieren Sie das Produkt in API Management für den Benutzer, indem Sie die [REST-API für Abonnements aufrufen].

## <a name="example-code"></a><a name="delegate-example-code"> </a> Beispielcode

Die Codebeispiele zeigen Folgendes:

* Abrufen des *Validierungsschlüssels für Delegierungen*, der auf dem Bildschirm **Delegierung** im Azure-Portal festgelegt wird.
* Erstellen Sie einen HMAC, mit dem die Signatur überprüft und dadurch die Gültigkeit der übergebenen „returnUrl“ nachgewiesen wird.

Mit kleineren Änderungen können Sie denselben Code für die **productId** und **userID** verwenden.

### <a name="c-code-to-generate-hash-of-returnurl"></a>C#-Code zum Generieren des Hashwerts von returnUrl

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

### <a name="nodejs-code-to-generate-hash-of-returnurl"></a>NodeJS-Code zum Generieren des Hashwerts von returnUrl

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> Sie müssen Sie [das Entwicklerportal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Delegierungsänderungen wirksam werden.

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zum Entwicklerportal.](api-management-howto-developer-portal.md)
- [Authentifizierung über Azure AD](api-management-howto-aad.md) oder mit [Azure AD B2C](api-management-howto-aad-b2c.md).
- Weitere Fragen zum Entwicklerportal? [Antworten finden Sie in unseren häufig gestellten Fragen](developer-portal-faq.md).

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Anfordern eines SAS-Tokens]: /rest/api/apimanagement/2020-12-01/user/get-shared-access-token
[Erstellen Sie einen Benutzer]: /rest/api/apimanagement/2020-12-01/user/create-or-update
[REST-API für Abonnements]: /rest/api/apimanagement/2020-12-01/subscription/create-or-update
[Next steps]: #next-steps
[Beispielcode]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
