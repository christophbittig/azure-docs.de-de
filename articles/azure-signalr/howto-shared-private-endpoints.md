---
title: Schützen von ausgehendem Azure SignalR-Datenverkehr über freigegebene private Endpunkte
titleSuffix: Azure SignalR Service
description: Vorgehensweise zum Schützen von ausgehendem Datenverkehr über freigegebene private Endpunkte, um zu vermeiden, dass Datenverkehr an ein öffentliches Netzwerk geht
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 07/08/2021
ms.author: dayshen
ms.openlocfilehash: 4be77c9dce68c55a37713c42ceee7fc2b7dbf6d9
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220189"
---
# <a name="secure-azure-signalr-outbound-traffic-through-shared-private-endpoints"></a>Schützen von ausgehendem Azure SignalR-Datenverkehr über freigegebene private Endpunkte

Wenn Sie den [serverlosen Modus](concept-service-mode.md#serverless-mode) im Azure SignalR-Dienst verwenden, kommt es möglicherweise zu ausgehendem Datenverkehr zum Upstream. Der Upstream, z. B. Azure-Web-App und Azure Functions, lässt sich so konfigurieren, dass Verbindungen aus einer Liste virtueller Netzwerke akzeptiert und externe Verbindungen abgelehnt werden, die aus einem öffentlichen Netzwerk stammen. Sie können eine ausgehende [private Endpunktverbindung](../private-link/private-endpoint-overview.md) erstellen, um diese Endpunkte zu erreichen.

   :::image type="content" alt-text="Übersicht: freigegebener privater Endpunkt." source="media\howto-shared-private-endpoints\shared-private-endpoint-overview.png" :::

Für diese ausgehende Methode gelten die folgenden Anforderungen:

+ Der Upstream muss eine Azure-Web-App oder Azure-Funktion sein.

+ Der Azure SignalR-Dienst muss die Standardebene verwenden.

+ Die Azure-Web-App oder Azure-Funktion muss sich auf bestimmten SKUs befinden. Siehe [Verwenden von privaten Endpunkten für eine Azure-Web-App](../app-service/networking/private-endpoint.md).

## <a name="shared-private-link-resources-management-apis"></a>Freigegebene Verwaltungs-APIs für Private Link-Ressourcen

Private Endpunkte von gesicherten Ressourcen, die über Azure SignalR-Dienst-APIs erstellt wurden, werden als *freigegebene Private Link-Ressourcen* bezeichnet. Der Grund: Der Zugriff auf eine Ressource (z. B. eine Azure-Funktion), die in den [Azure Private Link-Dienst](https://azure.microsoft.com/services/private-link/) integriert wurde, wird von Ihnen „freigegeben“. Diese privaten Endpunkte werden in der Azure SignalR-Dienstausführungsumgebung erstellt und sind für Sie nicht direkt sichtbar.

Derzeit können Sie die Verwaltungs-REST-API verwenden, um *freigegebene Private Link-Ressourcen* zu erstellen oder zu löschen. Im weiteren Verlauf dieses Artikels verwenden wir [Azure CLI](/cli/azure/), um die REST-API-Aufrufe zu veranschaulichen.

> [!NOTE]
> Die Beispiele in diesem Artikel basieren auf den folgenden Annahmen:
> * Die Ressourcen-ID dieses Azure SignalR-Diensts ist _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr_.
> * Die Ressourcen-ID dieser Upstream-Azure-Funktion ist „_/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func“.

In den restlichen Beispielen wird gezeigt, wie der Dienst _contoso-signalr_ so konfiguriert werden kann, dass seine Upstreamaufrufe an eine Funktion über einen privaten Endpunkt statt über ein öffentliches Netzwerk übertragen werden.

### <a name="step-1-create-a-shared-private-link-resource-to-the-function"></a>Schritt 1: Erstellen einer freigegebenen Private Link-Ressource zu der Funktion

Sie können den folgenden API-Aufruf mit der [Azure CLI](/cli/azure/) ausführen, um eine freigegebene Private Link-Ressource zu erstellen:

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview --body @create-pe.json
```

Der Inhalt der Datei *create-pe.json*, die den Anforderungstext für die API darstellt, lautet so:

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve"
      }
}
```

Der Prozess der Erstellung eines ausgehenden privaten Endpunkts ist ein zeitintensiver (asynchroner) Vorgang. Wie bei allen asynchronen Azure-Vorgängen gibt der `PUT`-Aufruf einen `Azure-AsyncOperation`-Headerwert zurück, der wie folgt aussieht:

```plaintext
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview"
```

Sie können diesen URI in regelmäßigen Abständen abrufen, um den Status des Vorgangs zu erhalten.

Wenn Sie die CLI verwenden, können Sie den Status abfragen, indem Sie den Wert `Azure-AsyncOperationHeader` manuell abfragen.

```donetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview
```

Warten Sie, bis sich der Status in „Succeeded“ (Erfolgreich) ändert, bevor Sie mit den nächsten Schritten fortfahren.

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-function"></a>Schritt 2a: Genehmigen der privaten Endpunktverbindung für die Funktion

> [!NOTE]
> In diesem Abschnitt durchlaufen Sie mithilfe des Azure-Portals den Genehmigungsflow für einen privaten Endpunkt zur Azure-Funktion. Alternativ könnten Sie die [REST-API](/rest/api/appservice/web-apps/approve-or-reject-private-endpoint-connection) verwenden, die über den App Service-Anbieter zur Verfügung steht.

> [!IMPORTANT]
> Nachdem Sie die private Endpunktverbindung genehmigt haben, kann über das öffentliche Netzwerk nicht mehr auf die Funktion zugegriffen werden. Möglicherweise müssen Sie andere private Endpunkte in Ihrem eigenen virtuellen Netzwerk erstellen, um auf den Funktionsendpunkt zuzugreifen.

1. Wählen Sie im Azure-Portal die Registerkarte **Netzwerk** Ihrer Funktions-App aus, und navigieren Sie zu **Private Endpunktverbindungen**. Klicken Sie auf **Verbindungen mit privaten Endpunkten konfigurieren**. Nachdem der asynchrone Vorgang erfolgreich ausgeführt wurde, sollte eine Anforderung für eine private Endpunktverbindung mit der Anforderungsnachricht aus dem vorherigen API-Aufruf vorliegen.

   :::image type="content" alt-text="Der Screenshot des Azure-Portals zeigt den Bereich „Private Endpunktverbindungen“." source="media\howto-shared-private-endpoints\portal-function-approve-private-endpoint.png" :::

1. Wählen Sie den privaten Endpunkt aus, den der Azure SignalR-Dienst erstellt hat. Identifizieren Sie in der Spalte **Privater Endpunkt** die private Endpunktverbindung anhand des Namens, der in der vorherigen API angegeben wurde, wählen Sie **Genehmigen** aus.

   Vergewissern Sie sich, dass die private Endpunktverbindung wie im folgenden Screenshot angezeigt wird. Es könnte zwischen ein und zwei Minuten dauern, bis der Status im Portal aktualisiert wird.

   :::image type="content" alt-text="Screenshot des Azure-Portals, der im Bereich „Private Endpunktverbindungen“ den Status „Genehmigt“ zeigt." source="media\howto-shared-private-endpoints\portal-function-approved-private-endpoint.png" :::

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Schritt 2b: Abfragen des Status der freigegebenen Private Link-Ressource

Es dauert Minuten, bis die Genehmigung an den Azure SignalR-Dienst weitergegeben wird. Zur Bestätigung, dass die freigegebene Private Link-Ressource nach der Genehmigung aktualisiert wurde, können Sie auch den „Verbindungsstatus“ mithilfe der GET-API abrufen.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/signalr/contoso-signalr/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview
```

Dadurch wird JSON-Code zurückgegeben, bei dem der Verbindungsstatus im Abschnitt „properties“ als „status“ angezeigt wird.

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve",
        "status": "Approved",
        "provisioningState": "Succeeded"
      }
}

```

Wenn der „Bereitstellungsstatus“ (`properties.provisioningState`) der Ressource `Succeeded` und der „Verbindungsstatus“ (`properties.status`) `Approved` lautet, bedeutet dies, dass die freigegebene Private Link-Ressource funktionsfähig ist und der Azure SignalR-Dienst über den privaten Endpunkt kommunizieren kann.

### <a name="step-3-verify-upstream-calls-are-from-a-private-ip"></a>Schritt 3: Überprüfen, ob Upstreamaufrufe von einer privaten IP-Adresse stammen

Nachdem der private Endpunkt eingerichtet wurde, können Sie überprüfen, ob eingehende Aufrufe von einer privaten IP-Adresse stammen, indem Sie den `X-Forwarded-For`-Header auf der Upstreamseite überprüfen.

:::image type="content" alt-text="Screenshot des Azure-Portals, der zeigt, dass eingehende Anforderungen von einer privaten IP-Adresse stammen." source="media\howto-shared-private-endpoints\portal-function-log.png" :::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu privaten Endpunkten:

+ [Was sind private Endpunkte?](../private-link/private-endpoint-overview.md)