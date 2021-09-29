---
title: Beispiele für Azure Resource Manager-Vorlagen – Event Grid | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Liste mit Azure Resource Manager-Vorlagenbeispielen für Azure Event Grid auf GitHub.
ms.topic: sample
ms.date: 09/28/2021
ms.openlocfilehash: 3c2188e99e62a1db3f71b4064a09535bc24c0c68
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210825"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-Vorlagen für Event Grid

Informationen zur JSON-Syntax und zu den Eigenschaften, die in einer Vorlage verwendet werden sollen, finden Sie unter [Microsoft.EventGrid resource types](/azure/templates/microsoft.eventgrid/allversions) (Microsoft.EventGrid-Ressourcentypen). Die folgende Tabelle enthält Links zu Azure Resource Manager-Vorlagen für Event Grid:

## <a name="event-grid-subscriptions"></a>Event Grid-Abonnements
- [Erstellen eines benutzerdefinierten Event Grid-Themas und eines Abonnements in Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid): Hier wird ein benutzerdefiniertes Event Grid-Thema bereitgestellt. Darüber hinaus wird ein Abonnement für dieses benutzerdefinierte Thema erstellt, von dem ein WebHook-Endpunkt verwendet wird. 
- [Erstellen eines benutzerdefinierten Event Grid-Themas und Senden von Ereignissen an Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler): Hier wird ein Event Grid-Abonnement für ein benutzerdefiniertes Thema erstellt. Das Abonnement verwendet eine Event Hub-Instanz für den Endpunkt. 
- [Erstellen eines Event Grid-Abonnements für Ressourcenereignisse](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-resource-events-to-webhook): Hier werden Ereignisse für eine Ressourcengruppe oder für ein Azure-Abonnement abonniert. Die Ressourcengruppe, die Sie im Rahmen der Bereitstellung als Ziel angeben, ist die Quelle der Ereignisse. Das Abonnement verwendet einen Webhook für den Endpunkt. 
- [Erstellen eines Azure Blob Storage-Kontos mit Event Grid-Abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-subscription-and-storage): Hier werden ein Azure Blob Storage-Konto bereitgestellt und Ereignisse für dieses Speicherkonto abonniert. 

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die folgenden Beispiele an:

- [PowerShell-Beispiele](powershell-samples.md)
- [CLI-Beispiele](cli-samples.md)
