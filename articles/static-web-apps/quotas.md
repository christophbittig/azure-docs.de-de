---
title: Kontingente in Azure Static Web Apps
description: Hier finden Sie Informationen zu Kontingenten im Zusammenhang mit Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5030b0fb4e714be221e28e590c463186ec81ad04
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515584"
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
| Autorisierung (mit benutzerdefinierten Rollen und Routingregeln) | Maximal 25 Endbenutzer, die möglicherweise zu benutzerdefinierten Rollen gehören | Maximal 25 Endbenutzer, die möglicherweise zu benutzerdefinierten Rollen gehören |

## <a name="github-storage"></a>GitHub-Speicher

Von GitHub Actions und Packages wird GitHub-Speicher beansprucht, wofür wiederum eigene Kontingente gelten. Wenn Sie eine Azure Static Web Apps-Website erstellen, werden die Artefakte auch nach der Bereitstellung noch von GitHub gespeichert.

Ausführlichere Informationen finden Sie in den folgenden Ressourcen:

- [Verwalten des Speicherplatzes von Actions](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [Informationen zur Abrechnung für GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Verwalten Ihres Ausgabenlimits für GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht](overview.md)
