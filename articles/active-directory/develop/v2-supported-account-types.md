---
title: Unterstützte Kontotypen | Azure
titleSuffix: Microsoft identity platform
description: Konzeptionelle Dokumentation über Zielgruppen und unterstützte Kontotypen in Anwendungen
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b72e911631bc15402cf504655426c63cd26a69d6
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231727"
---
# <a name="supported-account-types"></a>Unterstützte Kontotypen

In diesem Artikel wird erläutert, welche Kontotypen (manchmal als *Zielgruppen* bezeichnet) in Microsoft Identity Platform-Anwendungen unterstützt werden.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Kontotypen in der öffentlichen Cloud

In der öffentlichen Microsoft Azure-Cloud können die meisten App-Typen Benutzer mit beliebiger Zielgruppe anmelden:

- Wenn Sie eine Branchenanwendung schreiben, können Sie Benutzer in Ihrer eigenen Organisation anmelden. Eine solche Anwendung wird manchmal als *einzelner Mandant* bezeichnet.
- Wenn Sie ein ISV sind, können Sie eine Anwendung schreiben, die Benutzer anmeldet:

  - In einer beliebigen Organisation. Eine solche Anwendung wird als *mehrinstanzenfähige* Webanwendung bezeichnet. Bisweilen ist zu lesen, dass sie Benutzer mit deren Geschäfts-, Schul- oder Unikonten anmeldet.
  - Mit ihren Geschäfts-, Schul- oder Unikonten oder ihren persönlichen Microsoft-Konten.
  - Nur mit persönlichen Microsoft-Konten.
    
- Wenn Sie eine B2C-Anwendung schreiben, können Sie Benutzer über Azure Active Directory B2C (Azure AD B2C) auch mit Social Media-Identitäten anmelden.

## <a name="account-type-support-in-authentication-flows"></a>Unterstützung von Kontotypen in Authentifizierungsflows

Einige Kontotypen können mit bestimmten Authentifizierungsabläufen nicht verwendet werden. Auf dem Desktop betrifft dies beispielsweise UWP- oder Daemon-Anwendungen:

- Daemon-Anwendungen können nur mit Azure AD-Organisationen verwendet werden. Es wäre nicht sinnvoll, eine Daemon-Anwendung zu verwenden, um persönliche Microsoft-Konten zu manipulieren. Dazu würde niemals Administratorzustimmung erteilt.
- Sie können den integrierten Windows-Authentifizierungsflow nur mit Geschäfts-, Schul- oder Unikonten (in Ihrer oder einer anderen Organisation) verwenden. Die integrierte Windows-Authentifizierung funktioniert mit Domänenkonten und setzt voraus, dass die Computer in eine Domäne oder in Azure AD eingebunden sind. Dieser Flow ist für persönliche Microsoft-Konten nicht sinnvoll.
- Die [Gewährung der Kennwortanmeldeinformationen des Ressourcenbesitzers](./v2-oauth-ropc.md) (Benutzername/Kennwort) kann nicht mit persönlichen Microsoft-Konten verwendet werden. Persönliche Microsoft-Konten erfordern, dass der Benutzer bei jeder Anmeldesitzung dem Zugriff auf persönliche Ressourcen zustimmt. Daher ist dieses Verhalten mit nicht-interaktiven Flows nicht kompatibel.

## <a name="account-types-in-national-clouds"></a>Kontotypen in nationalen Clouds

Apps können Benutzer auch in [nationalen Clouds](authentication-national-cloud.md) anmelden. Allerdings werden persönliche Microsoft-Konten in diesen Clouds nicht unterstützt. Deshalb sind die unterstützten Kontotypen für diese Clouds auf Ihre Organisation (einzelner Mandant) oder beliebige Organisationen (mehrinstanzenfähige Anwendungen) beschränkt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Mandanten in Azure Active Directory](./single-and-multi-tenant-apps.md)
- Weitere Informationen zu [nationalen Clouds](./authentication-national-cloud.md)
