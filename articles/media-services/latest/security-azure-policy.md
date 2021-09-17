---
title: Integrierte Unterstützung in Media Services für Azure Policy
description: In diesem Artikel wird die integrierte Unterstützung von Azure Policy für Azure Media Services-Szenarien beschrieben.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 88b1907d0c767d77dd4a1f2e38ab859d4147ea96
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429882"
---
# <a name="azure-policy-for-media-services"></a>Azure Policy für Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services bietet mehrere integrierte [Azure Policy](../../governance/policy/overview.md)-Definitionen, die die Durchsetzung von Unternehmensstandards und Compliance im großen Stil unterstützen.
Häufige Anwendungsfälle für Azure Policy sind die Implementierung von Governance für Ressourcenkonsistenz, Einhaltung gesetzlicher Bestimmungen, Sicherheit, Kosten und Verwaltung.

Media Services enthält mehrere allgemeine Falldefinitionen für Azure Policy, die integriert sind, um Ihnen die ersten Schritte zu erleichtern.

## <a name="built-in-azure-policy-definitions-for-media-services"></a>Integrierte Azure Policy-Definitionen für Media Services

Für die Verwendung mit Media Services stehen mehrere integrierte Definitionen für Anwendungsfälle zur Verfügung, die Ihnen den Einstieg erleichtern und es Ihnen ermöglichen, Ihre eigenen Richtlinien zu definieren.

[!INCLUDE [Azure Policy Media Services](../../../includes/policy/reference/bycat/policies-media-services.md)]

Die [Liste der integrierten Richtliniendefinitionen für Media Services](../../governance/policy/samples/built-in-policies.md#media-services) enthält die neuesten Definitionen und Links zu den Codedefinitionen und zur Vorgehensweise beim Zugreifen auf das Portal.

## <a name="common-scenarios-that-require-azure-policy"></a>Häufige Szenarien, die Azure Policy erfordern

* Wenn die Sicherheit Ihres Unternehmens es erfordert, dass Sie sicherstellen, dass alle Media Services-Konten mit Private Links erstellt werden, können Sie mithilfe einer Richtliniendefinition sicherstellen, dass Konten nur mit der API 2020-05-01 (oder höher) erstellt werden, um den Zugriff auf die ältere REST v2-API zu deaktivieren und auf das Private Link-Feature zuzugreifen.
* Wenn Sie bestimmte Optionen für die Token durchsetzen möchten, die für Richtlinien von symmetrischen Schlüsseln verwendet werden, kann eine Azure Policy-Definition erstellt werden, um die spezifischen Anforderungen zu unterstützen.
* Wenn Ihre Sicherheitsziele es erfordern, dass eine Auftragseingabequelle nur von Ihren vertrauenswürdigen Speicherkonten stammen darf und der Zugriff auf externe HTTP(S)-Eingaben mithilfe von „JobInputHttp“ eingeschränkt werden muss, kann eine Azure-Richtlinie erstellt werden, um das URI-Eingabemuster zu begrenzen.

## <a name="example-policy-definitions"></a>Beispiele für Richtliniendefinitionen

Azure Media Services verwaltet und veröffentlicht eine Reihe von Azure Policy-Beispieldefinitionen in GitHub.
Weitere Informationen finden Sie in den Beispielen für [integrierte Richtliniendefinitionen für Media Services](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policyDefinitions/Media%20Services) im GitHub-Repository „azure-policy“.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Was ist Azure Policy?](../../governance/policy/overview.md)
- [Schnellstart: Erstellen einer Richtlinie im Portal](../../governance/policy/assign-policy-portal.md)
- [Liste der integrierten Richtliniendefinitionen für Media Services](../../governance/policy/samples/built-in-policies.md#media-services)

## <a name="next-steps"></a>Nächste Schritte

- [Entwickeln mit Media Services v3-APIs](media-services-apis-overview.md)
- [Rollenbasierte Zugriffssteuerung in Media Services](security-rbac-concept.md)
- [Schrittanleitung für Private Link mit Media Services](security-private-link-how-to.md)