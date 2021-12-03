---
title: Chaos-Experimente in Azure Chaos Studio
description: Einblicke in das Konzept eines Chaos-Experiments in Azure Chaos Studio. Was sind die Bestandteile eines Chaos-Experiments? Wie kann ich ein Chaos-Experiment erstellen?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: ef71d6d09d1965eefd7008dd8c3d020c0a8ae1ae
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371541"
---
# <a name="chaos-experiments"></a>Chaos-Experimente

In Chaos Studio erstellen Sie Chaos-Experimente und führen sie aus. Ein Chaos-Experiment ist eine Azure-Ressource, die die Fehler beschreibt, die ausgeführt werden sollen, und die Ressourcen, für die diese Fehler ausgeführt werden sollen. Ein Experiment ist in zwei Abschnitte unterteilt:
- **Selektoren**: Selektoren sind Gruppen von Zielressourcen, für die Fehler oder andere Aktionen ausgeführt werden. Mit einem Selektor können Sie Ressourcen logisch gruppieren, um sie für mehrere Aktionen wiederzuverwenden. Beispielsweise können Sie über einen Selektor mit dem Namen "AllNonProdEastUSVMs" verfügen, in dem Sie alle nicht für die Produktion bestimmten virtuellen Computer in "USA, Osten" hinzugefügt haben. Anschließend können Sie die CPU-Auslastung gefolgt von einer virtuellen Arbeitsspeicherdruck-Auslastung auf diese virtuellen Computer anwenden, indem Sie einfach auf den Selektor verweisen.
- **Logik**: Der Rest des Experiments beschreibt, wie und wann Fehler ausgeführt werden. Ein Experiment ist in Schritte **unterteilt,** die nacheinander ausgeführt werden. Jeder Schritt verfügt über mindestens eine **Verzweigung**, die gleichzeitig ausgeführt wird. Mit Schritten und Verzweigungen können Sie mehrere Fehler ressourcenübergreifend in Ihre Umgebung parallel einspeisen. Jede Verzweigung verfügt über eine oder mehrere **Aktionen**, bei denen es sich entweder um die Fehler handelt, die Sie ausführen möchten, oder um Zeitverzögerungen. **Fehler sind** Aktionen, die zu Unterbrechungen führen. Die meisten Fehler nehmen einen oder mehrere **Parameter** an, z. B. die Dauer der Ausführung des Fehlers oder die zu übernehmende Belastung.

![Diagramm, das das Layout eines Chaos-Experiments zeigt.](images/chaos-experiment.png)

## <a name="cross-subscription-and-cross-tenant-experiments"></a>Abonnementübergreifende und inhaberübergreifende Experimente

Ein Chaos-Experiment ist eine Azure-Ressource, die in einem Abonnement, einer Ressourcengruppe und einer Region bereitgestellt wird. Sie können das Azure-Portal oder die Chaos Studio-REST-API verwenden, um den Status eines Experiments zu erstellen, zu aktualisieren, zu starten, abzubrechen und anzuzeigen.

Chaos-Experimente können auf Ressourcen in einem anderen Abonnement als das Experiment abzielen, solange sich das Abonnement innerhalb desselben Azure-Inhabers befindet. Chaos-Experimente können auf Ressourcen in einer anderen Region als das Experiment abzielen, solange die Region eine unterstützte Region für Chaos Studio ist.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun wissen, was ein Chaos-Experiment ist, sind Sie bereit hierfür:
- [Erfahren Sie mehr über Fehler und Aktionen](chaos-studio-faults-actions.md)
- [Erstellen und Ausführen des ersten Experiments](chaos-studio-tutorial-service-direct-portal.md)
