---
title: Nachverfolgen der Sicherheitsbewertung in Azure Security Center
description: Erfahren Sie mehr über die verschiedenen Methoden zum Zugreifen auf und Nachverfolgen von Sicherheitsbewertungen im Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 9373806b87144005407861b2043799f0c62ce982
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237910"
---
# <a name="access-and-track-your-secure-score"></a>Zugreifen auf Ihre Sicherheitsbewertung und Nachverfolgen dieser Bewertung

Ihre insgesamte Sicherheitsbewertung sowie Ihre Bewertung pro Abonnement können Sie im Azure-Portal finden oder entsprechend den Angaben in den folgenden Abschnitten programmgesteuert abrufen:

> [!TIP]
> Eine ausführliche Erläuterung, wie diese Bewertungen berechnet werden, finden Sie unter [Berechnungen – Verstehen der Bewertung](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Abrufen ihrer Sicherheitsbewertung aus dem Portal

Von Security Center wird Ihre Bewertung im Portal auf der Übersichtsseite als erste Hauptkachel gut sichtbar angezeigt. Wenn Sie diese Kachel auswählen, gelangen Sie zur dedizierten Seite mit der Sicherheitsbewertung, auf der Sie die Bewertung nach Abonnement aufgeschlüsselt sehen. Wählen Sie ein einzelnes Abonnement aus, um die detaillierte Liste mit priorisierten Empfehlungen und die möglichen Auswirkungen anzuzeigen, die eine Umsetzung dieser Empfehlungen auf die Bewertung des Abonnements hat. 

Zur Wiederholung: Ihre Sicherheitsbewertung wird auf Seiten des Security Center-Portals an den folgenden Stellen gezeigt.

- Auf einer Kachel der **Übersicht** in Security Center (Hauptdashboard):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Die Sicherheitsbewertung auf dem Security Center-Dashboard":::

- Auf der Seite mit der dedizierten **Sicherheitsbewertung** können Sie die Sicherheitsbewertung für Ihr Abonnement und Ihre Verwaltungsgruppen sehen:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Sicherheitsbewertung für Abonnements auf der Security Center-Seite „Sicherheitsbewertung“":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Sicherheitsbewertung für Verwaltungsgruppen auf der Security Center-Seite „Sicherheitsbewertung“":::

    > [!NOTE]
    > Für alle Verwaltungsgruppen, für die Sie nicht über ausreichende Berechtigungen verfügen, wird als Bewertung „Eingeschränkt“ angezeigt. 

- Oben auf der Seite **Empfehlungen**:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Die Sicherheitsbewertung auf der Security Center-Seite „Empfehlungen“":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Abrufen ihrer Sicherheitsbewertung über die REST-API

Sie können auf Ihre Bewertung über die Sicherheitsbewertungs-API zugreifen. Die API-Methoden bieten die Flexibilität, die Daten abzufragen und im Laufe der Zeit einen eigenen Berichtsmechanismus für Ihre Sicherheitsbewertungen zu erstellen. Beispielsweise können Sie die API [Sicherheitsbewertungen](/rest/api/securitycenter/securescores) verwenden, um die Bewertung für ein bestimmtes Abonnement zu erhalten. Darüber hinaus können Sie die API [Sicherheitsbewertungs-Steuerelemente](/rest/api/securitycenter/securescorecontrols) verwenden, um die Sicherheitssteuerelemente und die aktuelle Bewertung Ihrer Abonnements aufzulisten.

![Abrufen einer einzelnen Sicherheitsbewertung über die API](media/secure-score-security-controls/single-secure-score-via-api.png)

Beispiele für Tools, die auf der Sicherheitsbewertungs-API basieren, finden Sie im [Bereich zu den Sicherheitsbewertungen in unserer GitHub-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Abrufen Ihrer Sicherheitsbewertung aus Azure Resource Graph

Azure Resource Graph bietet mit zuverlässigen Funktionen zum Filtern, Gruppieren und Sortieren sofortigen Zugriff auf Ressourceninformationen in Ihren Cloudumgebungen. Es ist eine schnelle und effiziente Möglichkeit, Informationen über Azure-Abonnements programmgesteuert oder aus dem Azure-Portal heraus abzufragen. [Erfahren Sie mehr über Azure Resource Graph](../governance/resource-graph/index.yml).

So greifen Sie mithilfe von Azure Resource Graph auf Sicherheitsbewertungen für mehrere Abonnements zu:

1. Öffnen Sie im Azure-Portal **Azure Resource Graph-Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Die Empfehlungsseite des Azure Resource Graph-Explorer** wird gestartet." :::

1. Geben Sie Ihre Kusto-Abfrage ein (in den nachstehenden Beispielen finden Sie Anleitungen).

    - Diese Abfrage gibt die Abonnement-ID, die aktuelle Bewertung in Punkten und als Prozentsatz sowie die maximale Bewertung des Abonnements zurück. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Diese Abfrage gibt den Status aller Sicherheitskontrollen zurück. Für jede Sicherheitskontrolle erhalten Sie die Anzahl fehlerhafter Ressourcen sowie die aktuelle und maximale Bewertung. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Wählen Sie **Abfrage ausführen** aus.


## <a name="tracking-your-secure-score-over-time"></a>Nachverfolgen Ihrer Sicherheitsbewertung im zeitlichen Verlauf

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Bericht „Sicherheitsbewertung im Zeitverlauf“ auf der Arbeitsmappenseite

Die Security Center-Arbeitsmappenseite enthält einen vorgefertigten Bericht zur visuellen Nachverfolgung der Bewertungen Ihrer Abonnements, Sicherheitskontrollen und vielem mehr. Weitere Informationen finden Sie unter [Erstellen umfassender, interaktiver Berichte zu Security Center-Daten](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Abschnitt des Berichts „Sicherheitsbewertung im Zeitverlauf“ aus dem Arbeitsmappenkatalog in Azure Security Center":::

### <a name="power-bi-pro-dashboards"></a>Power BI Pro-Dashboards

Wenn Sie ein Power BI-Benutzer sind und über ein Pro-Konto verfügen, können Sie das Power BI-Dashboard **Sicherheitsbewertung im zeitlichen Verlauf** verwenden, um Ihre Sicherheitsbewertung nachzuverfolgen und Änderungen zu untersuchen.

> [!TIP]
> Sie finden dieses Dashboard sowie andere Tools für die programmgesteuerte Arbeit mit Sicherheitsbewertungen im dedizierten Bereich der Azure Security Center-Community auf GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Das Dashboard enthält die folgenden zwei Berichte, anhand derer Sie Ihren Sicherheitsstatus analysieren können:

- **Zusammenfassung der Ressourcen**: Dieser Bericht enthält zusammengefasste Daten zur Integrität Ihrer Ressourcen.
- **Zusammenfassung der Sicherheitsbewertung**: Dieser Bericht enthält zusammengefasste Daten zu Ihrem Bewertungsfortschritt. Verwenden Sie das Diagramm „Sicherheitsbewertung im zeitlichen Verlauf pro Abonnement“, um Änderungen in der Bewertung anzuzeigen. Wenn Sie eine drastische Änderung in der Bewertung feststellen, überprüfen Sie die Tabelle „Erkannte Änderungen, die sich auf Ihre Sicherheitsbewertung auswirken können“ auf mögliche Änderungen, die dazu geführt haben könnten. In dieser Tabelle werden gelöschte Ressourcen, neu bereitgestellte Ressourcen oder Ressourcen dargestellt, deren Sicherheitsstatus aufgrund einer der Empfehlungen geändert wurde.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Das optionale Power BI-Dashboard für die Sicherheitsbewertung im zeitlichen Verlauf zum Nachverfolgen Ihrer Sicherheitsbewertung und zum Untersuchen von Änderungen":::


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie auf Ihre Sicherheitsbewertung zugreifen und diese nachverfolgen. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md)
- [Umsetzen von Empfehlungen in Azure Security Center](security-center-remediate-recommendations.md)
- [Anzeigen der GitHub-basierten Tools für die programmgesteuerte Arbeit mit Sicherheitsbewertungen](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)