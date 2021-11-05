---
title: Sicherheitsempfehlungen von Microsoft Defender für Cloud für MFA
description: Hier erfahren Sie, wie Sie die mehrstufige Authentifizierung für Ihre Azure-Abonnements mithilfe von Microsoft Defender für Cloud erzwingen.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ba741d97e3a9537b64957ce419b06208fd2a528
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095410"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Verwalten der MFA-Erzwingung (mehrstufige Authentifizierung) für Ihre Abonnements

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie für die Benutzerauthentifizierung lediglich Kennwörter verwenden, kann dies einen Angriffsvektor darstellen. Benutzer verwenden oft schwache Kennwörter oder dieselben Kennwörter für mehrere Dienste. Wenn [Multi-Factor Authentication](https://www.microsoft.com/security/business/identity/mfa) (MFA) aktiviert ist, sind Ihre Konten sicherer, und Benutzer können sich weiterhin bei nahezu allen Anwendungen mit der einmaligen Anmeldung (Single Sign-On, SSO) authentifizieren.

Es gibt mehrere Möglichkeiten, die mehrstufige Authentifizierung für Ihre Azure AD-Benutzer (Azure Active Directory) basierend auf den Lizenzen zu aktivieren, die Ihre Organisation besitzt. Diese Seite enthält die Details zu jeder Lizenz im Kontext von Microsoft Defender für Cloud.


## <a name="mfa-and-microsoft-defender-for-cloud"></a>MFA und Microsoft Defender für Cloud 

Defender für Cloud legt einen hohen Wert auf MFA. Die Sicherheitskontrolle, die am meisten zur Ihrer Sicherheitsbewertung beiträgt, ist **MFA aktivieren**. 

Durch die Empfehlungen in „MFA aktivieren“ wird sichergestellt, dass Sie sich an die empfohlenen Vorgehensweisen für Benutzer Ihrer Abonnements halten:

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibberechtigungen für Ihr Abonnement aktiviert werden.

Es gibt drei Möglichkeiten, MFA zu aktivieren und sich an die zwei Empfehlungen in Defender für Cloud zu halten: Sicherheitsstandards, Zuweisung nach Benutzer*in und die Richtlinie für bedingter Zugriff (ZS). All diese Optionen werden nachstehend erläutert.

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

Sie können die Liste der Benutzerkonten ohne aktivierte MFA entweder auf der Seite mit den Empfehlungsdetails für Defender für Cloud oder mithilfe von Azure Resource Graph anzeigen.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Anzeigen der Konten ohne aktivierte mehrstufige Authentifizierung im Azure-Portal
Wählen Sie auf der Seite mit den Details der Empfehlungen aus der Liste **Fehlerhafte Ressourcen** ein Abonnement aus, oder wählen Sie **Aktion ausführen** aus, damit die Liste angezeigt wird.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Anzeigen der Konten ohne aktivierte mehrstufige Authentifizierung mithilfe von Azure Resource Graph
Verwenden Sie die folgende Azure Resource Graph-Abfrage, um anzuzeigen, für welche Konten MFA nicht aktiviert ist. Die Abfrage gibt alle fehlerhaften Ressourcen (Konten) zur Empfehlung „MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein“ zurück. 

1. Öffnen Sie den **Azure Resource Graph-Explorer**.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Die Empfehlungsseite des Azure Resource Graph-Explorer** wird gestartet." :::

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
> Alternativ können Sie die GET-Methode für [Bewertungen](/rest/api/securitycenter/assessments/get) der REST-API für Defender für Cloud verwenden.


## <a name="faq---mfa-in-defender-for-cloud"></a>Häufig gestellte Fragen: MFA in Defender für Cloud

- [Wir verwenden bereits die Richtlinie für bedingten Zugriff, um MFA zu erzwingen. Warum erhalten wir immer noch die Empfehlungen für Defender für Cloud?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations)
- [Wir verwenden ein MFA-Tool eines Drittanbieters, um MFA zu erzwingen. Warum erhalten wir immer noch die Empfehlungen für Defender für Cloud?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations)
- [Warum zeigt Defender für Cloud Benutzerkonten ohne Berechtigungen für das Abonnement mit dem Hinweis „MFA erforderlich“ an?](#why-does-defender-for-cloud-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Wir erzwingen MFA mit PIM. Warum werden PIM-Konten als nicht konform angezeigt?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Kann ich einige Konten ausschließen bzw. schließen?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Gibt es Einschränkungen für den Identitäts- und Zugriffsschutz von Defender für Cloud?](#are-there-any-limitations-to-defender-for-clouds-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations"></a>Wir verwenden bereits die Richtlinie für bedingten Zugriff zum Erzwingen der mehrstufigen Authentifizierung. Warum erhalten wir immer noch die Empfehlungen für Defender für Cloud?
Überprüfen Sie die folgenden Konfigurationsoptionen in Ihrer MFA-Richtlinie für bedingten Zugriff, um zu untersuchen, wieso die Empfehlungen noch immer generiert werden:

- Die Konten sind im Bereich **Benutzer** (oder eine der Gruppen ist im Bereich **Gruppen**) Ihrer MFA-Richtlinie für bedingten Zugriff enthalten.
- Die App-ID der Azure-Verwaltung (797f4846-ba00-4fd7-ba43-dac1f8f63013) oder „Alle Apps“ ist im Abschnitt **Apps** Ihrer MFA-Richtlinie für bedingten Zugriff angegeben.
- Die App-ID der Azure-Verwaltung ist im Bereich **Apps** Ihrer MFA-Richtlinie für bedingten Zugriff nicht ausgeschlossen.

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-defender-for-cloud-recommendations"></a>Wir verwenden ein MFA-Tool eines Drittanbieters zum Erzwingen der mehrstufigen Authentifizierung. Warum erhalten wir immer noch die Empfehlungen für Defender für Cloud?
Die Defender für Cloud-Empfehlungen zu MFA unterstützen keine MFA-Tools von Drittanbietern (z. B. DUO).

Wenn die Empfehlungen für Ihre Organisation nicht relevant sind, können Sie diese wie unter [Ausschließen von Ressourcen und Empfehlungen aus der Sicherheitsbewertung](exempt-resource.md) beschrieben als „Entschärft“ kennzeichnen. Sie können auch [eine Empfehlung deaktivieren](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-defender-for-cloud-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Warum zeigt Defender für Cloud Benutzerkonten ohne Berechtigungen für das Abonnement mit dem Hinweis „MFA erforderlich“ an?
Die Defender für Cloud-Empfehlungen zu MFA beziehen sich auf [Azure RBAC](../role-based-access-control/role-definitions-list.md)-Rollen und die [„Klassische Azure-Abonnementadministratoren“](../role-based-access-control/classic-administrators.md)-Rolle. Stellen Sie sicher, dass keines der Konten über diese Rollen verfügt.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Wir erzwingen die mehrstufige Authentifizierung mit PIM. Wieso werden PIM-Konten als nicht konform angezeigt?
Die Defender für Cloud-Empfehlungen zu MFA unterstützen derzeit keine PIM-Konten. Sie können diese Konten im Abschnitt „Benutzer“ bzw. „Gruppe“einer Richtlinie für bedingten Zugriff hinzufügen.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Kann ich einige Konten ausschließen bzw. schließen?
Die Funktion zum Ausschließen einiger Konten, die keine mehrstufige Authentifizierung verwenden, wird derzeit nicht unterstützt.  

### <a name="are-there-any-limitations-to-defender-for-clouds-identity-and-access-protections"></a>Gibt es Einschränkungen für den Identitäts- und Zugriffsschutz von Defender für Cloud?
Es gibt einige Einschränkungen für den Identitäts- und Zugriffsschutz von Defender für Cloud:

- Empfehlungen zu Identitäten werden für Abonnements mit mehr als 600 Konten nicht angeboten. In solchen Fällen werden diese Empfehlungen unter „Nicht verfügbare Bewertungen“ aufgeführt.
- Für die Administrator-Agents des CSP-Partners (Cloud Solution Provider) sind keine Empfehlungen zu Identitäten verfügbar.
- Mit Empfehlungen zu Identitäten werden keine Konten identifiziert, die mit einem PIM-System (Privileged Identity Management) verwaltet werden. Wenn Sie ein PIM-Tool verwenden, werden unter der Steuerung **Zugriff und Berechtigungen verwalten** möglicherweise falsche Ergebnisse angezeigt.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie im folgenden Artikel:

- [Schützen Ihres Netzwerks in Microsoft Defender für Cloud](protect-network-resources.md)
