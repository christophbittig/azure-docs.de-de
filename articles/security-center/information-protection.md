---
title: 'Priorisieren von Sicherheitsaktionen nach Datenvertraulichkeit: Microsoft Defender für Cloud'
description: Verwenden der Datenvertraulichkeitsklassifizierungen von Azure Purview in Microsoft Defender für Cloud
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 7f82750d9b02bba6e843390562ae2eee060cef47
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479706"
---
# <a name="prioritize-security-actions-by-data-sensitivity"></a>Priorisieren von Sicherheitsaktionen nach Datenvertraulichkeit

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Mit [Azure Purview](../purview/overview.md), dem Microsoft-Dienst für Datengovernance, erhalten Sie umfassende Erkenntnisse zur *Vertraulichkeit Ihrer Daten*. Mit der automatisierten Datenermittlung, der Klassifizierung vertraulicher Daten und der End-to-End-Datenherkunft unterstützt Purview Organisationen bei der Verwaltung und Steuerung von Daten in Hybrid- und Multicloudumgebungen.

Kunden mit Microsoft Defender für Cloud, die Azure Purview verwenden, können von einer zusätzlichen wichtigen Metadatenebene in Warnungen und Empfehlungen profitieren: Informationen zu potenziell vertraulichen Daten. Dieses Wissen trägt dazu bei, das Problem der Selektierung zu lösen, und stellt sicher, dass sich Sicherheitsexperten auf die Bedrohungen für vertrauliche Daten konzentrieren können.

Auf dieser Seite wird die Integration der Purview-Bezeichnungen für Datenvertraulichkeitsklassifizierungen in Defender für Cloud erläutert.

## <a name="availability"></a>Verfügbarkeit
|Aspekt|Details|
|----|:----|
|Status des Release:|Vorschau.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Preise:|Sie benötigen ein Azure Purview-Konto, um die Datenvertraulichkeitsklassifizierungen zu erstellen und die Überprüfungen auszuführen. Das Anzeigen der Überprüfungsergebnisse und die Verwendung der Ausgabe ist für Benutzer von Defender für Cloud kostenlos.|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsadministrator** und **Security contributor** (Sicherheitsmitwirkender)|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet (**Teilweise:** Teilmenge von Warnungen und Sicherheitsrisikobewertungen für SQL-Server. Verhaltensbedingte Bedrohungsschutzmaßnahmen sind nicht verfügbar.)|
|||


## <a name="the-triage-problem-and-defender-for-clouds-solution"></a>Das Selektierungsproblem und die Lösung von Defender für Cloud
Sicherheitsteams stehen regelmäßig vor der Herausforderung, eingehende Probleme zu selektieren. 

Defender für Cloud umfasst zwei Mechanismen zur Priorisierung von Empfehlungen und Sicherheitswarnungen:

- Für Empfehlungen wurden **Sicherheitskontrollen** bereitgestellt, damit Sie nachvollziehen können, wie wichtig die einzelnen Empfehlungen für Ihren gesamten Sicherheitsstatus sind. Defender für Cloud enthält eine **Sicherheitsbewertung** für jede Kontrolle, damit Sie Ihre Sicherheitsmaßnahmen priorisieren können. Weitere Informationen finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).

- Für Warnungen haben wir jeder Warnung **Schweregradbezeichnungen** zugewiesen, damit Sie die Reihenfolge zur Bearbeitung der einzelnen Warnungen priorisieren können. Weitere Informationen finden Sie unter [Wie werden Warnungen klassifiziert?](alerts-overview.md#how-are-alerts-classified).

Nach Möglichkeit sollte das Sicherheitsteam seine Bemühungen jedoch auf Risiken für die **Daten** der Organisation konzentrieren. Wenn zwei Empfehlungen die gleiche Auswirkung auf Ihre Sicherheitsbewertung haben, sich eine aber auf eine Ressource mit vertraulichen Daten bezieht, sollten Sie im Idealfall diese Informationen bei der Bestimmung der Priorisierung berücksichtigen.

Die Datenvertraulichkeitsklassifizierungen und Datenvertraulichkeitsbezeichnungen von Azure Purview stellen diese Informationen bereit.

## <a name="discover-resources-with-sensitive-data"></a>Ermitteln von Ressourcen mit vertraulichen Daten
Um die wichtigen Informationen zu ermittelten vertraulichen Daten bereitzustellen und sicherzustellen, dass Sie über diese Informationen verfügen, wenn Sie sie benötigen, zeigt Defender für Cloud Informationen aus Purview an mehreren Orten an.

> [!TIP]
> Wenn eine Ressource von mehreren Purview-Konten überprüft wird, beziehen sich die in Defender für Cloud angezeigten Informationen auf die letzte Überprüfung.


### <a name="alerts-and-recommendations-pages"></a>Seiten mit Warnungen und Empfehlungen
Wenn Sie eine Empfehlung überprüfen oder eine Warnung untersuchen, sind die Informationen zu potenziell vertraulichen Daten auf der Seite enthalten.

Diese wichtige zusätzliche Metadatenebene trägt dazu bei, das Problem der Selektierung zu lösen, und stellt sicher, dass sich Sicherheitsexperten auf die Bedrohungen für vertrauliche Daten konzentrieren können.



### <a name="inventory-filters"></a>Filter für den Bestand
Die Seite [Ressourcenbestand](asset-inventory.md) enthält eine Sammlung leistungsstarker Filter, um Ihre Ressourcen mit ausstehenden Warnungen und Empfehlungen gemäß den für jedes Szenario relevanten Kriterien zu gruppieren. Diese Filter beinhalten **Datenvertraulichkeitsklassifizierungen** und **Datenvertraulichkeitsbezeichnungen**. Verwenden Sie diese Filter, um den Sicherheitsstatus von Ressourcen auszuwerten, für die Purview vertrauliche Daten ermittelt hat.

:::image type="content" source="./media/information-protection/information-protection-inventory-filters.png" alt-text="Screenshot: Informationsschutzfilter auf der Seite „Ressourcenbestand“ von Microsoft Defender für Cloud" lightbox="./media/information-protection/information-protection-inventory-filters.png":::

### <a name="resource-health"></a>Ressourcenintegrität 
Wenn Sie eine einzelne Ressource auswählen – sei es in einer Warnung, einer Empfehlung oder auf der Bestandsseite –, gelangen Sie zu einer detaillierten Integritätsseite mit einer ressourcenbezogenen Ansicht mit den wichtigen Sicherheitsinformationen im Zusammenhang mit dieser Ressource. 

Die Ressourcenintegritätsseite zeigt eine Momentaufnahme der allgemeinen Integrität einer einzelnen Ressource. Sie können ausführliche Informationen zur Ressource und sich alle Empfehlungen im Zusammenhang mit der Ressource ansehen. Wenn Sie einen Microsoft Defender-Plan verwenden, werden Ihnen außerdem ausstehende Sicherheitswarnungen für die jeweilige bestimmte Ressource angezeigt.

Beim Überprüfen der Integrität einer bestimmten Ressource werden die Purview-Informationen auf dieser Seite angezeigt. Anhand dieser Informationen können Sie ermitteln, welche Daten für diese Ressource erkannt wurden und welches Purview-Konto zum Überprüfen der Ressource verwendet wurde.

:::image type="content" source="./media/information-protection/information-protection-resource-health.png" alt-text="Screenshot: Seite zur Ressourcenintegrität in Defender für Cloud mit Informationsschutzbezeichnungen und -klassifizierungen von Azure Purview" lightbox="./media/information-protection/information-protection-resource-health.png":::

### <a name="overview-tile"></a>Übersichtskachel
Auf der dedizierten Kachel **Information Protection** auf dem [Übersichtsdashboard](overview-page.md) von Defender für Cloud wird die Abdeckung von Azure Purview angezeigt. Außerdem werden die Ressourcentypen mit den meisten ermittelten vertraulichen Daten angezeigt.

Ein Diagramm zeigt die Anzahl von Empfehlungen und Warnungen nach klassifizierten Ressourcentypen. Darüber hinaus enthält die Kachel einen Link zu Azure Purview, um zusätzliche Ressourcen zu überprüfen. Wählen Sie die Kachel aus, um klassifizierte Ressourcen auf der Seite „Ressourcenbestand“ von Defender for Cloud anzuzeigen.

:::image type="content" source="./media/information-protection/overview-dashboard-information-protection.png" alt-text="Screenshot: Kachel „Information Protection“ im Übersichtsdashboard von Microsoft Defender für Cloud" lightbox="./media/information-protection/overview-dashboard-information-protection.png":::


## <a name="next-steps"></a>Nächste Schritte

Verwandte Informationen

- [Was ist Azure Purview?](../purview/overview.md)
- [Unterstützte Datenquellen und Dateitypen in Azure Purview](../purview/sources-and-scans.md) und [Unterstützte Datenspeicher](../purview/purview-connector-overview.md)
- [Bewährte Methoden zur Bereitstellung von Azure Purview](../purview/deployment-best-practices.md)
- [Automatische Anwendung von Sensitivitätskennzeichnungen auf Ihre Daten in Azure Purview](../purview/how-to-automatically-label-your-content.md)
