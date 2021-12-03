---
title: Feature für die automatische Skalierung für den FHIR-Dienst der Azure Healthcare-APIs
description: In diesem Artikel wird das Feature für die automatische Skalierung für den FHIR-Dienst der Azure Healthcare-APIs beschrieben.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: zxue
ms.openlocfilehash: 382a3ccee6f14c4d95e74bb40a8d899868754926
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814762"
---
# <a name="fhir-service-autoscale"></a>Automatische Skalierung des FHIR-Diensts

Der FHIR-Dienst in Azure Healthcare-APIs ist ein verwalteter Dienst, mit dem Kunden FHIR-konforme Gesundheitsdaten beibehalten und über den API-Dienstendpunkt sicher damit interagieren können. Der FHIR-Dienst bietet die integrierte Funktion für die automatische Skalierung, um verschiedene Workloads zu erfüllen.  

## <a name="what-is-fhir-service-autoscale"></a>Was ist die automatische Skalierung des FHIR-Diensts?   

Das Feature für die automatische Skalierung für den FHIR-Dienst ist so konzipiert, dass es eine optimierte Dienstskalierbarkeit bietet, die kundenspezifischen Anforderungen automatisch entspricht, wenn Datentransaktionen zu einem beliebigen Zeitpunkt in konsistenten oder verschiedenen Workloads ausgeführt werden. Sie ist in allen Regionen verfügbar, in denen der FHIR-Dienst unterstützt wird. Beachten Sie, dass die Funktion für die automatische Skalierung den in Azure-Regionen verfügbaren Ressourcen unterliegt.   

## <a name="how-does-fhir-service-autoscale-work"></a>Wie funktioniert die automatische Skalierung des FHIR-Diensts?  

Wenn Transaktionsworkloads hoch sind, erhöht das Feature für die automatische Skalierung automatisch die Computingressourcen. Wenn die Transaktionsworkloads niedrig sind, werden die Computingressourcen entsprechend verringert.  

Das Feature für die automatische Skalierung passt Computingressourcen automatisch an, um die allgemeine Skalierbarkeit des Diensts zu optimieren. Unabhängig davon, ob Sie Leseanforderungen ausführen, die einfache Abfragen wie das Abrufen von Patienteninformationen mithilfe einer Patienten-ID und erweiterte Abfragen wie das Abrufen aller Ressourcen für Patienten mit dem Namen "Sarah" oder das Erstellen oder Aktualisieren von `DiagnosticReport` FHIR-Ressourcen umfassen, verwaltet das Feature für die automatische Skalierung die Dynamics und Komplexität der Ressourcenzuordnung, um eine hohe Skalierbarkeit sicherzustellen.

Das Feature für die automatische Skalierung ist Teil des verwalteten Diensts und erfordert keine Maßnahmen von Kunden. Kunden wird jedoch empfohlen, ihr Feedback zu teilen, um das Feature zu verbessern. Kunden können auch ein Supportticket ausstellen, um mögliche Skalierbarkeitsproblem zu beheben.  

### <a name="what-is-the-cost-of-the-fhir-service-autoscale"></a>Welche Kosten entstehen durch die automatische Skalierung des FHIR-Diensts?  

Das Feature für die automatische Skalierung verursacht keine zusätzlichen Kosten für Kunden basierend auf dem neuen API-Abrechnungsmodell.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über das Feature für die automatische Skalierung des FHIR-Diensts in Azure Healthcare-APIs erfahren. Weitere Informationen zu den vom FHIR-Dienst unterstützten Features finden Sie unter

>[!div class="nextstepaction"]
>[Unterstützte FHIR-Features](fhir-features-supported.md)
