---
title: Verbrauchsbasiertes serverloses Angebot in Azure Cosmos DB
description: Erfahren Sie mehr über das nutzungsbasierte serverlose Angebot für Azure Cosmos DB.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: de2ef5b76e67ae19eca6bbb24c9d65dfc7b602b6
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987307"
---
# <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB serverlos
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Mit Azure Cosmos DB serverlos können Sie Ihr Azure Cosmos-Konto auf verbrauchsbasierte Weise verwenden. Dabei werden Ihnen nur die Anforderungseinheiten in Rechnung gestellt, die von den Datenbankvorgängen und dem von Ihren Daten genutzten Speicher beansprucht werden. Serverlose Container können Tausende von Anforderungen pro Sekunde ohne Mindestgebühren und ohne erforderliche Kapazitätsplanung verarbeiten.

> [!IMPORTANT] 
> Haben Sie Feedback zu Azure Cosmos DB serverlos? Teilen Sie uns Ihre Meinung mit! Sie können eine Nachricht an das Azure Cosmos DB serverlos-Team senden: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com).

Wenn Sie Azure Cosmos DB verwenden, werden für jeden Datenbankvorgang Kosten in [Anforderungseinheiten](request-units.md) ausgedrückt. Wie Ihnen diese Kosten in Rechnung gestellt werden, hängt vom Typ des Azure Cosmos-Kontos ab, das Sie verwenden:

- Im Modus [bereitgestellter Durchsatz](set-throughput.md) müssen Sie sich auf eine bestimmte Menge an Durchsatz (ausgedrückt in Anforderungseinheiten pro Sekunde) festlegen, der für Ihre Datenbanken und Container bereitgestellt wird. Die Kosten für Ihre Datenbankvorgänge werden dann von der Anzahl der pro Sekunde verfügbaren Anforderungseinheiten abgezogen. Am Ende Ihres Abrechnungszeitraums wird Ihnen die bereitgestellte Menge an Durchsatz in Rechnung gestellt.
- Im serverlosen Modus müssen Sie beim Erstellen von Containern in Ihrem Azure Cosmos-Konto keinen Durchsatz bereitstellen. Am Ende Ihres Abrechnungszeitraums wird Ihnen die Anzahl der Anforderungseinheiten in Rechnung gestellt, die von den Datenbankvorgängen genutzt wurden.

## <a name="use-cases"></a>Anwendungsfälle

Azure Cosmos DB im serverlosen Modus eignet sich ideal für Szenarien, in denen Sie **zeitweiligen, nicht vorhersagbaren Datenverkehr** mit langen Leerlaufzeiten erwarten. Da in solchen Situationen die Bereitstellung von Kapazitäten nicht erforderlich ist und sich aus Kostengründen von selbst verbietet, sollten Sie in den folgenden Anwendungsfällen Azure Cosmos DB im serverlosen Modus in Erwägung ziehen:

- Erste Schritte mit Azure Cosmos DB
- Ausführen von Anwendungen, für die Folgendes gilt:
    - Zeitweilige Datenverkehrsbursts, die schwer vorherzusagen sind
    - Wenig durchschnittliches bis hohes Datenverkehrsaufkommen (< 10 %)
- Entwickeln, Testen, Erstellen von Prototypen und Ausführen in der Produktion bei Anwendungen mit unbekanntem Datenverkehrsmuster
- Integration in serverlose Computedienste wie [Azure Functions](../azure-functions/functions-overview.md)

Weitere Anleitungen zum Auswählen des Angebots, das Ihrem Anwendungsfall am besten entspricht, finden Sie im Artikel [Wählen zwischen bereitgestelltem Durchsatz und serverlos](throughput-serverless.md).

## <a name="using-serverless-resources"></a>Verwenden von serverlosen Ressourcen

Serverlos ist ein neuer Azure Cosmos-Kontotyp. Dies bedeutet, dass Sie zwischen **bereitgestelltem Durchsatz** und **serverlos** wählen müssen, wenn Sie ein neues Konto erstellen. Sie müssen ein neues serverloses Konto erstellen, um mit serverless zu beginnen. Das Migrieren vorhandener Konten zum/aus dem serverlosen Modus wird derzeit nicht unterstützt.

Jeder Container, der in einem serverlosen Konto erstellt wird, ist ein serverloser Container. Serverlose Container stellen dieselben Funktionen wie Container bereit, die im Modusfpr bereitgestellten Durchsatz erstellt wurden, sodass Sie Ihre Daten genau auf die gleiche Weise lesen, schreiben und abfragen. Serverlose Konten und Container haben jedoch auch bestimmte Merkmale:

- Ein serverloses Konto kann nur in einer einzelnen Azure-Region ausgeführt werden. Es ist nicht möglich, einem serverlosen Konto nach der Erstellung zusätzliche Azure-Regionen hinzuzufügen.
- Bereitstellung von Durchsatz ist für serverlose Container nicht erforderlich, daher gelten die folgenden Aussagen:
    - Wenn Sie einen serverlosen Container erstellen, können Sie keinen Durchsatz übergeben. Bei einem Versuch wird ein Fehler zurückgegeben.
    - Sie können den Durchsatz für einen serverlosen Container nicht lesen oder aktualisieren, und es wird ein Fehler zurückgegeben.
    - Sie können in einem serverlosen Konto keine Datenbank mit freigegebenem Durchsatz erstellen, und es wird ein Fehler zurückgegeben.
- Serverlose Container können maximal 50 GB Daten und Indizes speichern.

## <a name="monitoring-your-consumption"></a>Überwachen Ihres Verbrauchs

Wenn Sie Azure Cosmos DB bereits im Modus „Bereitgestellter Durchsatz“ verwendet haben, werden Sie feststellen, dass serverlos kostengünstiger ist, wenn Ihr Datenverkehr die bereitgestellte Kapazität nicht rechtfertigt. Der Kompromiss besteht darin, dass Ihre Kosten weniger vorhersehbar werden, da die Abrechnung auf der Grundlage der Anzahl der Anforderungen erfolgt, die Ihre Datenbank bearbeitet hat. Aus diesem Grund ist es wichtig, den aktuellen Verbrauch im Auge zu behalten.

Wenn Sie den Bereich **Metriken** Ihres Kontos durchsuchen, finden Sie unter der Registerkarte **Übersicht** ein Diagramm mit dem Namen **Verbrauchte Anforderungseinheiten**. Dieses Diagramm zeigt, wie viele Anforderungseinheiten Ihr Konto verbraucht hat:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Diagramm mit den verbrauchten Anforderungseinheiten" border="false":::

Sie finden dasselbe Diagramm, wenn Sie Azure Monitor verwenden, wie [hier](monitor-request-unit-usage.md) beschrieben. Beachten Sie, dass Sie mit Azure Monitor [Warnungen](../azure-monitor/alerts/alerts-metric-overview.md) einrichten können, mit denen Sie benachrichtigt werden können, wenn der Verbrauch von Anforderungseinheiten einen bestimmten Schwellenwert überschritten hat.

## <a name="performance"></a><a id="performance"></a>Leistung

Serverlose Ressourcen ergeben bestimmte Leistungsmerkmale, die sich von den Merkmalen von Ressourcen mit bereitgestelltem Durchsatz unterscheiden. Die Latenz von serverlosen Containern wird durch ein Servicelevelziel (SLO) von 10 Millisekunden oder weniger für Punkt-Lesevorgänge und 30 Millisekunden oder weniger für Schreibvorgänge abgedeckt. Ein Punktlesevorgang besteht aus dem Abrufen eines einzelnen Elements anhand seiner ID und seines Partitionsschlüsselwerts.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit den folgenden Artikeln mit serverlos vertraut:

- [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
- [Wählen zwischen bereitgestelltem Durchsatz und serverlos](throughput-serverless.md)
- [Preismodell in Azure Cosmos DB](how-pricing-works.md)
