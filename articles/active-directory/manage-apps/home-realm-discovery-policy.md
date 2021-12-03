---
title: Richtlinie zur Startbereichsermittlung
titleSuffix: Azure AD
description: Erfahren Sie, wie Sie die automatische Beschleunigung für eine Anwendung mithilfe der Richtlinie zu Home Realm Discovery steuern können.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: 8ac93a42c4911a6694770ee320f84b88d6ea6aa5
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547444"
---
# <a name="home-realm-discovery-for-an-application"></a>Startbereichsermittlung für eine Anwendung

Home Realm Discovery (HRD) ist der Prozess, der es Azure AD ermöglicht, zu bestimmen, bei welchem Identitätsanbieter ("IdP") sich ein Benutzer zum Zeitpunkt der Anmeldung authentifizieren muss.  Wenn sich ein Benutzer bei einem Azure AD-Mandanten oder auf der allgemeinen Anmeldeseite von Azure AD anmeldet, um auf eine Ressource zuzugreifen, gibt er einen Benutzernamen (UPN) ein. Azure AD verwendet diesen, um zu ermitteln, wo sich der Benutzer anmelden muss.

Der Benutzer wird zu einem der folgenden Identitätsanbieter weitergeleitet, um sich zu authentifizieren:

- Basismandant des Benutzers (dies kann der gleiche Mandant wie die Ressource sein, auf die der Benutzer zugreifen möchte).

- Microsoft-Konto:  Der Benutzer ist ein Gast des Ressourcenmandanten, der ein Consumerkonto für die Authentifizierung verwendet.

- Lokaler Identitätsanbieter, z.B. Active Directory-Verbunddienste (AD FS).

- Anderer Identitätsanbieter, der einen Verbund mit dem Azure AD-Mandanten bildet.

## <a name="auto-acceleration"></a>Automatische Beschleunigung

Einige Unternehmen konfigurieren Domänen in ihrem Azure AD-Tenant für den Verbund mit einem anderen IdP, wie z. B. AD FS, zur Benutzerauthentifizierung.

Wenn sich ein Benutzer bei einer Anwendung anmeldet, wird zunächst eine Azure AD-Anmeldeseite angezeigt. Falls er sich in einer Verbunddomäne befindet, wird er nach Eingabe seines UPN zur Anmeldeseite des Identitätsanbieters für diese Domäne weitergeleitet. Unter bestimmten Umständen können Administratoren Benutzer auf die Anmeldeseite weiterleiten, wenn sie sich bei bestimmten Anwendungen anmelden.

Daher können Benutzer die erste Seite von Azure Active Directory überspringen. Dieser Vorgang wird als „automatische Beschleunigung der Anmeldung“ bezeichnet.

In den Fällen, in denen der Mandant mit einem anderen IdP für die Anmeldung einen Verbund bildet, wird die Benutzeranmeldung durch die automatische Beschleunigung optimiert.  Sie können die automatische Beschleunigung für einzelne Anwendungen konfigurieren.

> [!NOTE]
> Wenn Sie eine Anwendung für die automatische Beschleunigung konfigurieren, können Benutzer keine verwalteten Anmeldeinformationen (beispielsweise FIDO) verwenden, und Gastbenutzer können sich nicht anmelden. Wenn Sie einen Benutzer zur Authentifizierung direkt zu einem föderierten IdP leiten, gibt es für ihn keine Möglichkeit, auf die Anmeldeseite von Azure Active Directory zurückzukehren. Gastbenutzer, die möglicherweise an andere Mandanten oder an einen externen IdP wie ein Microsoft-Konto weitergeleitet werden müssen, können sich nicht bei dieser Anwendung anmelden, weil sie den Schritt „Startbereichsermittlung“ (Home Realm Discovery, HDR) überspringen.

Die automatische Beschleunigung für einen Verbundidentitätsanbieter kann auf drei Arten gesteuert werden:

- Verwenden Sie einen Domänenhinweis für Authentifizierungsanforderungen für eine Anwendung.
- Konfigurieren Sie eine Richtlinie zu Home Realm Discovery, um die [automatische Beschleunigung zu erzwingen]() (configure-authentication-for-federated-users-portal.md)
- Sie können eine Richtlinie zur Startbereichsermittlung konfigurieren, um [Domänenhinweise von bestimmten Anwendungen oder für bestimmte Domänen zu ignorieren](prevent-domain-hints-with-home-realm-discovery.md).

## <a name="domain-hints"></a>Domänenhinweise

Domänenhinweise sind in der Authentifizierungsanforderung einer Anwendung enthaltene Anweisungen. Sie können verwendet werden, um die beschleunigte Anmeldung des Benutzers auf der Anmeldeseite seines Verbundidentitätsanbieters zu ermöglichen. Sie können auch von einer Anwendung für mehrere Mandanten verwendet werden, um den Benutzer beschleunigt direkt zur organisationsspezifischen Azure AD-Anmeldeseite für ihren Mandanten zu leiten.

Die Anwendung „largeapp.com“ könnte es z.B. ihren Kunden ermöglichen, auf die Anwendung über eine benutzerdefinierte URL wie „contoso.largeapp.com“ zuzugreifen. Dabei könnte die App einen Domänenhinweis auf „contoso.com“ in die Authentifizierungsanforderung einbeziehen.

Die Syntax des Domänenhinweises variiert je nach verwendetem Protokoll und wird in der Regel auf folgende Weise in der Anwendung konfiguriert:

- Für Anwendungen, die die **WS-Federation** verwenden: whr=contoso.com in der Abfragezeichenfolge.

- Für Anwendungen, die **SAML** verwenden: Entweder eine SAML-Authentifizierungsanfrage, die einen Domain-Hinweis oder eine Abfragezeichenfolge whr=contoso.com enthält.

- Für Anwendungen, die **Open ID Connect** verwenden: Eine Abfragezeichenfolge domain_hint=contoso.com.

Von Azure AD wird standardmäßig versucht, die Anmeldung an den für eine Domäne konfigurierten Identitätsanbieter umzuleiten, wenn **beide** der folgenden Punkte zutreffen:

- Die Authentifizierungsanforderung der Anwendung enthält einen Domänenhinweis. **Und:**
- Der Mandant gehört einem Verbund mit dieser Domäne an.

Wenn sich der Domänenhinweis nicht auf eine überprüfte Verbunddomäne bezieht, wird er ignoriert.

> [!NOTE]
> Wenn ein Domänenhinweis in einer Authentifizierungsanforderung enthalten ist und [berücksichtigt werden soll](#home-realm-discovery-policy-to-prevent-auto-acceleration), wird die automatische Beschleunigung außer Kraft gesetzt, die in der Richtlinie zur Startbereichsermittlung für die Anwendung festgelegt ist.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Richtlinie zur Startbereichsermittlung für die automatische Beschleunigung

Einige Anwendungen bieten keine Möglichkeit, die von ihnen ausgegebene Authentifizierungsanforderung zu konfigurieren. In diesen Fällen ist es nicht möglich, Domänenhinweise zum Steuern der automatischen Beschleunigung zu verwenden. Die automatische Beschleunigung kann über die [Richtlinie Home Realm Discovery konfiguriert](configure-authentication-for-federated-users-portal.md) werden, um das gleiche Verhalten zu erreichen.

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Richtlinie für die Startbereichsermittlung zur Vermeidung der automatischen Beschleunigung

Einige Microsoft- und SaaS-Anwendungen enthalten automatisch Domänenhinweise. (`https://outlook.com/contoso.com` resultiert beispielsweise in einer Anmeldeanforderung, an die `&domain_hint=contoso.com` angefügt ist.) Dies kann das Rollout von verwalteten Anmeldeinformationen wie FIDO behindern.  Sie können die [Home Realm Discovery Policy to ignore domain hints](prevent-domain-hints-with-home-realm-discovery.md) von bestimmten Anwendungen oder für bestimmte Domänen beim Rollout von verwalteten Anmeldeinformationen zu ignorieren.

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Aktivieren der direkten ROPC-Authentifizierung von Verbundbenutzern für Legacyanwendungen

Die beste Praxis ist, dass Anwendungen Azure AD-Bibliotheken und interaktive Anmeldung zur Authentifizierung von Benutzern verwenden. Die Bibliotheken kümmern sich um die Verbundbenutzerflows.  Manchmal übermitteln Legacy-Anwendungen, insbesondere solche, die ROPC-Grants (Resource Owner Password Credentials) verwenden, Benutzernamen und Kennwörter direkt an Azure AD und sind nicht so geschrieben, dass sie Federation verstehen. Sie führen kein HRD durch und interagieren nicht mit dem richtigen föderierten Endpunkt, um einen Benutzer zu authentifizieren. Wenn Sie sich dafür entscheiden, können Sie die [Richtlinie zur Startbereichsermittlung zur Aktivierung bestimmter Legacy-Anwendungen](configure-authentication-for-federated-users-portal.md) verwenden, um bestimmten Legacy-Anwendungen, die Benutzername/Passwort-Anmeldeinformationen über die ROPC-Berechtigung übermitteln, die direkte Authentifizierung mit Azure Active Directory zu ermöglichen, muss Password Hash Sync aktiviert sein.

> [!IMPORTANT]
> Aktivieren Sie die direkte Authentifizierung nur dann, wenn die Kennworthashsynchronisierung aktiviert ist und Sie sicher sind, dass diese Anwendung problemlos authentifiziert werden kann, ohne dass Richtlinien von Ihrem lokalen Identitätsanbieter implementiert werden. Wenn Sie die Kennworthashsynchronisierung oder die Verzeichnissynchronisierung mit AD Connect aus irgendeinem Grund deaktivieren, müssen Sie diese Richtlinie entfernen, um die Möglichkeit der direkten Authentifizierung mit einem veralteten Kennworthash zu verhindern.

## <a name="set-hrd-policy"></a>Festlegen der Richtlinie zur Startbereichsermittlung

Um in einer Anwendung für die automatische Beschleunigung bei der Verbundanmeldung oder für direkte cloudbasierte Anwendungen eine Richtlinie zur Startbereichsermittlung festzulegen, sind drei Schritte durchzuführen:

1. Erstellen einer Richtlinie zur Startbereichsermittlung

2. Ermitteln des Dienstprinzipals, an den die Richtlinie angefügt wird

3. Anfügen der Richtlinie an den Dienstprinzipal

Richtlinien treten für eine bestimmte Anwendung nur in Kraft, wenn sie an einen Dienstprinzipal angefügt werden.

Es kann immer nur eine Richtlinie zur Startbereichsermittlung auf einem Dienstprinzipal aktiv sein.

Sie können eine Richtlinie zur Startbereichsermittlung mit den Azure Active Directory PowerShell-Cmdlets erstellen und verwalten.

Im Folgenden finden Sie ein Beispiel für die Definition einer Personalentwicklungspolitik:

```json
{  
  "HomeRealmDiscoveryPolicy":
  {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
  }
}
```

Der Richtlinientyp lautet [HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy).

**AccelerateToFederatedDomain** ist optional. Wenn **AccelerateToFederatedDomain** FALSE ist, besitzt die Richtlinie keine Auswirkungen auf die automatische Beschleunigung. Wenn **AccelerateToFederatedDomain** TRUE ist und der Mandant nur eine verifizierte Verbunddomäne aufweist, werden Benutzer zur Anmeldung direkt zum Verbundidentitätsanbieter weitergeleitet. Wenn die Option TRUE ist und der Mandant mehrere verifizierte Domänen aufweist, muss **PreferredDomain** angegeben werden.

**PreferredDomain** ist optional. **PreferredDomain** sollte eine Domäne angeben, zu der die Beschleunigung erfolgt. Die Angabe kann ausgelassen werden, wenn der Mandant nur eine Verbunddomäne besitzt.  Wenn diese Angabe ausgelassen wird und mehrere überprüfte Verbunddomänen verfügbar sind, besitzt die Richtlinie keine Auswirkungen.

 Wenn **PreferredDomain** angegeben wird, muss diese Angabe mit einer überprüften Verbunddomäne für den Mandanten übereinstimmen. Alle Benutzer der Anwendung müssen sich bei dieser Domäne anmelden können. Benutzer, die sich nicht bei der Verbunddomäne anmelden können, können den Vorgang nicht fortsetzen und die Anmeldung nicht abschließen.

**AllowCloudPasswordValidation** ist optional. Wenn **AllowCloudPasswordValidation** TRUE ist, darf die Anwendung einen Verbundbenutzer durch direkte Übermittlung von Anmeldeinformationen (Benutzername/Kennwort) an den Tokenendpunkt von Azure Active Directory authentifizieren. Dies funktioniert nur, wenn die Kennworthashsynchronisierung aktiviert ist.

Darüber hinaus stehen zwei HRD-Optionen auf Mandantenebene zur Verfügung, die oben nicht behandelt wurden:

- **AlternateIdLogin** ist optional.  Ist diese Option aktiviert, [können Benutzer anstelle ihres UPN ihre E-Mail-Adresse verwenden](../authentication/howto-authentication-use-email-signin.md), um sich auf der Azure AD-Anmeldeseite anzumelden.  Die Verwendung alternativer IDs setzt voraus, dass der Benutzer nicht automatisch zu einem Verbundidentitätsanbieter weitergeleitet wird.

- **DomainHintPolicy** ist ein optionales komplexes Objekt, das [*verhindert*, dass Benutzer durch Domänenhinweise automatisch zu Verbunddomänen weitergeleitet werden](prevent-domain-hints-with-home-realm-discovery.md). Mithilfe dieser mandantenweiten Einstellung wird sichergestellt, dass Benutzer durch Anwendungen, die Domänenhinweise senden, nicht davon abgehalten werden, sich mit in der Cloud verwalteten Anmeldeinformationen anzumelden.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorität und Auswertung von Richtlinien zur Startbereichsermittlung

Richtlinien zur Startbereichsermittlung können erstellt und dann bestimmten Organisationen und Dienstprinzipalen zugewiesen werden. Das bedeutet, dass mehrere Richtlinien für eine bestimmte Anwendung gelten können. Daher muss von Azure AD entschieden werden, welche Richtlinie Vorrang hat. Welche (der zahlreichen angewendeten) HRD-Richtlinien wirksam wird, hängt von einer Reihe von Regeln ab:

- Wenn die Authentifizierungsanforderung einen Domänenhinweis enthält, wird die HRD-Richtlinie für den Mandanten (also die Richtlinie, die als Standard für den Mandanten festgelegt ist) überprüft, um zu ermitteln, ob Domänenhinweise ignoriert werden sollen. Sind Domänenhinweise zulässig, wird das durch den Domänenhinweis angegebene Verhalten verwendet.

- Andernfalls wird sie erzwungen, wenn eine Richtlinie explizit dem Dienstprinzipal zugewiesen ist.

- Wenn kein Domänenhinweis vorhanden und dem Dienstprinzipal nicht explizit eine Richtlinie zugewiesen ist, wird eine Richtlinie erzwungen, die ausdrücklich der übergeordneten Organisation des Dienstprinzipals zugewiesen ist.

- Wenn kein Domänenhinweis vorhanden ist und dem Dienstprinzipal oder der Organisation keine Richtlinie zugewiesen wurde, wird das standardmäßige Verhalten der Startbereichsermittlung verwendet.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren Sie das Anmeldeverhalten für eine Anwendung mithilfe einer Richtlinie zur Startbereichsermittlung](configure-authentication-for-federated-users-portal.md)
- [Deaktivieren der automatischen Anmeldebeschleunigung an einen Verbundidentitätsanbieter während der Benutzeranmeldung per Richtlinie zur Startbereichsermittlung (Home Realm Discovery, HRD)](prevent-domain-hints-with-home-realm-discovery.md)
- Weitere Informationen zur Funktionsweise der Authentifizierung in Azure AD finden Sie unter [Authentifizierungsszenarien für Azure AD](../develop/authentication-vs-authorization.md).
