---
title: Datei einfügen
description: include file
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 07/13/2021
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 7ea469c4d549d968745f08a5eff341bbc650854e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201052"
---
- Jede verwaltete Identität wird auf den Objektkontingentgrenzwert in einem Azure AD-Mandanten angerechnet, wie unter [Dienst- und andere Einschränkungen für Azure AD](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md) beschrieben.
-   Für die Rate, mit der verwaltete Identitäten erstellt werden können, gelten die folgenden Grenzwerte:

    1. Pro Azure AD-Mandant pro Azure-Region: 400 Erstellvorgänge pro 20 Sekunden
    2. Pro Azure-Abonnement pro Azure-Region: 80 Erstellvorgänge pro 20 Sekunden

-   Dies ist die Rate, mit der die benutzerseitig zugewiesene verwaltete Identität einer Azure-Ressource zugewiesen werden kann:

    1. Pro Azure AD-Mandant pro Azure-Region: 400 Zuweisungsvorgänge pro 20 Sekunden
    2. Pro Azure-Abonnement pro Azure-Region: 300 Zuweisungsvorgänge pro 20 Sekunden




