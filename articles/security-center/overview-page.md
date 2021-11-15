---
title: Hauptdashboard oder Seite Übersichtsseite von Microsoft Defender für Cloud
description: Erfahren Sie mehr über die Features der Übersichtsseite von Defender für Cloud.
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 640b6fedd532174ff86e222a7479007b9e72b5bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437826"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Übersichtsseite von Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie Microsoft Defender für Cloud öffnen, wird als erste Seite die Übersichtsseite angezeigt. 

Dieses interaktive Dashboard ermöglicht eine einheitliche Übersicht über den Sicherheitsstatus Ihrer Hybrid Cloud-Workloads. Darüber hinaus werden darauf Sicherheitswarnungen, Informationen zur Abdeckung und andere Infos angezeigt.

Sie können ein beliebiges Element auf der Seite auswählen, um ausführlichere Informationen zu erhalten.

:::image type="content" source="./media/overview-page/overview.png" alt-text="Screenshot: Übersichtsseite von Microsoft Defender für Cloud" lightbox="./media/overview-page/overview.png":::
## <a name="features-of-the-overview-page"></a>Features der Übersichtsseite

:::image type="content" source="./media/overview-page/top-bar-of-overview.png" alt-text="Screenshot: Obere Leiste auf der Übersichtsseite von Microsoft Defender für Cloud":::

### <a name="metrics"></a>Metriken
Die **obere Menüleiste** enthält Folgendes:
- **Abonnements**: Sie können die Liste mit den Abonnements anzeigen und filtern, indem Sie diese Schaltfläche auswählen. Die Anzeige wird angepasst, um den Sicherheitsstatus der ausgewählten Abonnements widerzuspiegeln.
- **Neuigkeiten**: Öffnet die [Versionshinweise](release-notes.md), damit Sie sich über den neuesten Stand der neuen Features, Fehlerbehebungen und veralteten Funktionen informieren können.
- **Allgemeine Zahlenwerte** für die verbundenen Cloudkonten, um den Kontext zu den Informationen in den darunter angeordneten Kacheln anzuzeigen. Darüber hinaus wird die Anzahl der bewerteten Ressourcen, aktiven Empfehlungen und Sicherheitswarnungen gezeigt. Wählen Sie die Anzahl der bewerteten Ressourcen aus, um auf [Ressourcenbestand](asset-inventory.md) zuzugreifen. Informieren Sie sich darüber, wie Sie Ihre [AWS-Konten](quickstart-onboard-aws.md) und [GCP-Projekte](quickstart-onboard-gcp.md) verbinden.

### <a name="feature-tiles"></a>Funktionskacheln
In der Mitte der Seite befinden sich **Funktionskacheln**, die jeweils mit wichtigen Funktionen oder einem dedizierten Dashboard verlinkt sind:
- **Sicherheitsbewertung**: Defender für Cloud führt eine ständige Bewertung Ihrer Ressourcen, Abonnements und Organisation in Bezug auf Sicherheitsprobleme durch. Anschließend werden alle Ergebnisse in einer einzigen Bewertung zusammengefasst, sodass Sie auf einen Blick Ihre aktuelle Sicherheitssituation erkennen können: je höher die Bewertung, desto geringer das ermittelte Risiko. [Weitere Informationen](secure-score-security-controls.md)
- **Workloadschutz**: Dies ist die Cloudworkload-Schutzplattform (Cloud Workload Protection Platform, CWPP), die in Defender für Cloud integriert ist, um Ihren Workloads, die auf Azure, lokalen Computern oder anderen Cloudanbietern ausgeführt werden, erweiterten und intelligenten Schutz bereitzustellen. Für jeden Ressourcentyp gibt es einen entsprechenden Microsoft Defender-Plan. Auf der Kachel wird die Abdeckung für Ihre verbundenen Ressourcen (für die derzeit ausgewählten Abonnements) und die letzten Warnungen angezeigt, die je nach Schweregrad mit einer bestimmten Farbe gekennzeichnet sind. Weitere Informationen finden Sie unter [Die erweiterten Sicherheitsfunktionen von Microsoft Defender für Cloud](enhanced-security-features-overview.md).
- **Konformität**: Defender für Cloud stellt basierend auf fortlaufenden Bewertungen Ihrer Azure-Umgebung Informationen zu Ihrem Konformitätsstatus (Compliance) bereit. Mit Defender für Cloud werden Risikofaktoren in Ihrer Umgebung anhand der bewährten Methoden für die Sicherheit analysiert. Diese Bewertungen sind Konformitätskontrollen zugeordnet, die aus dem unterstützten Standardsatz stammen. [Weitere Informationen](regulatory-compliance-dashboard.md).
- **Firewall Manager**: Diese Kachel zeigt den Status Ihrer Hubs und Netzwerke in [Azure Firewall Manager](../firewall-manager/overview.md).
- **Bestand**: Die Seite mit dem Ressourcenbestand von Microsoft Defender für Cloud bietet eine einzelne, zentrale Ansicht des Sicherheitsstatus der Ressourcen, die Sie mit Microsoft Defender für Cloud verknüpft haben. Alle Ressourcen mit nicht aufgelösten Sicherheitsempfehlungen werden im Bestand angezeigt. Wenn Sie die Integration mit Microsoft Defender für Endpunkt und Microsoft Defender für Server aktiviert haben, haben Sie außerdem Zugriff auf den Softwarebestand. Auf der Kachel auf der Übersichtsseite werden alle fehlerfreien und fehlerhaften Ressourcen (für die derzeit ausgewählten Abonnements) auf einen Blick angezeigt. [Weitere Informationen](asset-inventory.md).
- **Informationsschutz**: Ein Diagramm auf dieser Kachel zeigt die Ressourcentypen, die von [Azure Purview](../purview/overview.md) überprüft wurden, vertrauliche Daten enthalten und über ausstehende Empfehlungen und Warnungen verfügen. Greifen Sie über den Link **Überprüfen** auf die Azure Purview-Konten zu, um neue Überprüfungen zu konfigurieren, oder wählen Sie einen anderen Teil der Kachel aus, um den [Ressourcenbestand](asset-inventory.md) zu öffnen und Ihre Ressourcen gemäß Ihren Vertraulichkeitsklassifizierungen für Purview-Daten anzuzeigen. [Weitere Informationen](information-protection.md).

### <a name="insights"></a>Einblicke
Der Bereich **Insights** enthält angepasste Elemente für Ihre Umgebung, die beispielsweise folgende Informationen liefern:
- Ihre Ressourcen mit den meisten Angriffen
- Ihre [Sicherheitskontrollen](secure-score-security-controls.md) mit dem höchsten Potenzial für die Erhöhung Ihrer Sicherheitsbewertung
- Aktive Empfehlungen mit den meisten betroffenen Ressourcen
- Aktuelle Blogbeiträge von Microsoft Defender für Cloud-Experten

## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde die Übersichtsseite von Defender für Cloud vorgestellt. Verwandte Informationen

- [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md)
- [Sicherheitsbewertung in Microsoft Defender für Cloud](secure-score-security-controls.md)
