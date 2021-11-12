---
title: Begrenzen des gesamten für Ihr Azure Cosmos DB-Konto bereitgestellten Durchsatzes
description: Erfahren Sie, wie Sie den gesamten für Ihr Azure Cosmos DB-Konto bereitgestellten Durchsatz begrenzen.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2021
ms.author: thweiss
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1423d4444c7ec53e40f77d951acb58a63538cfbf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557792"
---
# <a name="limit-the-total-throughput-provisioned-on-your-azure-cosmos-db-account"></a>Begrenzen des gesamten für Ihr Azure Cosmos DB-Konto bereitgestellten Durchsatzes
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Wenn Sie ein Azure Cosmos DB-Konto im Modus [Bereitgestellter Durchsatz](./set-throughput.md) verwenden, entsteht der Großteil Ihrer Kosten in der Regel aufgrund der Durchsatzmenge, die Sie für Ihr Konto bereitgestellt haben. Insbesondere werden diese Kosten direkt durch Folgendes beeinflusst:

- Anzahl der Datenbanken mit gemeinsam genutztem Durchsatz
- Anzahl der Container mit dediziertem Durchsatz
- Umfang des Durchsatzes, die für jede dieser Ressourcen bereitgestellt wird
- Anzahl der Regionen, in denen Ihr Konto verfügbar ist

Die Nachverfolgung des gesamten Durchsatzumfangs, den Sie für Ihr Konto bereitgestellt haben, kann zu einer Herausforderung werden, insbesondere wenn Sie gerade erst mit Azure Cosmos DB anfangen. Dies kann zu unerwarteten Kosten führen, wenn dieser Betrag ein bestimmtes Budget übersteigt, mit dem Sie nicht gerechnet haben. Damit Sie Ihre Kosten besser im Griff haben, können Sie in Azure Cosmos DB den für Ihr Konto bereitgestellten Gesamtdurchsatz begrenzen.

> [!NOTE]
> Dieses Feature ist für [serverlose](./serverless.md) Konten nicht verfügbar.

Nachdem Sie eine Obergrenze für den Gesamtdurchsatz Ihres Kontos festgelegt haben, wird jeder der folgenden Vorgänge, der zu einer Überschreitung dieser Grenze führt, blockiert und ausdrücklich fehlgeschlagen:

- Erstellen einer neuen Datenbank mit gemeinsam genutztem Durchsatz
- Erstellen eines Containers mit dediziertem Durchsatz
- Erhöhen des bereitgestellten Durchsatzes für eine Ressource, die im (manuellen) Standardmodus konfiguriert ist
- Erhöhen des maximalen bereitgestellten Durchsatzes für eine Ressource, die im Autoskalierungsmodus konfiguriert ist
- Hinzufügen einer neuen Region zu Ihrem Konto

> [!NOTE]
> Bei Ressourcen, die im Autoskalierungsmodus konfiguriert sind, wird der maximale für die Ressource konfigurierte Durchsatz auf den Gesamtdurchsatz Ihres Kontos angerechnet.

> [!IMPORTANT]
> Sobald ein Grenzwert für den Gesamtdurchsatz für Ihr Konto aktiviert ist, müssen Sie beim Erstellen neuer Container einen expliziten Durchsatzwert übergeben. Derzeit erhalten Sie eine Fehlermeldung, wenn Sie versuchen, einen Container ohne expliziten Durchsatz zu erstellen.

## <a name="set-the-total-throughput-limit-from-the-azure-portal"></a>Festlegen der Obergrenze für den Gesamtdurchsatz im Azure-Portal

### <a name="new-account"></a>Neues Konto

Beim Erstellen eines neuen Azure Cosmos DB-Kontos im Portal haben Sie die Möglichkeit, den Gesamtdurchsatz des Kontos zu begrenzen:

:::image type="content" source="./media/limit-total-account-throughput/create-account.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie der Gesamtdurchsatz des Kontos beim Erstellen eines neuen Kontos begrenzt wird" border="true":::

Wenn Sie diese Option aktivieren, wird der Gesamtdurchsatz Ihres Kontos auf 4.000 RU/s begrenzt. Sie können diesen Wert ändern, nachdem Ihr Konto erstellt wurde.

### <a name="existing-account"></a>Vorhandenes Konto

Navigieren Sie im Azure-Portal zu Ihrem Azure Cosmos DB-Konto, und wählen Sie im linken Menü **Cost Management** aus.

:::image type="content" source="./media/limit-total-account-throughput/existing-account.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie der Gesamtdurchsatz eines Kontos für ein vorhandenes Konto aktualisiert wird" border="true":::

Dieser Abschnitt zeigt eine Übersicht über den für Ihr Konto bereitgestellten Gesamtdurchsatz und ermöglicht Ihnen, die Obergrenze für den Gesamtdurchsatz zu konfigurieren. Die folgenden drei Optionen sind verfügbar:

- **Den gesamten bereitgestellten Durchsatz des Kontos auf die vom Rabatt des Free-Tarifs vorgesehene Größe begrenzen.** Diese Option ist nur für Konten im Free-Tarif verfügbar und begrenzt den Gesamtdurchsatz Ihres Kontos auf 1.000 RU/s. Durch Aktivieren dieser Option stellen Sie sicher, dass Ihnen keine Kosten für den bereitgestellten Durchsatz in Rechnung gestellt werden.
- **Zulassen, dass der Gesamtdurchsatz des Kontos bis zu einem benutzerdefinierten Umfang bereitgestellt werden kann**. Bei dieser Option können Sie den gesamten bereitgestellten Durchsatz eingeben, den Sie nicht überschreiten möchten. Eine monatliche Kostenschätzung, die Ihrer Eingabe entspricht, wird als Referenz angezeigt.
  > [!NOTE]
  > Dieser benutzerdefinierte Grenzwert darf nicht niedriger sein als der Gesamtdurchsatz, der dem Konto derzeit bereitgestellt wird.
- **Ohne festgelegten Grenzwert die Bereitstellung des Gesamtdurchsatzes des Kontos bis zu einem beliebigen Umfang zulassen**. Mit dieser Option wird die Obergrenze deaktiviert.

## <a name="set-the-total-throughput-limit-programmatically"></a>Programmgesteuertes Festlegen der Obergrenze des Gesamtdurchsatzes

### <a name="using-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Beim Erstellen oder Aktualisieren Ihres Azure Cosmos DB-Kontos mit Azure Resource Manager können Sie die Obergrenze für den Gesamtdurchsatz konfigurieren, indem Sie die Eigenschaft `properties.capacity.totalThroughputLimit` festlegen:

```json
{
  "location": "West US",
  "kind": "DocumentDB",
  "properties": {
    "locations": [
      {
        "locationName": "West US",
        "failoverPriority": 0,
        "isZoneRedundant": false
      }
    ],
    "databaseAccountOfferType": "Standard",
    "capacity": {
        "totalThroughputLimit": 2000
    }
  }
}
```

Legen Sie diese Eigenschaft auf `-1` fest, um die Obergrenze zu deaktivieren.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

#### <a name="are-there-situations-where-the-total-provisioned-throughput-can-exceed-the-limit"></a>Gibt es Situationen, in denen der gesamte bereitgestellte Durchsatz den Grenzwert überschreiten kann?

Azure Cosmos DB erzwingt einen Mindestdurchsatz von 10 RU/s pro GB gespeicherter Daten. Wenn Sie Daten auf dem Mindestwert erfasst haben, erhöht sich der für Ihre Ressourcen bereitgestellte Durchsatz automatisch, um 10 RU/s pro GB zu verarbeiten. In diesem Fall und nur in diesem Fall kann der gesamte bereitgestellte Durchsatz den von Ihnen festgelegten Grenzwert überschreiten.

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte mit der [Planung und Verwaltung Ihrer Kosten](./plan-manage-costs.md) in Azure Cosmos DB.
- Erfahren Sie mehr über [bereitgestellten Durchsatz](./set-throughput.md).
- Erfahren Sie, wie Sie [die Kosten für den bereitgestellten Durchsatz optimieren](./optimize-cost-throughput.md).
