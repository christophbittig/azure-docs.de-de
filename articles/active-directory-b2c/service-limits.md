---
title: Dienstlimits und -einschränkungen für Azure AD B2C
titleSuffix: Azure AD B2C
description: Referenz zu Dienstlimits und -einschränkungen für Azure Active Directory B2C
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/02/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 8e3ceab429f92340a080a6a42afd095375ce51b1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130266085"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Dienstlimits und -einschränkungen für Azure Active Directory B2C

In diesem Artikel werden die Nutzungseinschränkungen und andere Dienstlimits für Azure Active Directory B2C (Azure AD B2C) beschrieben.

## <a name="end-userconsumption-related-limits"></a>Grenzwerte für Endbenutzer und Verbrauch

Die folgenden Dienstlimits für Endbenutzer gelten für alle Authentifizierungs- und Autorisierungsprotokolle, die von Azure AD B2C unterstützt werden, einschließlich SAML, Open ID Connect, OAuth 2 und ROPC.

|Category |Begrenzung    |
|---------|---------|
|Anzahl von Anforderungen pro IP-Adresse pro Azure AD B2C-Mandant       |6\.000/5 min          |
|Gesamtanzahl von Anforderungen pro Azure AD B2C-Mandant     |12.000/min          |

Die Anzahl der Anforderungen variiert je nach der Anzahl der Lese- und Schreibvorgänge im Verzeichnis, die während der Azure AD B2C-User Journey auftreten. Eine einfache Anmelde-User Journey, die aus dem Verzeichnis liest, umfasst z. B. eine Anforderung. Wenn die Anmelde-User Journey auch das Verzeichnis aktualisieren muss, wird dieser Vorgang als zusätzliche Anforderung gezählt.

## <a name="azure-ad-b2c-configuration-limits"></a>Grenzwerte für die Azure AD B2C-Konfiguration

In der folgenden Tabelle sind die Grenzwerte für die Verwaltungskonfiguration im Azure AD B2C-Dienst aufgeführt.

|Category  |Begrenzung  |
|---------|---------|
|Anzahl der Bereiche pro Anwendung        |1000          |
|Anzahl von [benutzerdefinierten Attributen](user-profile-attributes.md#extension-attributes) pro Benutzer<sup>1</sup>       |100         |
|Anzahl der Umleitungs-URLs pro Anwendung       |100         |
|Anzahl der Abmelde-URLs pro Anwendung        |1          |
|Zeichenfolgenlimit pro Attribut      |250 Zeichen          |
|Anzahl der B2C-Mandanten pro Abonnement      |20         |
|[Vererbungsebenen](custom-policy-overview.md#inheritance-model) in benutzerdefinierten Richtlinien     |10         |
|Anzahl der Richtlinien pro Azure AD B2C-Mandant (Benutzerflows und benutzerdefinierte Richtlinien)     |200          |
|Maximale Dateigröße von Richtlinien      |1024 KB          |

<sup>1</sup> Weitere Informationen finden Sie unter [Dienstlimits und -einschränkungen für Azure AD](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Richtlinien für die Microsoft Graph-Drosselung](/graph/throttling). 
- Erfahren Sie mehr über die [Unterschiede bei der Validierung für Azure AD B2C-Anwendungen](../active-directory/develop/supported-accounts-validation.md).
