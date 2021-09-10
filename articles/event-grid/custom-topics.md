---
title: Benutzerdefinierte Themen in Azure Event Grid
description: Enthält eine Beschreibung der benutzerdefinierten Themen in Azure Event Grid.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 54d331d2080d8adf96bf39c9250e7d6888723c1d
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727750"
---
# <a name="custom-topics-in-azure-event-grid"></a>Benutzerdefinierte Themen in Azure Event Grid
Ein Event Grid-Thema stellt einen Endpunkt bereit, an den die Ereignisquelle Ereignisse sendet. Der Herausgeber erstellt das Thema und legt fest, ob eine Ereignisquelle ein Thema oder mehrere Themen benötigt. Ein Event Grid-Thema wird für eine Sammlung ähnlicher Ereignisse verwendet. Um auf bestimmte Arten von Ereignissen zu reagieren, legen Abonnenten fest, welche Themen sie abonnieren.

**Benutzerdefinierte Themen** sind Anwendungs- und Drittanbieterthemen. Wenn Sie ein benutzerdefiniertes Thema erstellen oder Zugriff darauf erhalten, wird das benutzerdefinierte Thema in Ihrem Abonnement angezeigt. 

Beim Entwerfen Ihrer Anwendung können Sie flexibel entscheiden, wie viele Themen erstellt werden sollen. Erstellen Sie bei großen Lösungen für **jede Kategorie mit verwandten Ereignissen** ein **benutzerdefiniertes Thema**. Denken Sie beispielsweise an eine Anwendung, die Ereignisse im Zusammenhang mit der Änderung von Benutzerkonten und der Verarbeitung von Bestellungen sendet. Es ist unwahrscheinlich, dass ein Ereignishandler beide Ereigniskategorien benötigt. Erstellen Sie zwei benutzerdefinierte Themen, und lassen Sie Ereignishandler das jeweils relevante Thema abonnieren. Für kleine Lösungen empfiehlt es sich ggf., alle Ereignisse an ein Thema zu senden. Ereignisabonnenten können nach den gewünschten Ereignistypen filtern.

## <a name="event-schema"></a>Ereignisschema
Eine ausführliche Übersicht über Ereignisschemen finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). Für benutzerdefinierte Themen bestimmt der Ereignisherausgeber das **Datenobjekt**. Die Daten auf oberster Ebene müssen die gleichen Felder wie über Standardressourcen definierte Ereignisse aufweisen.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Die folgenden Abschnitte enthalten Links zu Tutorials zur Erstellung von benutzerdefinierten Themen mit dem Azure-Portal, der CLI, PowerShell und ARM-Vorlagen (Azure Resource Manager). 


## <a name="azure-portal-tutorials"></a>Tutorials zum Azure-Portal
|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit dem Azure-Portal](custom-event-quickstart-portal.md) | Zeigt die Verwendung des Portals zum Senden von benutzerdefinierten Ereignissen. |
| [Schnellstart: Weiterleiten von benutzerdefinierten Ereignissen an Azure Queue Storage](custom-event-to-queue-storage.md) | Beschreibt das Senden von benutzerdefinierten Ereignisse an eine Queue Storage-Instanz. |
| [Gewusst wie: Posten eines Ereignisses in einem benutzerdefinierten Thema](post-to-custom-topic.md) | Zeigt das Posten eines Ereignisses in einem benutzerdefinierten Thema. |


## <a name="azure-cli-tutorials"></a>Tutorials zur Azure CLI
|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure CLI](custom-event-quickstart.md) | Zeigt die Verwendung von Azure CLI zum Senden von benutzerdefinierten Ereignissen. |
| [Azure CLI: Erstellen eines benutzerdefinierten Event Grid-Themas](./scripts/event-grid-cli-create-custom-topic.md)|Beispielskript, mit dem ein benutzerdefiniertes Thema erstellt wird. Das Skript ruft den Endpunkt und einen Schlüssel ab.|
| [Azure CLI: Abonnieren von Ereignissen für ein benutzerdefiniertes Thema](./scripts/event-grid-cli-subscribe-custom-topic.md)|Beispielskript, mit dem ein Abonnement für ein benutzerdefiniertes Thema erstellt wird. Sendet Ereignisse an einen WebHook.|

## <a name="azure-powershell-tutorials"></a>Tutorials zu Azure PowerShell
|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure PowerShell](custom-event-quickstart-powershell.md) | Zeigt die Verwendung von Azure PowerShell zum Senden von benutzerdefinierten Ereignissen. |
| [PowerShell: Erstellen eines benutzerdefinierten Event Grid-Themas](./scripts/event-grid-powershell-create-custom-topic.md)|Beispielskript, mit dem ein benutzerdefiniertes Thema erstellt wird. Das Skript ruft den Endpunkt und einen Schlüssel ab.|
| [PowerShell: Abonnieren von Ereignissen für ein benutzerdefiniertes Thema](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Beispielskript, mit dem ein Abonnement für ein benutzerdefiniertes Thema erstellt wird. Sendet Ereignisse an einen WebHook.|

## <a name="arm-template-tutorials"></a>Tutorials zu ARM-Vorlagen
|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Resource Manager-Vorlage: Benutzerdefiniertes Thema und WebHook-Endpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid) | Eine Resource Manager-Vorlage, mit der ein benutzerdefiniertes Thema und ein Abonnement für dieses benutzerdefinierte Thema erstellt werden. Sendet Ereignisse an einen WebHook. |
| [Resource Manager-Vorlage: Benutzerdefiniertes Thema und Event Hubs-Endpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler)| Eine Resource Manager-Vorlage, mit der ein Abonnement für ein benutzerdefiniertes Thema erstellt wird. Sendet Ereignisse an Azure Event Hubs. |

> [!NOTE]
> Azure Digital Twins kann Ereignisbenachrichtigungen an benutzerdefinierte Themen routen, die Sie mit Event Grid erstellen. Weitere Informationen finden Sie unter [Verwalten von Endpunkten und Routen in Azure Digital Twins](../digital-twins/how-to-manage-routes.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln: 

- [Systemthemen](system-topics.md)
- [Domänen](event-domains.md)