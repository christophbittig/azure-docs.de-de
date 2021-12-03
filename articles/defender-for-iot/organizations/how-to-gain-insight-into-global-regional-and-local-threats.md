---
title: Gewinnen von Erkenntnissen zu globalen, regionalen und lokalen Bedrohungen
description: In der lokalen Verwaltungskonsole können Sie mithilfe der Siteübersicht Erkenntnisse zu globalen, regionalen und lokalen Bedrohungen gewinnen.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 6cef79e708f5aff5eff2c02f2b0fe612aa11a224
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293229"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Gewinnen von Erkenntnissen zu globalen, regionalen und lokalen Bedrohungen

In der lokalen Verwaltungskonsole können Sie mithilfe der Siteübersicht eine vollständige Sicherheitsabdeckung erzielen, indem Sie Ihr Netzwerk in geografische und logische Segmente unterteilen, die Ihre Geschäftstopologie widerspiegeln:

- **Geografische Einrichtungsebene**: Ein Standort spiegelt eine Anzahl von Geräten wider, die nach einem auf der Karte dargestellten geografischen Standort gruppiert sind. Microsoft Defender für IoT stellt Ihnen standardmäßig eine Weltkarte bereit. Sie können die Karte aktualisieren, um Ihre Organisations- oder Geschäftsstruktur widerzuspiegeln. Verwenden Sie z. B. eine Karte, die Standorte in einem bestimmten Land, in einer Stadt oder in einem Industriegelände widerspiegelt. Wenn sich die Farbe des Standorts auf der Karte ändert, bietet dies dem SOC-Team einen Hinweis auf den kritischen Systemstatus in der Einrichtung.

  Die Karte ist interaktiv und ermöglicht das Öffnen der einzelnen Standorte und das genaue Betrachten der Informationen zum jeweiligen Standort.

- **Globale logische Ebene**: Eine Geschäftseinheit ist eine Möglichkeit, Ihr Unternehmen nach bestimmten Branchen in logische Segmente zu unterteilen. Wenn Sie dies tun, wird Ihre Geschäftstopologie auf der Karte widergespiegelt.

  Beispielsweise kann ein globales Unternehmen, das Glasfabriken, Kunststofffabriken und Automobilfabriken unterhält, als drei verschiedene Geschäftseinheiten verwaltet werden. Ein physischer Standort in Toronto umfasst drei verschiedene Glasproduktionslinien, eine Kunststoffproduktionslinie und eine Produktionslinie für LKW-Motoren. Dieser Standort verfügt also über Vertreter aller drei Geschäftseinheiten.

- **Geografische Regionsebene**: Erstellen Sie Regionen, um ein globales Unternehmen in geografische Regionen zu unterteilen. Das von uns beschriebene Unternehmen könnte beispielsweise die Regionen „Nordamerika“, „Westeuropa“ und „Osteuropa“ verwenden. In der Region „Nordamerika“ gibt es Fabriken aus allen drei Geschäftseinheiten. In der Region „Westeuropa“ gibt es Automobilfabriken und Glasfabriken, und in der Region „Osteuropa“ gibt es nur Kunststofffabriken.

- **Lokale logische Segmentebene**: Eine Zone ist ein logisches Segment innerhalb eines Standorts, das beispielsweise einen Funktionsbereich oder eine Produktionslinie definiert. Das Arbeiten mit Zonen ermöglicht die Durchsetzung von Sicherheitsrichtlinien, die für die Zonendefinition relevant sind. Beispielsweise kann ein Standort, der fünf Produktionslinien enthält, in fünf Zonen segmentiert werden.

- **Lokale Ansichtsebene**: Eine lokale Ansicht einer einzelnen Sensorinstallation bietet Einblicke in den Betriebs- und Sicherheitsstatus verbundener Geräte.

## <a name="work-with-site-map-views"></a>Arbeiten mit Siteübersichtansichten

Die lokale Verwaltungskonsole bietet einen Überblick über Ihr industrielles Netzwerk auf einer kontextbezogenen Karte. In der allgemeinen Kartenansicht wird die globale Karte Ihrer Organisation mit dem geografischen Standort der einzelnen Standorte dargestellt.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Screenshot der Kartenansicht des Unternehmens":::

### <a name="color-coded-map-views"></a>Farbcodierte Kartenansichten

**Grün**: Die Anzahl der sicherheitsrelevanten Ereignisse liegt unter dem von Defender für IoT für Ihr System definierten Schwellenwert. Es ist keine Aktion erforderlich.

**Gelb**: Die Anzahl der sicherheitsrelevanten Ereignisse ist gleich dem von Defender für IoT für Ihr System definierten Schwellenwert. Sie sollten die Ereignisse untersuchen.  

**Rot**: Die Anzahl der sicherheitsrelevanten Ereignisse liegt über dem von Defender für IoT für Ihr System definierten Schwellenwert. Ergreifen Sie umgehend Maßnahmen.

### <a name="risk-level-map-views"></a>Kartenansichten auf Risikoebene

**Risikobewertung**: In der Ansicht „Risikobewertung“ werden Informationen zu Standortrisiken angezeigt. Anhand der Risikoinformationen können Sie Prioritäten für die Risikominderung setzen und eine Roadmap für die Planung von Sicherheitsverbesserungen erstellen.

**Reaktion auf Vorfälle**: Erhalten Sie eine zentrale Ansicht aller nicht bestätigten Warnungen an allen Standorten des Unternehmens. Sie können einen Drilldown für die an einem bestimmten Standort erkannten Warnungen ausführen und diese verwalten.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Screenshot der Kartenansicht des Unternehmens mit der Reaktion auf Vorfälle":::

**Schädliche Aktivitäten**: Bei erkannter Malware wird der Standort rot angezeigt. Dies ist ein Hinweis darauf, dass Sie umgehend Maßnahmen ergreifen müssen.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Screenshot der Kartenansicht des Unternehmens mit schädlichen Aktivitäten":::

**Vorgangswarnungen**: Diese Kartenansicht für OT-Systeme ermöglicht ein besseres Verständnis dafür, auf welchem OT-System operative Vorfälle wie das Beenden der SPS, Firmwareuploads und Programmuploads auftreten könnten.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Screenshot der Kartenansicht des Unternehmens mit Vorgangswarnungen":::

Gehen Sie wie folgt vor, um eine Kartenansicht auszuwählen:

1. Wählen Sie auf der Karte die Option **Standardansicht** aus.
2. Wählen Sie eine Ansicht aus.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Screenshot der Standardansicht der Siteübersicht":::

## <a name="update-the-site-map-image"></a>Aktualisieren des Siteübersichtsbilds

Defender für IoT stellt eine Standardweltkarte bereit. Sie können diese Karte ändern, damit Ihre Organisation widergespiegelt wird: Sie können z. B. eine Länderkarte oder eine Stadtkarte verwenden. 

Gehen Sie wie folgt vor, um die Karte zu ersetzen:

1. Wählen Sie im linken Bereich die Option **Systemeinstellungen** aus.

2. Wählen Sie **Siteübersicht ändern** aus, und laden Sie die Grafikdatei hoch, um die Standardkarte zu ersetzen.

## <a name="next-step"></a>Nächster Schritt

[Anzeigen von Warnungen](how-to-view-alerts.md)
