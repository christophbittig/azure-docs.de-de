---
title: Identity Protection und bedingter Zugriff in Azure AD B2C
description: Hier erfahren Sie, wie Sie mit Identity Protection Risikoanmeldungen und Risikoerkennungen anzeigen. Außerdem erfahren Sie, wie sich mithilfe von bedingtem Zugriff Organisationsrichtlinien auf der Grundlage von Risikoereignissen in Ihren Azure AD B2C-Mandanten erzwingen lassen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 05/28/2021
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c5c7b8ed515fc3148f42b06c3c81f6060816e79
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281379"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identity Protection und bedingter Zugriff für Azure AD B2C

Verbessern Sie die Sicherheit von Azure Active Directory B2C (Azure AD B2C) mit Azure AD Identity Protection und bedingtem Zugriff. Die Identity Protection-Features zur Erkennung von Risiken wie Risikobenutzern und Risikoanmeldungen werden automatisch erkannt und in Ihrem Azure AD B2C-Mandanten angezeigt. Sie können Richtlinien für bedingten Zugriff erstellen, die diese Risikoerkennungen nutzen, um Aktionen zu bestimmen und Organisationsrichtlinien zu erzwingen. Zusammen geben diese Funktionen Azure AD B2C-Anwendungsbesitzern mehr Kontrolle über risikobehaftete Authentifizierungen und Zugriffsrichtlinien.
  
Wenn Sie bereits mit [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) und [bedingtem Zugriff](../active-directory/conditional-access/overview.md) in Azure AD vertraut sind, gibt es bei der Verwendung dieser Funktionen mit Azure AD B2C nur einige kleinere Unterschiede zu beachten. Diese sind im vorliegenden Artikel beschrieben.

![Bedingter Zugriff in einem B2C-Mandanten](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Für die Erstellung von Risikoanmeldungsrichtlinien ist Azure AD B2C **Premium 2** erforderlich. **Premium P1**-Mandanten können eine auf Standort, Anwendung, Benutzern oder Gruppen basierende Richtlinie erstellen. Weitere Informationen finden Sie unter [Ändern Ihres Azure AD B2C-Tarifs](billing.md#change-your-azure-ad-pricing-tier).

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Vorteile von Identity Protection und bedingtem Zugriff für Azure AD B2C

Die Kombination aus Richtlinien für bedingten Zugriff und Identity Protection-Risikoerkennung ermöglicht es Ihnen, auf risikobehaftete Authentifizierungen mit der passenden Richtlinienaktion zu reagieren.

- **Profitieren Sie von mehr Transparenz im Zusammenhang mit Authentifizierungsrisiken für Ihre Apps und Ihre Kundenbasis.** Anhand von Signalen aus mehreren Milliarden monatlichen Authentifizierungen für Azure AD und Microsoft-Konten können die Risikoerkennungsalgorithmen nun Authentifizierungen für Ihre lokalen Consumer oder Bürger als niedriges, mittleres oder hohes Risiko kennzeichnen.
- **Behandeln Sie Risiken automatisch, indem Sie Ihre eigene adaptive Authentifizierung konfigurieren.** Für angegebene Anwendungen können Sie festlegen, dass bestimmte Benutzer einen zweiten Authentifizierungsfaktor verwenden müssen, wie etwa bei der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA). Alternativ können Sie den Zugriff basierend auf der erkannten Risikostufe blockieren. Genau wie bei anderen Azure AD B2C-Umgebungen können Sie die resultierende Endbenutzererfahrung an die Sprache, den Stil und die Marke Ihrer Organisation anpassen. Außerdem können Sie Abhilfealternativen anzeigen, wenn der Benutzer nicht zugreifen kann.
- **Steuern Sie den Zugriff basierend auf Standort, Gruppen und Apps.**  Bedingter Zugriff kann auch für die Steuerung in nicht risikobehafteten Situationen verwendet werden. So können Sie beispielsweise festlegen, dass Kunden, die auf eine bestimmte App zugreifen, eine mehrstufige Authentifizierung durchlaufen müssen, oder den Zugriff aus angegebenen geografischen Regionen blockieren.
- **Nutzen Sie die Integration mit Azure AD B2C-Benutzerflows und benutzerdefinierten IEF-Richtlinien (Identity Experience Framework).** Ergänzen Sie Ihre vorhandenen Umgebungen mit den Steuerelementen, die Sie für die Interaktion mit bedingtem Zugriff benötigen. Sie können auch erweiterte Szenarien für die Zugriffsgewährung implementieren, beispielsweise wissensbasierten Zugriff oder Ihren bevorzugten MFA-Anbieter.

## <a name="feature-differences-and-limitations"></a>Funktionsunterschiede und -einschränkungen

Identity Protection und bedingter Zugriff in Azure AD B2C funktionieren weitgehend wie in Azure AD, es gibt jedoch ein paar Ausnahmen:

- Microsoft Defender für Cloud ist in Azure AD B2C nicht verfügbar.

- Identity Protection und bedingter Zugriff werden für ROPC-Server-zu-Server-Flows in Azure AD B2C-Mandanten nicht unterstützt.

- In Azure AD B2C-Mandanten sind Identity Protection-Risikoerkennungen nur für lokale B2C-Konten, aber nicht für soziale Identitäten wie Google oder Facebook verfügbar.

- In Azure AD B2C-Mandanten steht nur ein Teil der Identity Protection-Risikoerkennungen zur Verfügung. Weitere Informationen finden Sie unter [Untersuchen eines Risikos mit Identity Protection](identity-protection-investigate-risk.md)und [Hinzufügen von bedingtem Zugriff zu Benutzerflows in Azure Active Directory B2C](conditional-access-user-flow.md).

- Das Gerätekompatibilitätsfeature des bedingten Zugriffs ist in Azure AD B2C-Mandanten nicht verfügbar.

## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Integrieren von bedingtem Zugriff in Benutzerflows und benutzerdefinierte Richtlinien

In Azure AD B2C können Bedingungen für bedingten Zugriff über integrierte Benutzerflows ausgelöst werden. Des Weiteren können Sie bedingten Zugriff in benutzerdefinierte Richtlinien integrieren. Das Messaging der Endbenutzererfahrung kann genau wie andere Aspekte des B2C-Benutzerflows an die Sprache, Marke und Abhilfealternativen Ihrer Organisation angepasst werden. Weitere Informationen finden Sie unter [Hinzufügen von bedingtem Zugriff zu Benutzerflows in Azure Active Directory B2C](conditional-access-user-flow.md).

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

Richtlinien für bedingten Zugriff in Azure AD B2C können auch mit der Microsoft Graph-API verwaltet werden. Ausführliche Informationen finden Sie unter [Was ist bedingter Zugriff?](../active-directory/conditional-access/overview.md) und in der [Microsoft Graph-Referenz](microsoft-graph-operations.md#conditional-access).

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen von bedingtem Zugriff zu Benutzerflows in Azure Active Directory B2C](conditional-access-user-flow.md)
- [Was ist Identity Protection?](../active-directory/identity-protection/overview-identity-protection.md)
- [Was ist bedingter Zugriff?](../active-directory/conditional-access/overview.md)
