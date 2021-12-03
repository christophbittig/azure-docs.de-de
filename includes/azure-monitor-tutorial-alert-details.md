---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 9db1bd652fed38cb03bca6a590ea217d0fadf169
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350834"
---
Im Abschnitt **Details zur Warnungsregel** konfigurieren Sie verschiedene Einstellungen für die Warnungsregel.

- **Name der Warnungsregel**: Geben Sie einen aussagekräftigen Namen an, da dieser beim Auslösen der Warnung angezeigt wird. 
- Geben Sie optional eine **Beschreibung** ein, die in den Details der Warnung enthalten ist.
- **Abonnement** und **Ressourcengruppe**, in denen die Warnungsregel gespeichert wird. Hierbei muss es sich nicht um dieselbe Ressourcengruppe handeln, in der sich die überwachte Ressource befindet.
- **Schweregrad** für die Warnung. Mit dem Schweregrad können Warnungen mit einer ähnlichen relativen Wichtigkeit gruppiert werden. Der Schweregrad **Fehler** ist für einen nicht reagierenden virtuellen Computer geeignet.
- Lassen Sie das Kontrollkästchen **Enable alert upon creation** (Warnung bei Erstellung aktivieren) aktiviert.
- Lassen Sie das Kontrollkästchen **Warnungen automatisch auflösen** aktiviert. Dadurch wird die Warnung automatisch aufgelöst, wenn der Metrikwert unter den Schwellenwert fällt. 
