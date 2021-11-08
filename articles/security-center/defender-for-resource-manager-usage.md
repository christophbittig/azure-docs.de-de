---
title: Reagieren auf Warnungen von Microsoft Defender für Resource Manager
description: Enthält eine Beschreibung der erforderlichen Schritte zum Reagieren auf Warnungen von Microsoft Defender für Resource Manager.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 913f1f07a588842d1cc2b4a845d5e9ff025b77fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453115"
---
# <a name="respond-to-microsoft-defender-for-resource-manager-alerts"></a>Reagieren auf Warnungen von Microsoft Defender für Resource Manager

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie eine Warnung von Microsoft Defender für Resource Manager erhalten, empfehlen wir Ihnen, die Warnung wie unten beschrieben zu untersuchen und entsprechend darauf zu reagieren. Microsoft Defender für Resource Manager schützt alle verbundenen Ressourcen. Auch wenn Sie mit der Anwendung oder dem Benutzer vertraut sind, von der bzw. dem die Warnung ausgelöst wurde, sollten Sie bei jeder Warnung die Situation aber unbedingt überprüfen.  


## <a name="step-1-contact"></a>Schritt 1: Contact

1. Wenden Sie sich an den Ressourcenbesitzer, um zu bestimmen, ob das Verhalten erwartet oder beabsichtigt war.
1. Wenn die Aktivität erwartet wird, schließen Sie die Warnung.
1. Wenn die Aktivität unerwartet ist, behandeln Sie die betroffenen Benutzerkonten, Abonnements und virtuellen Computer als kompromittiert und mindern Sie das Risiko, wie im nächsten Schritt beschrieben.

## <a name="step-2-immediate-mitigation"></a>Schritt 2: Sofortige Risikominderung 

1. Risikominderung für kompromittierte Benutzerkonten:
    - Wenn sie nicht bekannt sind, löschen Sie sie, da sie möglicherweise von einem Angreifer erstellt wurden.
    - Wenn sie bekannt sind, ändern Sie ihre Anmeldeinformationen für die Authentifizierung.
    - Verwenden Sie Azure-Aktivitätsprotokolle, um alle von dem Benutzer ausgeführten Aktivitäten zu überprüfen und dabei alle verdächtigen Aktionen zu identifizieren.

1. Risikominderung für kompromittierte Abonnements:
    - Entfernen Sie alle unbekannten Runbooks aus dem kompromittierten Automation-Konto.
    - Überprüfen Sie die IAM-Berechtigungen für das Abonnement, und entfernen Sie Berechtigungen für alle unbekannten Benutzerkonten.
    - Überprüfen Sie alle Azure-Ressourcen im Abonnement, und löschen Sie alle unbekannten.
    - Überprüfen und untersuchen Sie alle Sicherheitswarnungen für das Abonnement in Microsoft Defender für Cloud.
    - Verwenden Sie Azure-Aktivitätsprotokolle, um alle in dem Abonnement ausgeführten Aktivitäten zu überprüfen und dabei alle verdächtigen Aktionen zu identifizieren.

1. Korrigieren Sie die kompromittierten virtuellen Computer.
    - Ändern Sie die Kennwörter für alle Benutzer.
    - Führen Sie eine vollständige Antischadsoftwareüberprüfung auf dem Computer aus.
    - Führen Sie ein Reimaging der Computer aus einer schadsoftwarefreien Quelle durch.


## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde die Reaktion auf eine Warnung von Microsoft Defender für Resource Manager beschrieben. Zugehörige Informationen finden Sie auf den folgenden Seiten:

- [Einführung in Microsoft Defender für Resource Manager](defender-for-resource-manager-introduction.md)
- [Unterdrücken von Sicherheitswarnungen](alerts-suppression-rules.md)
- [Fortlaufender Export von Defender für Cloud-Daten](continuous-export.md)