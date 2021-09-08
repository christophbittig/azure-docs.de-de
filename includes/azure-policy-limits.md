---
title: Datei einfügen
description: include file
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: e9ba8efefd2238b1aadc2fbeaf17a286e0714bcd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123485076"
---
Für jeden Objekttyp für Azure Policy gilt eine maximale Anzahl. Bei Definitionen gilt ein _Scope_-Eintrag (Bereich) entweder für die [Verwaltungsgruppe](../articles/governance/management-groups/overview.md) oder das Abonnement.
Bei Zuweisungen und Ausnahmen gilt ein _Scope_-Eintrag für die [Verwaltungsgruppe](../articles/governance/management-groups/overview.md), das Abonnement, die Ressourcengruppe oder eine einzelne Ressource.

| Hierbei gilt: | Was | Maximale Anzahl |
|---|---|---|
| `Scope` | Richtliniendefinitionen | 500 |
| `Scope` | Initiativdefinitionen | 200 |
| Tenant | Initiativdefinitionen | 2\.500 |
| `Scope` | Richtlinien- oder Initiativenzuweisungen | 200 |
| Bereich | Ausnahmen | 1000 |
| Richtliniendefinition | Parameter | 20 |
| Initiativdefinition | Richtlinien | 1000 |
| Initiativdefinition | Parameter | 300 |
| Richtlinien- oder Initiativenzuweisungen | Ausschlüsse (notScopes) | 400 |
| Richtlinienregel | Geschachtelte konditionelle Abschnitte | 512 |
| Wartungstask | Ressourcen | 500 |
