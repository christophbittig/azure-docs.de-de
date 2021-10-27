---
title: Azure Security Center-Sicherheitsempfehlungen für die MFA
description: Hier erfahren Sie, wie Sie mithilfe von Azure Security Center die mehrstufige Authentifizierung für Ihre Azure-Abonnements erzwingen.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 6ca7b13a7a0873300995a91a6469fc29050f3c95
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047036"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Verwalten der MFA-Erzwingung (mehrstufige Authentifizierung) für Ihre Abonnements

Wenn Sie für die Benutzerauthentifizierung lediglich Kennwörter verwenden, kann dies einen Angriffsvektor darstellen. Benutzer verwenden oft schwache Kennwörter oder dieselben Kennwörter für mehrere Dienste. Wenn [Multi-Factor Authentication](https://www.microsoft.com/security/business/identity/mfa) (MFA) aktiviert ist, sind Ihre Konten sicherer, und Benutzer können sich weiterhin bei nahezu allen Anwendungen mit der einmaligen Anmeldung (Single Sign-On, SSO) authentifizieren.

Es gibt mehrere Möglichkeiten, die mehrstufige Authentifizierung für Ihre Azure AD-Benutzer (Azure Active Directory) basierend auf den Lizenzen zu aktivieren, die Ihre Organisation besitzt. Diese Seite enthält die Details zu allen Möglichkeiten im Kontext von Azure Security Center.


## <a name="mfa-and-security-center"></a>MFA und Security Center 

Security Center legt viel Wert auf die mehrstufige Authentifizierung. Die Sicherheitskontrolle, die am meisten zur Ihrer Sicherheitsbewertung beiträgt, ist **MFA aktivieren**. 

Durch die Empfehlungen in „MFA aktivieren“ wird sichergestellt, dass Sie sich an die empfohlenen Vorgehensweisen für Benutzer Ihrer Abonnements halten:

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibberechtigungen für Ihr Abonnement aktiviert werden.

Es gibt drei Möglichkeiten, MFA zu aktivieren und sich an die zwei Empfehlungen in Security Center zu halten: Sicherheitsstandards, die Zuweisung nach Benutzer und die Richtlinie für bedingten Zugriff. All diese Optionen werden nachstehend erläutert.

### <a name="free-option---security-defaults"></a>Kostenlose Option: Sicherheitsstandards
Wenn Sie die kostenlose Edition von Azure AD verwenden, aktivieren Sie die mehrstufige Authentifizierung für Ihren Mandanten mithilfe von [Sicherheitsstandards](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA für Microsoft 365 Business-, E3- oder E5-Kunden
Kunden mit Microsoft 365 können die **Zuweisung nach Benutzer** verwenden. In diesem Szenario wird Azure AD MFA für alle Benutzer und alle Anmeldeereignisse aktiviert oder deaktiviert. Es besteht keine Möglichkeit, die mehrstufige Authentifizierung nur für einen Teil der Benutzer oder nur für bestimmte Szenarios zu aktivieren. Die Verwaltung erfolgt über das Office 365-Portal.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA für Azure AD Premium-Kunden
Führen Sie zugunsten einer optimierten Benutzerumgebung ein Upgrade auf Azure AD Premium P1 oder P2 durch, und verwenden Sie **Richtlinien für bedingten Zugriff**. Sie benötigen [Berechtigungen für den Azure AD-Mandanten (Azure Active Directory)](../active-directory/roles/permissions-reference.md), um eine Richtlinie für bedingten Zugriff konfigurieren zu können.

Für Ihre Richtlinie für bedingten Zugriff muss Folgendes gelten:
- Sie muss MFA erzwingen.
- Sie muss die App-ID der Microsoft Azure-Verwaltung (797f4846-ba00-4fd7-ba43-dac1f8f63013) oder „Alle Apps“ angeben.
- Sie darf die App-ID der Microsoft Azure-Verwaltung nicht ausschließen.

**Azure AD Premium P1**-Kunden können den bedingten Azure AD-Zugriff verwenden, um Benutzer in bestimmten Szenarios oder bei bestimmten Ereignissen zur mehrstufigen Authentifizierung aufzufordern und auf diese Weise die geschäftlichen Anforderungen zu erfüllen. Dieses Feature ist auch bei den folgenden Lizenzen enthalten: Enterprise Mobility + Security E3, Microsoft 365 F1 und Microsoft 365 E3.

**Azure AD Premium P2** bietet die stärksten Sicherheitsfeatures und eine verbesserte Benutzerumgebung. Bei dieser Lizenz ist im Vergleich mit den Azure AD Premium P1-Features zusätzlich der [risikobasierte bedingte Zugriff](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) möglich. Der risikobasierte bedingte Zugriff passt sich an die Muster Ihrer Benutzer an und fordert weniger häufig zur mehrstufigen Authentifizierung auf. Dieses Feature ist auch bei den folgenden Lizenzen enthalten: Enterprise Mobility + Security E5 und Microsoft 365 E5.

Weitere Informationen finden Sie in der [Azure-Dokumentation zum bedingten Zugriff](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identifizieren von Konten ohne aktivierte mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)

Die Liste der Benutzerkonten, bei denen MFA nicht aktiviert ist, wird entweder auf der Seite mit den Details der Security Center-Empfehlungen oder durch Verwenden von Azure Resource Graph angezeigt.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Anzeigen der Konten ohne aktivierte mehrstufige Authentifizierung im Azure-Portal
Wählen Sie auf der Seite mit den Details der Empfehlungen aus der Liste **Fehlerhafte Ressourcen** ein Abonnement aus, oder wählen Sie **Aktion ausführen** aus, damit die Liste angezeigt wird.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Anzeigen der Konten ohne aktivierte mehrstufige Authentifizierung mithilfe von Azure Resource Graph
Verwenden Sie die folgende Azure Resource Graph-Abfrage, um anzuzeigen, für welche Konten MFA nicht aktiviert ist. Die Abfrage gibt alle fehlerhaften Ressourcen (Konten) zur Empfehlung „MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein“ zurück. 

1. Öffnen Sie den **Azure Resource Graph-Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Die Empfehlungsseite des Azure Resource Graph-Explorer** wird gestartet." :::

1. Geben Sie die folgende Abfrage ein, und wählen Sie **Abfrage ausführen** aus.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. Mit der `additionalData`-Eigenschaft lässt sich die Liste der Kontoobjekt-IDs für Konten anzeigen, für die MFA nicht erzwungen wird. 

    > [!NOTE]
    > Die Konten werden mit ihrer Objekt-ID und nicht mit ihrem Kontonamen angezeigt, um den Datenschutz für die Kontoinhaber sicherzustellen.

> [!TIP]
> Sie können auch die REST-API-Methode [Assessments – Get](/rest/api/securitycenter/assessments/get) im Security Center verwenden.


## <a name="faq---mfa-in-security-center"></a>Häufig gestellte Fragen: MFA in Security Center

- [Wir verwenden bereits eine ZS-Richtlinie, um MFA zu erzwingen. Warum erhalten wir weiterhin die Security Center-Empfehlungen?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Wir verwenden das MFA-Tool eines Drittanbieters, um MFA zu erzwingen. Warum erhalten wir weiterhin die Security Center-Empfehlungen?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Wieso zeigt Security Center für Benutzerkonten ohne Berechtigungen für das Abonnement an, dass sie MFA erfordern?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Wir erzwingen MFA mit PIM. Warum werden PIM-Konten als nicht konform angezeigt?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Kann ich einige Konten ausschließen bzw. schließen?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Gibt es Einschränkungen beim Identitäts- und Zugriffsschutz von Security Center?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Wir verwenden bereits die Richtlinie für bedingten Zugriff zum Erzwingen der mehrstufigen Authentifizierung. Wieso werden uns immer noch Security Center-Empfehlungen angezeigt?
Überprüfen Sie die folgenden Konfigurationsoptionen in Ihrer MFA-Richtlinie für bedingten Zugriff, um zu untersuchen, wieso die Empfehlungen noch immer generiert werden:

- Die Konten sind im Bereich **Benutzer** (oder eine der Gruppen ist im Bereich **Gruppen**) Ihrer MFA-Richtlinie für bedingten Zugriff enthalten.
- Die App-ID der Azure-Verwaltung (797f4846-ba00-4fd7-ba43-dac1f8f63013) oder „Alle Apps“ ist im Abschnitt **Apps** Ihrer MFA-Richtlinie für bedingten Zugriff angegeben.
- Die App-ID der Azure-Verwaltung ist im Bereich **Apps** Ihrer MFA-Richtlinie für bedingten Zugriff nicht ausgeschlossen.

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Wir verwenden ein MFA-Tool eines Drittanbieters zum Erzwingen der mehrstufigen Authentifizierung. Wieso werden uns immer noch Security Center-Empfehlungen angezeigt?
Die Security Center-Empfehlungen zu MFA unterstützen keine MFA-Tools von Drittanbietern (z. B. DUO).

Wenn die Empfehlungen für Ihre Organisation nicht relevant sind, können Sie diese wie unter [Ausschließen von Ressourcen und Empfehlungen aus der Sicherheitsbewertung](exempt-resource.md) beschrieben als „Entschärft“ kennzeichnen. Sie können auch [eine Empfehlung deaktivieren](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Wieso zeigt Security Center für Benutzerkonten ohne Berechtigungen für das Abonnement an, dass sie MFA erfordern?
Die Security Center-Empfehlungen zu MFA beziehen sich auf die [Azure RBAC-Rollen](../role-based-access-control/role-definitions-list.md) und die [Administratorrolle des klassischen Azure-Abonnements](../role-based-access-control/classic-administrators.md). Stellen Sie sicher, dass keines der Konten über diese Rollen verfügt.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Wir erzwingen die mehrstufige Authentifizierung mit PIM. Wieso werden PIM-Konten als nicht konform angezeigt?
PIM-Konten werden für die Security Center-Empfehlungen zu MFA derzeit nicht unterstützt. Sie können diese Konten im Abschnitt „Benutzer“ bzw. „Gruppe“einer Richtlinie für bedingten Zugriff hinzufügen.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Kann ich einige Konten ausschließen bzw. schließen?
Die Funktion zum Ausschließen einiger Konten, die keine mehrstufige Authentifizierung verwenden, wird derzeit nicht unterstützt.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Gibt es Einschränkungen beim Identitäts- und Zugriffsschutz von Security Center?
Es gibt einige Einschränkungen beim Identitäts- und Zugriffsschutz im Security Center:

- Empfehlungen zu Identitäten werden für Abonnements mit mehr als 600 Konten nicht angeboten. In solchen Fällen werden diese Empfehlungen unter „Nicht verfügbare Bewertungen“ aufgeführt.
- Für die Administrator-Agents des CSP-Partners (Cloud Solution Provider) sind keine Empfehlungen zu Identitäten verfügbar.
- Mit Empfehlungen zu Identitäten werden keine Konten identifiziert, die mit einem PIM-System (Privileged Identity Management) verwaltet werden. Wenn Sie ein PIM-Tool verwenden, werden unter der Steuerung **Zugriff und Berechtigungen verwalten** möglicherweise falsche Ergebnisse angezeigt.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie im folgenden Artikel:

- [Schützen Ihres Netzwerks in Azure Security Center](security-center-network-recommendations.md)