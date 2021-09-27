---
title: Erweiterte Anforderungsbegrenzung mit Azure API Management
description: Erfahren Sie, wie Sie mit Azure API Management flexibel Richtlinien zur Kontingents- und Ratenbegrenzung erstellen und anwenden.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: danlep
ms.openlocfilehash: 177d393041b397e504c1c8c155fe9b2b1f99b451
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653683"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Erweiterte Anforderungsbegrenzung mit Azure API Management
Die Fähigkeit, eingehende Anforderungen zu begrenzen oder zu drosseln, ist für Azure API Management von großer Bedeutung. Mit API Management lässt sich entweder die Rate der Anforderungen oder die Gesamtzahl der Anforderungen bzw. die Gesamtmenge der übertragenen Daten steuern. Dies ermöglicht es den API-Anbietern, ihre APIs vor Missbrauch zu schützen und mit verschiedenen API-Produkttarifen Mehrwert zu schaffen.

## <a name="rate-limits-and-quotas"></a>Ratenbegrenzungen und Kontingente
Ratenbegrenzungen und Kontingente werden für verschiedene Zwecke verwendet.

### <a name="rate-limits"></a>Ratenbegrenzungen
Ratenbegrenzungen werden in der Regel zum Schutz vor kurzen, hohen Volumenspitzen verwendet. Wenn Sie z. B. wissen, dass es bei der Datenbank des Back-End-Diensts bei einem hohen Aufrufvolumen zu einem Engpass kommt, können Sie mithilfe dieser Einstellung in einer `rate-limit-by-key`-Richtlinie festlegen, dass ein hohes Aufrufvolumen nicht zugelassen wird.

### <a name="quotas"></a>Kontingente
Kontingente werden in der Regel zum Steuern von Aufrufraten über einen längeren Zeitraum verwendet. Mit ihnen kann z. B. die Gesamtanzahl von Aufrufen durch einen bestimmten Abonnenten in einem bestimmten Monat festgelegt werden. Beim Monetarisieren Ihrer API können Sie bei Abonnements mit mehreren Tarifen auch verschiedene Kontingente festgelegen. Beispielsweise können mit einem Abonnement mit dem Tarif „Basic“möglicherweise nicht mehr als 10.000 Aufrufe pro Monat getätigt werden, mit dem Tarif „Premium“ jedoch bis zu 100.000.000.

In Azure API Management werden Ratenbegrenzungen in der Regel schneller an die Knoten übermittelt, um sie vor Spitzen zu schützen. Nutzungskontingentinformationen werden hingegen über einen längeren Zeitraum verwendet und daher anders implementiert.

> [!CAUTION]
> Da die Einschränkungsarchitektur verteilt ist, ist die Begrenzung der Rate nie ganz genau. Die Differenz zwischen der konfigurierten und der tatsächlichen Anzahl zugelassener Anforderungen variiert basierend auf Anforderungsvolumen und -rate, Back-End-Latenz und anderen Faktoren.

## <a name="product-based-throttling"></a>Produktbasierte Drosselung
Die Funktionen zur Ratendrosselung, die jeweils auf ein bestimmtes Abonnement bezogen sind, helfen dem API-Anbieter, Grenzwerte für Entwickler anzuwenden, die sich für die Verwendung ihrer API registriert haben. Sie helfen jedoch nicht, um beispielsweise einzelne Endbenutzer der API zu drosseln. Daher ist es möglich, dass ein einzelner Benutzer der Anwendung eines Entwicklers das gesamte Kontingent in Anspruch nimmt und dadurch andere Kunden des Entwicklers an der Nutzung der Anwendung hindert. Außerdem können verschiedene Kunden gemeinsam ein so hohes Anforderungsvolumen generieren, dass der Zugriff für gelegentliche Benutzer beschränkt wird.

## <a name="custom-key-based-throttling"></a>Benutzerdefinierte schlüsselbasierte Drosselung

> [!NOTE]
> Im Verbrauchstarif von Azure API Management sind die Richtlinien `rate-limit-by-key` und `quota-by-key` nicht verfügbar. 

Die Richtlinien [rate-limit-by-key](./api-management-access-restriction-policies.md#LimitCallRateByKey) und [quota-by-key](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) bieten mehr Flexibilität bei der Steuerung des Datenverkehrs. Mit diesen Richtlinien können Sie in selbst definierten Ausdrücken Schlüssel angeben, die zum Nachverfolgen der Datenverkehrsnutzung verwendet werden. Wie das funktioniert, lässt sich am einfachsten anhand eines Beispiels veranschaulichen. 

## <a name="ip-address-throttling"></a>Drosselung basierend auf der IP-Adresse
Die folgenden Richtlinien beschränken eine einzelne Client-IP-Adresse auf nur 10 Aufrufe pro Minute bei einer Gesamtanzahl von 1.000.000 Aufrufen und 10.000 Kilobyte Bandbreite pro Monat. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Wenn alle Clients im Internet eine eindeutige IP-Adresse verwenden würden, wäre dies vielleicht eine effektive Möglichkeit zur Nutzungsbegrenzung nach Benutzer. Allerdings ist die Wahrscheinlichkeit hoch, dass mehrere Benutzer eine einzige öffentliche IP-Adresse gemeinsam nutzen, weil sie über ein NAT-Gerät auf das Internet zugreifen. Für APIs, die einen nicht authentifizierten Zugriff zulassen, ist `IpAddress` dennoch unter Umständen die beste Option.

## <a name="user-identity-throttling"></a>Drosselung nach Benutzeridentität
Wenn ein Endbenutzer authentifiziert ist, kann auf Basis der eindeutigen Identitätsinformationen dieses Benutzers ein Drosselungsschlüssel generiert werden.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

In diesem Beispiel wird veranschaulicht, wie der „Authorization“-Header extrahiert, in ein `JWT`-Objekt konvertiert und der Antragsteller des Tokens verwendet wird, um den Benutzer zu identifizieren. Diesen verwenden wir als Schlüssel zum Begrenzen der Rate. Wenn die Benutzeridentität in `JWT` als einer der anderen Ansprüche gespeichert ist, kann stattdessen dieser Wert verwendet werden.

## <a name="combined-policies"></a>Kombinierte Richtlinien
Obwohl die benutzerbasierten Drosselungsrichtlinien mehr Steuerungsmöglichkeiten bieten als die abonnementbasierten, lohnt es sich weiterhin, beide Fähigkeiten zu kombinieren. Die Drosselung nach Produktabonnementschlüssel ([Aufrufrate nach Abonnement einschränken](./api-management-access-restriction-policies.md#LimitCallRate) und [Nutzungskontingent nach Abonnement festlegen](./api-management-access-restriction-policies.md#SetUsageQuota)) gibt Ihnen die Möglichkeit, nach Nutzungsstufen gestaffelte Gebühren für eine API festzulegen und sie so zu monetarisieren. Die feiner differenzierten Steuerungsmöglichkeiten einer Drosselung nach Benutzer können ergänzend genutzt werden, um zu verhindern, dass das Verhalten eines Benutzers die Erfahrung eines anderen beeinträchtigt. 

## <a name="client-driven-throttling"></a>Clientgesteuerte Drosselung
Wenn der Drosselungsschlüssel mithilfe eines [Richtlinienausdrucks](./api-management-policy-expressions.md) definiert wird, legt der API-Anbieter Art und Umfang der Drosselung fest. Ein Entwickler möchte jedoch möglicherweise selbst steuern, wie er die Raten für seine Kunden begrenzt. Der API-Anbieter kann dies einrichten. Dazu führt er einen benutzerdefinierten Header ein, der es der Clientanwendung des Entwicklers ermöglicht, den Schlüssel an die API zu kommunizieren.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Auf diese Weise kann die Clientanwendung des Entwicklers festlegen, wie der Schlüssel zur Ratenbegrenzung erstellt werden soll. Die Cliententwickler können eigene Ratenstufen erstellen, indem sie den Benutzern Gruppen von Schlüsseln zuweisen und eine Rotation der Schlüsselnutzung einrichten.

## <a name="summary"></a>Zusammenfassung
Das Azure API Management bietet Raten- und Quoten-Drosselung, um Ihren API-Dienst zu schützen und aufzuwerten. Die neuen Drosselungsrichtlinien ermöglichen durch ihre benutzerdefinierten Umfangsregeln eine feinere Steuerung der Drosselung, sodass Ihre Kunden noch bessere Anwendungen erstellen können. Die Beispiele in diesem Artikel veranschaulichen die Verwendung dieser neuen Richtlinien. Sie zeigen, wie Client-IP-Adressen, Benutzeridentitäten und vom Client generierte Werte zum Erstellen von Schlüsseln zur Ratenbegrenzung genutzt werden können. Es gibt jedoch noch viele andere Teile einer Nachricht, die für diesen Zweck verwendet werden können, z. B. Benutzer-Agent, URL-Pfad-Fragmente und Nachrichtengröße.

## <a name="next-steps"></a>Nächste Schritte
Bitte geben Sie uns Feedback in Form eines GitHub-Issues zu diesem Thema. Wir würden gern von Ihnen hören, welche anderen möglichen Schlüsselwerte sich in Ihren Szenarien anbieten oder bewährt haben.
