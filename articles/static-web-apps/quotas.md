---
title: Kontingente in Azure Static Web Apps
description: Hier finden Sie Informationen zu Kontingenten im Zusammenhang mit Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 10/13/2021
ms.author: cshoe
ms.openlocfilehash: 4565012ec824961f971f1f1fb34ab330ae11372c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999300"
---
# <a name="quotas-in-azure-static-web-apps"></a>Kontingente in Azure Static Web Apps

Für Azure Static Web Apps gelten folgende Kontingente.

| Funktion                     | Free-Plan        | Standard-Plan |
|-----------------------------|------------------|---------------|
| Enthaltene Bandbreite          | 100 GB pro Monat und Abonnement | 100 GB pro Monat und Abonnement |
| Zusätzliche Bandbreite           | Nicht verfügbar      | 0,20 USD pro GB |
| Apps pro Azure-Abonnement | 10               | Unbegrenzt |
| App-Größe                    | 250 MB           | 500 MB |
| Plangröße                   | Maximale App-Größe von 500 MB für eine einzelne Bereitstellung und 0,50 GB für alle Staging- und Produktionsumgebungen  | Maximale App-Größe von 500 MB für eine einzelne Bereitstellung und 2,00 GB kombiniert in allen Staging- und Produktionsumgebungen |
| Präproduktionsumgebungen | 3                | 10 |
| Benutzerdefinierte Domänen              | 2 pro App        | 5 pro App |
| Autorisierung (integrierte Rollen) | Unbegrenzte Anzahl von Endbenutzern, die sich mit der integrierten Rolle `authenticated` authentifizieren können | Unbegrenzte Anzahl von Endbenutzern, die sich mit der integrierten Rolle `authenticated` authentifizieren können |
| Autorisierung (benutzerdefinierte Rollen) | Maximal 25 Endbenutzer, die über [Einladungen](authentication-authorization.md?tabs=invitations#role-management) möglicherweise zu benutzerdefinierten Rollen gehören | Maximal 25 Endbenutzer, die über [Einladungen](authentication-authorization.md?tabs=invitations#role-management) möglicherweise zu benutzerdefinierten Rollen gehören, oder unbegrenzte Anzahl von Endbenutzern, denen über die [serverlose Funktion](authentication-authorization.md?tabs=function#role-management) benutzerdefinierte Rollen zugewiesen werden können |

## <a name="github-storage"></a>GitHub-Speicher

Von GitHub Actions und Packages wird GitHub-Speicher beansprucht, wofür wiederum eigene Kontingente gelten. Wenn Sie eine Azure Static Web Apps-Website erstellen, werden die Artefakte auch nach der Bereitstellung noch von GitHub gespeichert.

Ausführlichere Informationen finden Sie in den folgenden Ressourcen:

- [Verwalten des Speicherplatzes von Actions](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [Informationen zur Abrechnung für GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Verwalten Ihres Ausgabenlimits für GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht](overview.md)
