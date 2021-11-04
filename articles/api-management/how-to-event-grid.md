---
title: Senden von Ereignissen von Azure API Management an Event Grid
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie Event Grid-Ereignisse für Ihre Azure API Management-Instanz aktivieren und dann Ereignisse an eine Beispielanwendung senden.
author: dlepow
ms.topic: how-to
ms.service: api-management
ms.author: danlep
ms.date: 11/2/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b98e958869b30a90d7006020768cdc7d8c1d62c7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063060"
---
# <a name="send-events-from-api-management-to-event-grid"></a>Senden von Ereignissen von API Management an Event Grid

API Management integriert sich in [Azure Event Grid](../event-grid/overview.md), sodass Sie Ereignisbenachrichtigungen an andere Dienste senden und nachfolgende Prozesse auslösen können. Event Grid ist ein vollständig verwalteter Ereignisroutingdienst, der ein Veröffentlichen-Abonnieren-Modell verwendet. Event Grid verfügt über integrierte Unterstützung für Azure-Dienste wie [Azure Functions](../azure-functions/functions-overview.md) und [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und kann mithilfe von Webhooks Ereigniswarnungen an Nicht-Azure-Dienste übermitteln.

Wenn Sie beispielsweise die Integration in Event Grid verwenden, können Sie eine Anwendung erstellen, die eine Datenbank aktualisiert, ein Abrechnungskonto erstellt und jedes Mal eine E-Mail-Benachrichtigung sendet, wenn ein Benutzer ihrer API Management-Instanz hinzugefügt wird.

In diesem Artikel abonnieren Sie Event Grid-Ereignisse in Ihrer API Management-Instanz, lösen Ereignisse aus und senden die Ereignisse an einen Endpunkt, der die Daten verarbeitet. Zur Vereinfachung senden Sie Ereignisse an eine Beispiel-Web-App, die die Nachrichten sammelt und anzeigt:

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-intro.png" alt-text="API Management-Ereignisse im Event Grid Viewer":::

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
- Arbeiten Sie die folgende Schnellstartanleitung durch, falls Sie nicht bereits über einen API Management-Dienst verfügen: [Erstellen einer Azure API Management-Instanz](get-started-create-service-instance.md).
- Aktivieren Sie eine [systemseitig zugewiesene verwaltete Identität](api-management-howto-use-managed-service-identity.md#create-a-system-assigned-managed-identity) in Ihrer API Management-Instanz.
- Erstellen Sie eine [Ressourcengruppe](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups), wenn Sie über keine verfügen, in der der Beispielendpunkt bereitgestellt werden soll.

## <a name="create-an-event-endpoint"></a>Erstellen eines Ereignisendpunkts

In diesem Abschnitt wird beschrieben, wie Sie mit einer Resource Manager-Vorlage eine vorab erstellte Beispielwebanwendung in Azure App Service bereitstellen. In einem anderen Abschnitt wird erläutert, wie Sie die Event Grid-Ereignisse Ihrer API Management-Instanz abonnieren und die App als den Endpunkt festlegen, an den Ereignisse gesendet werden.

Um die Beispiel-App bereitzustellen, können Sie die Azure CLI, Azure PowerShell oder das Azure-Portal verwenden. Im folgenden Beispiel wird der Befehl [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) in der Azure CLI verwendet.

* Legen Sie `RESOURCE_GROUP_NAME` auf den Namen einer vorhandenen Ressourcengruppe fest.
* Legen Sie `SITE_NAME` auf einen eindeutigen Namen für Ihre Web-App fest.

  Der Websitename muss innerhalb von Azure eindeutig sein, da er Bestandteil des vollqualifizierten Domänennamens (FQDN) der Web-App ist. In einem Abschnitt weiter unten navigieren Sie in einem Webbrowser zum FQDN der App und lassen sich dort die Ereignisse anzeigen.

```azurecli-interactive
RESOURCE_GROUP_NAME=<your-resource-group-name>
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Öffnen Sie nach der erfolgreichen Bereitstellung (dieser Vorgang kann einige Minuten dauern) einen Browser, und navigieren Sie zu Ihrer Web-App, um sicherzustellen, dass diese ausgeführt wird:

`https://<your-site-name>.azurewebsites.net`

Die Beispiel-App sollte nun gerendert werden, und es sollten keine Ereignismeldungen angezeigt werden.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-api-management-events"></a>Abonnieren von API Management-Ereignissen

In Event Grid abonnieren Sie ein *Thema*, um festzulegen, welche Ereignisse Sie nachverfolgen möchten und wohin diese gesendet werden sollen. Hier erstellen Sie ein Abonnement von Ereignissen in Ihrer API Management-Instanz.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie **Ereignisse > + Ereignisabonnement** aus. 
1. Auf der Registerkarte **Basic**:
    * Geben Sie einen aussagekräftigen **Namen** für das Ereignisabonnement ein.
    * Wählen Sie unter **Ereignistypen** einen oder mehrere API Management-Ereignistypen aus, die an Event Grid gesendet werden sollen. Wählen Sie für das Beispiel in diesem Artikel mindestens **Microsoft.APIManagement.ProductCreated** aus. 
    * Wählen Sie unter **Endpunktdetails** den Ereignistyp **Webhook** aus, klicken Sie auf **Endpunkt auswählen**, und geben Sie Ihre Web-App-URL, gefolgt von `api/updates`, ein. Beispiel: `https://myapp.azurewebsites.net/api/updates`.
    * Wählen Sie **Auswahl bestätigen** aus.
1. Übernehmen Sie die Einstellungen auf den verbleibenden Registerkarten mit ihren Standardwerten, und wähle dann **Erstellen** aus.

    :::image type="content" source="media/how-to-event-grid/create-event-subscription.png" alt-text="Erstellen eines Ereignisabonnements im Azure-Portal":::

## <a name="trigger-and-view-events"></a>Auslösen und Anzeigen von Ereignissen

Da die Beispiel-App nun funktioniert und ausgeführt wird und Sie Ihre API Management-Instanz mit Event Grid abonniert haben, können Sie nun Ereignisse generieren.

Beispielsweise [erstellen Sie ein Produkt](./api-management-howto-add-products.md) in Ihrer API Management-Instanz. Wenn Ihr Ereignisabonnement das Ereignis **Microsoft.APIManagement.ProductCreated** umfasst, löst das Erstellen des Produkts ein Ereignis aus, das an Ihren Web-App-Endpunkt gepusht wird. 

Navigieren Sie zu Ihrer Event Grid Viewer-Web-App, wo das `ProductCreated`-Ereignis angezeigt werden sollte. Wählen Sie die Schaltfläche neben dem Ereignis aus, um die Details anzuzeigen. 

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-product-created.png" alt-text="Ereignis „Produkt erstellt“ im Event Grid Viewer":::

## <a name="event-grid-event-schema"></a>Event Grid-Ereignisschema

API Management-Ereignisdaten umfassen den `resourceUri`, der die API Management-Ressource identifiziert, die das Ereignis ausgelöst hat. Ausführliche Informationen zum API Management-Ereignismeldungsschema finden Sie in der Event Grid Dokumentation:

[Azure Event Grid-Ereignisschema für API Management](../event-grid/event-schema-api-management.md)

## <a name="next-steps"></a>Nächste Schritte

* [Auswahl von Azure-Messagingdiensten: Event Grid, Event Hubs und Service Bus](../event-grid/compare-messaging-services.md)
* Weitere Informationen zum [Abonnieren von Ereignissen](../event-grid/subscribe-through-portal.md).
