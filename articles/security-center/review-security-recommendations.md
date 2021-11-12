---
title: Sicherheitsempfehlungen in Microsoft Defender für Cloud
description: In diesem Dokument erfahren Sie, wie Sicherheitsempfehlungen in Microsoft Defender für Cloud Ihnen helfen, Ihre Azure-Ressourcen zu schützen und Ihre Sicherheitsrichtlinien einzuhalten.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/07/2021
ms.author: memildin
ms.openlocfilehash: eb310e8edfb48c2252ae596aa95225e00329f5d2
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988230"
---
# <a name="review-your-security-recommendations"></a>Überprüfen Ihrer Sicherheitsempfehlungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel wird erläutert, wie Sie die Empfehlungen in Microsoft Defender für Cloud anzeigen und nutzen können, um Ihre Azure-Ressourcen zu schützen.

## <a name="monitor-recommendations"></a>Überwachen von Empfehlungen<a name="monitor-recommendations"></a>

Defender für Cloud analysiert den Sicherheitsstatus Ihrer Ressourcen, um mögliche Sicherheitsrisiken zu identifizieren. 

1. Öffnen Sie im Menü von Microsoft Defender für Cloud die Seite **Empfehlungen**, um die für Ihre Umgebung geltenden Empfehlungen anzuzeigen. Die Empfehlungen sind nach Sicherheitssteuerungen gruppiert.

    :::image type="content" source="./media/review-security-recommendations/view-recommendations.png" alt-text="Nach Sicherheitssteuerung gruppierte Empfehlungen" lightbox="./media/review-security-recommendations/view-recommendations.png":::

1. Verwenden Sie die optionalen Filter oberhalb der Liste der Empfehlungen, um Empfehlungen zu Ressourcentyp, Schweregrad, Umgebung oder anderen Kriterien zu finden, die für Sie wichtig sind.

    :::image type="content" source="media/review-security-recommendations/recommendation-list-filters.png" alt-text="Filter zum Optimieren der Liste der Microsoft Defender für Cloud-Empfehlungen":::

1. Erweitern Sie eine Steuerung, und wählen Sie eine bestimmte Empfehlung aus, um die Seite mit den Empfehlungsdetails anzuzeigen.

    :::image type="content" source="./media/review-security-recommendations/recommendation-details-page.png" alt-text="Seite mit den Empfehlungsdetails" lightbox="./media/review-security-recommendations/recommendation-details-page.png":::

    Inhalt der Seite:

    1. Für unterstützte Empfehlungen zeigt die obere Symbolleiste eine oder alle der folgenden Schaltflächen an:
        - **Erzwingen** und **Ablehnen** (siehe [Verhindern von Fehlkonfigurationen mit „Erzwingen“/“Ablehnen“-Empfehlungen](prevent-misconfigurations.md)).
        - **Richtliniendefinition anzeigen**, um direkt zum Azure Policy-Eintrag der zugrunde liegenden Richtlinie zu gelangen.
        - **Abfrage öffnen**: Alle Empfehlungen bieten die Möglichkeit, die detaillierten Informationen zu den betroffenen Ressourcen mithilfe des Azure Resource Graph-Explorers anzuzeigen.
    1. **Angabe des Schweregrads**.
    1. **Aktualisierungsintervall** (sofern relevant).
    1. **Anzahl ausgenommener Ressourcen**: Wenn für eine Empfehlung Ausnahmen vorhanden sind, wird die Anzahl der ausgenommenen Ressourcen mit einem Link zur Ansicht der spezifischen Ressourcen angezeigt.
    1. **Zuordnung zu MITRE ATT&CK ®-Taktiken und -Techniken**: Wenn eine Empfehlung über definierte Taktiken und Techniken verfügt, wählen Sie das Symbol aus, um Links zu den entsprechenden Seiten auf der MITRE-Website zu erhalten. 

        :::image type="content" source="media/review-security-recommendations/tactics-window.png" alt-text="Screenshot: Zuordnung der MITRE-Taktiken für eine Empfehlung.":::

    1. **Beschreibung**: Eine kurze Beschreibung des Sicherheitsproblems.
    1. Falls relevant, enthält die Detailseite auch eine Tabelle **verwandter Empfehlungen**:

        Tabellenbeziehungstypen sind:

        - **Voraussetzung**: Eine Empfehlung, die vor der ausgewählten Empfehlung abgeschlossen werden muss.
        - **Alternative**: Eine andere Empfehlung, die eine weitere Möglichkeit bietet, die Ziele der ausgewählten Empfehlung zu erreichen.
        - **Abhängig**: Eine Empfehlung, für die die ausgewählte Empfehlung eine Voraussetzung ist.

        Für jede verwandte Empfehlung wird in der Spalte „Betroffene Ressourcen“ die Anzahl fehlerhafter Ressourcen angezeigt.

        > [!TIP]
        > Wenn eine verwandte Empfehlung ausgegraut ist, ist ihre Abhängigkeit noch nicht abgeschlossen und die Empfehlung ist daher nicht verfügbar.

    1. **Schritte zur Bereinigung**: Eine Beschreibung der manuellen Schritte, die erforderlich sind, um das Sicherheitsproblem für die betroffenen Ressourcen zu beheben. Für Empfehlungen mit der Option **Korrigieren**** können Sie **Korrekturlogik anzeigen** auswählen, bevor Sie die vorgeschlagene Korrektur auf die Ressourcen anwenden.

    1. **Betroffene Ressourcen**: Die Ressourcen sind in Registerkarten unterteilt:
        - **Fehlerfreie Ressourcen**: Relevante Ressourcen, die entweder nicht beeinträchtigt sind oder bei denen das Problem bereits behoben wurde.
        - **Fehlerhafte Ressourcen**: Ressourcen, die weiterhin vom identifizierten Problem betroffen sind.
        - **Nicht anwendbare Ressourcen**: Ressourcen, für die die Empfehlung keine definitive Antwort geben kann. Auf dieser Registerkarte werden auch Gründe für die jeweilige Ressource angegeben. 

            :::image type="content" source="./media/review-security-recommendations/recommendations-not-applicable-reasons.png" alt-text="Nicht anwendbare Ressourcen mit Gründen":::
    1. Aktionsschaltflächen zum Beheben über die Empfehlung oder Auslösen einer Logik-App

## <a name="review-recommendation-data-in-azure-resource-graph-explorer-arg"></a>Überprüfen von Empfehlungsdaten im Azure Resource Graph-Explorer (ARG)

Die Symbolleiste auf der Seite mit den Empfehlungsdetails enthält die Schaltfläche **Abfrage öffnen**, um die Details in [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml) zu untersuchen, einem Azure-Dienst, der die Möglichkeit bietet, die Daten zum Sicherheitsstatus von Defender für Cloud abonnementübergreifend abzufragen.

ARG wurde entwickelt, um eine effiziente Ressourcenerkundung mit der Möglichkeit zu bieten, in Ihren Cloudumgebungen mit robusten Filter-, Gruppierungs- und Sortierfunktionen im großen Stil Abfragen auszuführen. Es ist eine schnelle und effiziente Möglichkeit, Informationen über Azure-Abonnements programmgesteuert oder aus dem Azure-Portal heraus abzufragen.

Mithilfe der [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) (Kusto-Abfragesprache) können Sie Daten von Defender für Cloud mit anderen Ressourceneigenschaften abgleichen.

Auf dieser Seite mit Empfehlungsdetails werden beispielsweise 15 betroffene Ressourcen angezeigt:

:::image type="content" source="./media/review-security-recommendations/open-query.png" alt-text="Die Schaltfläche **Abfrage öffnen** auf der Seite mit den Empfehlungsdetails.":::

Wenn Sie die zugrunde liegende Abfrage öffnen und ausführen, gibt der Azure Resource Graph-Explorer dieselben 15 Ressourcen und ihren Integritätsstatus für diese Empfehlung zurück: 

:::image type="content" source="./media/review-security-recommendations/run-query.png" alt-text="Azure Resource Graph-Explorer mit den Ergebnissen für die Empfehlung aus dem vorherigen Screenshot.":::

## <a name="preview-recommendations"></a>Vorschau der Empfehlungen

Empfehlungen, die als **Vorschau** gekennzeichnet sind, werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen.

Sie sollten nach Möglichkeit weiterhin korrigiert werden, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen.

Beispiel für eine Vorschauempfehlung:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Empfehlung mit dem Flag „Vorschau“":::
 
## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurden Ihnen die Sicherheitsempfehlungen in Defender für Cloud vorgestellt. In den folgenden Artikeln finden Sie verwandte Informationen:

- [Umsetzen von Empfehlungen](implement-security-recommendations.md) – In diesem Artikel erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
- [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md).
- [Automatisieren von Reaktionen auf Defender für Cloud-Trigger](workflow-automation.md): In diesem Artikel erfahren Sie, wie Sie Reaktionen auf Empfehlungen automatisieren.
- [Ausschließen einer Ressource aus einer Empfehlung](exempt-resource.md)
- [Sicherheitsempfehlungen: Referenzhandbuch](recommendations-reference.md)
