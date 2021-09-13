---
author: baanders
description: Includedatei mit Informationen zum Einrichten eines Event Grid-Endpunkts und einer Route
ms.service: digital-twins
ms.topic: include
ms.date: 7/21/2021
ms.author: baanders
ms.openlocfilehash: 0311f53845a4ee18bf595d9efe2593d486818fb8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748554"
---
### <a name="create-the-event-grid-topic"></a>Erstellen des Event Grid-Themas

[Event Grid](../articles/event-grid/overview.md) ist ein Azure-Dienst, mit dem Sie von Azure-Diensten stammende Ereignisse an andere Orte in Azure weiterleiten und dort bereitstellen können. Sie können ein [Event Grid-Thema](../articles/event-grid/concepts.md) erstellen, um bestimmte Ereignisse von einer Quelle zu erfassen. Abonnenten können dann über das Thema lauschen, um Ereignisse zu erhalten, sobald sie verfügbar sind.

Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um ein Event Grid-Thema zu erstellen:

```azurecli-interactive
az eventgrid topic create --resource-group <your-resource-group> --name <name-for-your-event-grid-topic> --location <region>
```

Die Ausgabe dieses Befehls enthält Informationen zum von Ihnen erstellten Event Grid-Thema. Speichern Sie den **Namen**, den Sie Ihrem Event Grid-Thema gegeben haben, da Sie ihn später benötigen.

### <a name="create-the-endpoint"></a>Erstellen des Endpunkts

Erstellen Sie als Nächstes einen Event Grid-Endpunkt in Azure Digital Twins, über den Ihre Instanz mit Ihrem Event Grid-Thema verbunden wird. Verwenden Sie den folgenden Befehl. Geben Sie dabei den Namen Ihres Event Grid-Themas an, und füllen Sie die anderen Platzhalterfelder nach Bedarf aus.

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

Die Ausgabe dieses Befehls enthält Informationen zum von Ihnen erstellten Endpunkt.

Suchen Sie in der Ausgabe nach dem Feld `provisioningState`, und vergewissern Sie sich, dass der Wert „Succeeded“ (Erfolgreich) lautet.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/output-endpoints.png" alt-text="Screenshot des Ergebnisses der Endpunktabfrage in der Cloud Shell des Azure-Portals mit dem Endpunkt mit dem provisioningState „Erfolgreich“.":::

Er kann auch „Provisioning“ lauten. Dies bedeutet, dass die Erstellung des Endpunkts noch nicht abgeschlossen ist. Falls ja, warten Sie einige Sekunden, und führen Sie den folgenden Befehl aus, um den Status des Endpunkts zu überprüfen. Wiederholen Sie diesen Schritt, bis `provisioningState` den Wert „Succeeded“ (Erfolgreich) hat.

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Speichern Sie den **Namen** Ihres Endpunkts für später.

### <a name="create-the-route"></a>Erstellen der Route

Erstellen Sie als Nächstes eine Azure Digital Twins-Route, über die Ereignisse an den Event Grid-Endpunkt gesendet werden, den Sie gerade erstellt haben. 

Hierzu können Sie den folgenden CLI-Befehl verwenden. (Geben Sie den Namen Ihres Endpunkts an, und füllen Sie die anderen Platzhalterfelder nach Bedarf aus.) Durch diesen Befehl werden alle Ereignisse weitergeleitet, die im Zwillingsgraphen auftreten. Auf Wunsch können die Ereignisse mithilfe von [Filtern](../articles/digital-twins/how-to-manage-routes.md?tabs=portal%2Cportal2%2Cportal3#filter-events) auf bestimmte Ereignisse beschränkt werden.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

Die Ausgabe dieses Befehls enthält einige Informationen zur von Ihnen erstellten Route.

>[!NOTE]
>Die Bereitstellung der Endpunkte (aus dem vorherigen Schritt) muss abgeschlossen sein, bevor Sie eine Ereignisroute einrichten können, in der diese verwendet werden. Wenn die Routenerstellung fehlschlägt, weil die Endpunkte noch nicht bereit sind, sollten Sie einige Minuten warten und es dann erneut versuchen.