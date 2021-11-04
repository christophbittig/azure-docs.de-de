---
title: Hauptdashboard oder Seite Übersichtsseite von Microsoft Defender für Cloud
description: Erfahren Sie mehr über die Features der Übersichtsseite von Defender für Cloud.
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0ab115e1e963283a2b8f7e7abd963653b9a39dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084080"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Übersichtsseite von Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie Microsoft Defender für Cloud öffnen, wird als erste Seite die Übersichtsseite angezeigt. 

Dieses interaktive Dashboard ermöglicht eine einheitliche Übersicht über den Sicherheitsstatus Ihrer Hybrid Cloud-Workloads. Darüber hinaus werden darauf Sicherheitswarnungen, Informationen zur Abdeckung und andere Infos angezeigt.

Sie können ein beliebiges Element auf der Seite auswählen, um ausführlichere Informationen zu erhalten.

:::image type="content" source="media/overview-page/overview.png" alt-text="Übersichtsseite von Defender für Cloud":::

## <a name="features-of-the-overview-page"></a>Features der Übersichtsseite

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Obere Leiste der Übersichtsseite von Defender für Cloud":::

Die **obere Menüleiste** enthält Folgendes:
- **Abonnements**: Sie können die Liste mit den Abonnements anzeigen und filtern, indem Sie diese Schaltfläche auswählen. Die Anzeige wird angepasst, um den Sicherheitsstatus der ausgewählten Abonnements widerzuspiegeln.
- **Neuigkeiten**: Öffnet die [Versionshinweise](release-notes.md), damit Sie sich über den neuesten Stand der neuen Features, Fehlerbehebungen und veralteten Funktionen informieren können.
- **Allgemeine Zahlenwerte** für die verbundenen Cloudkonten, um den Kontext zu den Informationen in den darunter angeordneten Kacheln anzuzeigen. Darüber hinaus wird die Anzahl der bewerteten Ressourcen, aktiven Empfehlungen und Sicherheitswarnungen gezeigt. Wählen Sie die Anzahl der bewerteten Ressourcen aus, um auf [Ressourcenbestand](asset-inventory.md) zuzugreifen. Informieren Sie sich darüber, wie Sie Ihre [AWS-Konten](quickstart-onboard-aws.md) und [GCP-Projekte](quickstart-onboard-gcp.md) verbinden.


In der Mitte der Seite befinden sich **vier zentrale Kacheln**, die jeweils mit einem dedizierten Dashboard mit weiteren Details verlinkt sind:
- **Sicherheitsbewertung**: Defender für Cloud führt eine ständige Bewertung Ihrer Ressourcen, Abonnements und Organisation in Bezug auf Sicherheitsprobleme durch. Anschließend werden alle Ergebnisse in einer einzigen Bewertung zusammengefasst, sodass Sie auf einen Blick Ihre aktuelle Sicherheitssituation erkennen können: je höher die Bewertung, desto geringer das ermittelte Risiko. [Weitere Informationen](secure-score-security-controls.md)
- **Konformität**: Defender für Cloud stellt basierend auf fortlaufenden Bewertungen Ihrer Azure-Umgebung Informationen zu Ihrem Konformitätsstatus (Compliance) bereit. Mit Defender für Cloud werden Risikofaktoren in Ihrer Hybrid Cloud-Umgebung anhand der bewährten Methoden für die Sicherheit analysiert. Diese Bewertungen sind Konformitätskontrollen zugeordnet, die aus dem unterstützten Standardsatz stammen. [Weitere Informationen](regulatory-compliance-dashboard.md).
- **Workloadschutz**: Dies ist die Cloudworkload-Schutzplattform (Cloud Workload Protection Platform, CWPP), die in Defender für Cloud integriert ist, um Ihren Workloads, die auf Azure, lokalen Computern oder anderen Cloudanbietern ausgeführt werden, erweiterten und intelligenten Schutz bereitzustellen. Für jeden Ressourcentyp gibt es einen entsprechenden Microsoft Defender-Plan. Auf der Kachel wird die Abdeckung für Ihre verbundenen Ressourcen (für die derzeit ausgewählten Abonnements) und die letzten Warnungen angezeigt, die je nach Schweregrad mit einer bestimmten Farbe gekennzeichnet sind. Erfahren Sie mehr über [die erweiterten Schutzpläne von Microsoft Defender](defender-for-cloud-introduction.md).
- **Firewall Manager**: Diese Kachel zeigt den Status Ihrer Hubs und Netzwerke in [Azure Firewall Manager](../firewall-manager/overview.md). 


Der Bereich **Insights** enthält angepasste Elemente für Ihre Umgebung, die beispielsweise folgende Informationen liefern:
- Ihre Ressourcen mit den meisten Angriffen
- Ihre Sicherheitskontrollen mit dem höchsten Potenzial für die Erhöhung Ihrer Sicherheitsbewertung
- Aktive Empfehlungen mit den meisten betroffenen Ressourcen
- Aktuelle Blogbeiträge von Microsoft Defender für Cloud-Experten

## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde die Übersichtsseite von Defender für Cloud vorgestellt. Verwandte Informationen

- [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md)
- [Sicherheitsbewertung in Microsoft Defender für Cloud](secure-score-security-controls.md)
