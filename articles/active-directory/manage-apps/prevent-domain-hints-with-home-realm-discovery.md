---
title: Verhindern der automatischen Anmeldebeschleunigung mit der Richtlinie für die Startbereichsermittlung (Home Realm Discovery, HRD)
titleSuffix: Azure AD
description: Es wird beschrieben, wie Sie die automatische Anmeldebeschleunigung über Domänenhinweise an einen Verbundidentitätsanbieter verhindern.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: 912299553e48bbaec6c815e44f91a6ac0b42441e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547467"
---
# <a name="disable-auto-acceleration-sign-in"></a>Deaktivieren der Automatischen Beschleunigung der Anmeldung

Mit der [Richtlinie zur Startbereichsermittlung (Home Realm Discovery, HRD)](/graph/api/resources/homeRealmDiscoveryPolicy) können Administratoren auf verschiedene Weise steuern, wie und wo sich Benutzer authentifizieren müssen. Der Abschnitt `domainHintPolicy` der HRD-Richtlinie wird verwendet, um Verbundbenutzer zu in der Cloud verwalteten Anmeldeinformationen, z. B. [FIDO](../authentication/howto-authentication-passwordless-security-key.md), zu migrieren. Hierfür wird sichergestellt, dass sie immer auf die Azure AD-Anmeldeseite geleitet werden und nicht aufgrund von Domänenhinweisen die automatische Anmeldebeschleunigung an einen Verbundidentitätsanbieter erfolgt.

Diese Richtlinie wird in Situationen benötigt, in denen von Anwendungen, die von einem Administrator nicht gesteuert oder aktualisiert werden können, während der Anmeldung Domänenhinweise hinzugefügt werden.  Von `outlook.com/contoso.com` wird der Benutzer beispielsweise an eine Anmeldeseite mit angefügtem Parameter `&domain_hint=contoso.com` geleitet, um für den Benutzer die direkte automatische Anmeldebeschleunigung an den Verbundidentitätsanbieter für die Domäne `contoso.com` durchzuführen. Benutzer mit verwalteten Anmeldeinformationen, die an einen Verbundidentitätsanbieter geleitet werden, können sich nicht mit ihren verwalteten Anmeldeinformationen anmelden. Dies führt zu einer Verringerung der Sicherheit und zu Frustration bei den Benutzern, weil die Anmeldeumgebung nicht einheitlich ist. Administratoren, die verwaltete Anmeldeinformationen einführen, [sollten auch diese Richtlinie einrichten](#suggested-use-within-a-tenant). Hierdurch wird sichergestellt, dass Benutzer immer ihre verwalteten Anmeldeinformationen nutzen können.

## <a name="domainhintpolicy-details"></a>Details zu „DomainHintPolicy“

Der Abschnitt „DomainHintPolicy“ der HRD-Richtlinie ist ein JSON-Objekt, mit dem ein Administrator bestimmte Domänen und Anwendungen aus den Domänenhinweisen ausschließen kann.  Aus technischer Sicht bedeutet dies Folgendes: Die Azure AD-Anmeldeseite wird angewiesen, sich so zu verhalten, als ob die Anmeldeanforderung den Parameter `domain_hint` nicht enthält.

### <a name="the-respect-and-ignore-policy-sections"></a>Abschnitte zum Beachten/Ignorieren der Richtlinie (Respect/Ignore)

|`Section` | Bedeutung | Werte |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Wenn dieser Domänenhinweis in der gesendeten Anforderung enthalten ist, wird er ignoriert. |Array mit Domänenadressen (z. B. `contoso.com`). `all_domains` wird ebenfalls unterstützt.|
|`RespectDomainHintForDomains`| Wenn dieser Domänenhinweis in der gesendeten Anforderung enthalten ist, wird er auch dann beachtet, wenn mit `IgnoreDomainHintForApps` darauf hingewiesen wird, dass für die in der Anforderung enthaltene App keine automatische Anmeldebeschleunigung erfolgen soll. Hiermit wird der Rollout von veralteten Domänenhinweisen in Ihrem Netzwerk verlangsamt. Sie können aber angeben, dass die Beschleunigung für einige Domänen durchgeführt werden soll. | Array mit Domänenadressen (z. B. `contoso.com`). `all_domains` wird ebenfalls unterstützt.|
|`IgnoreDomainHintForApps`| Wenn eine Anforderung dieser Anwendung einen Domänenhinweis enthält, wird er ignoriert. | Array mit Anwendungs-IDs (GUIDs). `all_apps` wird ebenfalls unterstützt.|
|`RespectDomainHintForApps` |Wenn eine Anforderung dieser Anwendung einen Domänenhinweis enthält, wird er auch dann beachtet, wenn die Domäne in `IgnoreDomainHintForDomains` enthalten ist. Hiermit wird sichergestellt, dass einige Apps weiterhin funktionieren, wenn Sie feststellen, dass dafür Domänenhinweise erforderlich sind. | Array mit Anwendungs-IDs (GUIDs). `all_apps` wird ebenfalls unterstützt.|

### <a name="policy-evaluation"></a>Richtlinienauswertung

Die „DomainHintPolicy“-Logik wird für jede eingehende Anforderung ausgeführt, die einen Domänenhinweis enthält, und die Beschleunigung erfolgt anhand von zwei Datenelementen der Anforderung: der Domäne im Domänenhinweis und der Client-ID (die App). Kurz gesagt: Die Beachtung einer Domäne oder App hat Vorrang vor einer Anweisung zum Ignorieren eines Domänenhinweises für eine bestimmte Domäne oder Anwendung.

1. Falls keine Domänenhinweisrichtlinie vorhanden ist oder in keinem der vier Abschnitte auf die entsprechende App oder den Domänenhinweis verwiesen wird, wird der [restliche Teil der HRD-Richtlinie evaluiert](home-realm-discovery-policy.md#priority-and-evaluation-of-hrd-policies).
1. Falls entweder der Abschnitt `RespectDomainHintForApps` oder `RespectDomainHintForDomains` (oder beide) die App oder den Domänenhinweis in der Anforderung enthält, wird für den Benutzer wie gewünscht die automatische Beschleunigung an den Verbundidentitätsanbieter durchgeführt.
1. Falls entweder in `IgnoreDomainHintsForApps` oder `IgnoreDomainHintsForDomains` (oder an beiden Orten) in der Anforderung auf die App oder den Domänenhinweis verwiesen wird, aber in den Abschnitten für die Beachtung („Respect“) kein Verweis enthalten ist, wird für die Anforderung keine automatische Beschleunigung durchgeführt. Der Benutzer verbleibt auf der Azure AD-Anmeldeseite und muss einen Benutzernamen angeben.

Nachdem ein Benutzer auf der Anmeldeseite einen Benutzernamen eingegeben hat, kann er seine verwalteten Anmeldeinformationen verwenden, falls diese für ihn registriert wurden.  Wenn sich der Benutzer gegen die Verwendung der verwalteten Anmeldeinformationen entscheidet oder keine Informationen registriert sind, wird er wie üblich an seinen Verbunddienstanbieter geleitet, damit er die Anmeldeinformationen eingeben kann.

## <a name="suggested-use-within-a-tenant"></a>Empfohlene Nutzung innerhalb eines Mandanten

Administratoren von Verbunddomänen sollten für die Einrichtung dieses Abschnitts der HRD-Richtlinie einen Plan mit vier Phasen verwenden. Das Ziel des Plans besteht darin, dass schließlich alle Benutzer eines Mandanten ihre verwalteten Anmeldeinformationen verwenden können, und zwar unabhängig von der Domäne oder Anwendung (mit Ausnahme der Apps, für die in Bezug auf die Nutzung von `domain_hint` feste Abhängigkeiten gelten).  Anhand dieses Plans können Administratoren nach diesen Apps suchen, sie aus der neuen Richtlinie ausschließen und dann mit dem Rollout der Änderung für den restlichen Teil des Mandanten fortfahren.

1. Wählen Sie eine Domäne aus, für die diese Änderung zuerst eingeführt werden soll.  Da dies Ihre Testdomäne ist, sollten Sie eine Domäne auswählen, in der mehr Akzeptanz in Bezug auf Änderungen der Benutzeroberfläche (geänderte Anmeldeseite) vorhanden ist.  Hierbei werden alle Domänenhinweise aller Anwendungen ignoriert, in denen dieser Domänenname verwendet wird. Führen Sie das [Einrichten](#configuring-policy-through-graph-explorer) dieser Richtlinie in der HRD-Standardrichtlinie für Ihren Mandanten durch:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Sammeln Sie Feedback von den Benutzern der Testdomäne. Sammeln Sie die Details von Anwendungen, die aufgrund dieser Änderung beschädigt wurden. Da für diese Anwendungen die Verwendung von Domänenhinweisen zwingend erforderlich ist, sollten sie aktualisiert werden. Fügen Sie sie vorerst dem Abschnitt `RespectDomainHintForApps` hinzu:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Fahren Sie mit der Einführung der Richtlinie für neue Domänen fort, und sammeln Sie weiteres Feedback.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Schließen den Rollout ab, indem Sie alle Domänen ausschließen, für die weiterhin eine Beschleunigung erfolgen soll:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Nach Abschluss von Schritt 4 können sich alle Benutzer, die nicht in `guestHandlingDomain.com` enthalten sind, auf der Azure AD-Anmeldeseite anmelden. Dies gilt auch, wenn Domänenhinweise normalerweise dazu führen würden, dass eine automatische Anmeldebeschleunigung an einen Verbundidentitätsanbieter erfolgt.  Eine Ausnahme ist der Fall, in dem die App, von der die Anmeldung angefordert wird, in der Liste mit den ausgeschlossenen Apps enthalten ist. Für diese Apps werden alle Domänenhinweise weiterhin akzeptiert.

## <a name="configuring-policy-through-graph-explorer"></a>Konfigurieren der Richtlinie per Graph-Tester

Legen Sie die [HRD-Richtlinie](/graph/api/resources/homeRealmDiscoveryPolicy) wie gewohnt per Microsoft Graph fest.  

1. Gewähren Sie in [Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) die Berechtigung „Policy.ReadWrite.ApplicationConfiguration“.  
1. Verwenden Sie die URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`.
1. Führen Sie für die neue Richtlinie den POST-Vorgang für diese URL durch, oder den PATCH-Vorgang für `/policies/homerealmdiscoveryPolicies/{policyID}`, falls das Überschreiben erforderlich ist.

Inhalt von POST bzw. PATCH:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Achten Sie bei Verwendung von Graph darauf, dass Sie Schrägstriche verwenden, um den JSON-Abschnitt `Definition` mit Escapezeichen zu versehen.  

`isOrganizationDefault` muss auf „true“ festgelegt sein, aber „displayName“ und die Definition können sich ändern.

## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln](../authentication/howto-authentication-passwordless-security-key.md)
* [Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App (Vorschauversion)](../authentication/howto-authentication-passwordless-phone.md)
