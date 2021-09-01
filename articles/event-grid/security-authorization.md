---
title: Azure Event Grid – Sicherheit und Authentifizierung
description: In diesem Artikel werden Azure Event Grid und die zugehörigen Begriffe beschrieben.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: 74f4cfdb40d13a56d21727f3ced0477276b578c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345787"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorisieren des Zugriffs auf Event Grid-Ressourcen
Azure Event Grid bietet die Möglichkeit, den Umfang zu steuern, in dem unterschiedliche Benutzer Zugriff auf verschiedene **Verwaltungsvorgänge** erhalten, z. B. Auflisten und Erstellen von Ereignisabonnements und Generieren von Schlüsseln. Event Grid verwendet die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC).

> [!NOTE]
> Event Grid unterstützt Azure RBAC nicht zum Veröffentlichen von Ereignissen in Event Grid-Themen oder -Domänen. Verwenden Sie einen SAS-Schlüssel (Shared Access Signature) oder ein SAS-Token zum Authentifizieren von Clients, die Ereignisse veröffentlichen. Weitere Informationen finden Sie unter [Authentifizieren von Veröffentlichungsclients (Azure Event Grid)](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>Vorgangstypen
Führen Sie den folgenden Azure CLI-Befehl aus, um eine Liste der von Azure Event Grid unterstützten Vorgänge abzurufen: 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

Die folgenden Vorgänge geben potenziell geheime Informationen zurück, die aus normalen Lesevorgängen herausgefiltert werden. Es empfiehlt sich, den Zugriff auf diese Vorgänge einzuschränken. 

* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action


## <a name="built-in-roles"></a>Integrierte Rollen
In Event Grid sind die folgenden drei integrierten Rollen verfügbar. 

Die Rollen „Event Grid-Abonnementleser“ und „Event Grid-Abonnementmitwirkender“ dienen der Verwaltung von Ereignisabonnements. Sie sind wichtig beim Implementieren von [Ereignisdomänen](event-domains.md), da sie Benutzern die erforderlichen Berechtigungen erteilen, um Themen in Ihrer Ereignisdomäne zu abonnieren. Diese Rollen gelten für Ereignisabonnements und gewähren keinen Zugriff auf Aktionen. Das heißt, sie berechtigen Sie beispielsweise nicht zum Erstellen von Themen.

Mit der Rolle „Event Grid-Mitwirkender“ können Sie Event Grid-Ressourcen erstellen und verwalten. 


| Role | BESCHREIBUNG |
| ---- | ----------- | 
| [Event Grid-Abonnementleser](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Ermöglicht das Lesen von Event Grid-Ereignisabonnements. |
| [Event Grid-Abonnementmitwirkender](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Ermöglicht die Verwaltung von Vorgängen des Event Grid-Ereignisabonnements. |
| [Event Grid-Mitwirkender](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Ermöglich das Erstellen und Verwalten von Event Grid-Ressourcen. |
| [Event Grid-Datensender](../role-based-access-control/built-in-roles.md#eventgrid-data-sender) | Ermöglicht Ihnen das Senden von Ereignissen an ein Event Grid-Thema. |


> [!NOTE]
> Klicken Sie für weitere Informationen zu den Rollen auf den jeweiligen Link in der linken Spalte, um zu dem entsprechenden Artikel zu gelangen. Eine Anleitung zur Zuweisung von Benutzern oder Gruppen zu RBAC-Rollen finden Sie in [diesem Artikel](../role-based-access-control/quickstart-assign-role-user-portal.md).


## <a name="custom-roles"></a>Benutzerdefinierte Rollen

Wenn Sie Berechtigungen angeben müssen, die sich von den integrierten Rollen unterscheiden, können Sie benutzerdefinierte Rollen erstellen.

Im Folgenden finden Sie Beispiele für Event Grid-Rollendefinitionen, die Benutzern das Durchführen unterschiedlicher Aktionen ermöglichen. Diese benutzerdefinierten Rollen unterscheiden sich von den integrierten Rollen, da sie einen breiteren Zugriff ermöglichen als Ereignisabonnements.

**EventGridReadOnlyRole.json**: Ausschließliches Zulassen schreibgeschützter Operationen.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: Zulassen eingeschränkter Post-Aktionen, nicht aber von Löschaktionen.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: Zulassen aller Event Grid-Aktionen.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Benutzerdefinierte Rollen können mit [PowerShell](../role-based-access-control/custom-roles-powershell.md), der [Azure CLI](../role-based-access-control/custom-roles-cli.md) oder der [REST-API](../role-based-access-control/custom-roles-rest.md) erstellt werden.



### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Alle Ereignisse oder Daten, die vom Event Grid-Dienst auf den Datenträger geschrieben werden, werden unter Verwendung eines von Microsoft verwalteten Schlüssels verschlüsselt, um die Verschlüsselung der ruhenden Daten zu gewährleisten. Ereignisse und Daten werden außerdem maximal 24 Stunden lang aufbewahrt (in Übereinstimmung mit der [Event Grid-Wiederholungsrichtlinie](delivery-and-retry.md)). Nach 24 Stunden oder nach Ablauf der Ereignislebensdauer (je nachdem, welcher Zeitraum kürzer ist) werden alle Ereignisse und Daten von Event Grid gelöscht.

## <a name="permissions-for-event-subscriptions"></a>Berechtigungen für Ereignisabonnements
Wenn Sie einen Ereignishandler verwenden, der kein Webhook ist (z.B. einen Event Hub oder Warteschlangenspeicher), benötigen Sie Schreibzugriff auf diese Ressource. Durch diese Berechtigungsüberprüfung wird verhindert, dass ein nicht autorisierter Benutzer Ereignisse an Ihre Ressource sendet.

Sie benötigen die Berechtigung **Microsoft.EventGrid/EventSubscriptions/Write** für die Ressource, die als Ereignisquelle verwendet wird. Sie benötigen diese Berechtigung, da Sie ein neues Abonnement im Gültigkeitsbereich der Ressource schreiben. Die angeforderte Ressource variiert abhängig davon, ob Sie ein Systemthema oder ein benutzerdefiniertes Thema abonnieren. In diesem Abschnitt werden beide Typen beschrieben.

### <a name="system-topics-azure-service-publishers"></a>Systemthemen (Azure-Dienstherausgeber)
Für Systemthemen benötigen Sie die Berechtigung zum Schreiben eines neuen Abonnements im Gültigkeitsbereich der Ressource, die das Ereignis veröffentlicht, wenn Sie nicht Besitzer oder Mitwirkender der Quellressource sind. Das Format der Ressource ist: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`.

Beispiel: Um ein Ereignis im Speicherkonto **myacct** zu abonnieren, benötigen Sie die Berechtigung Microsoft.EventGrid/EventSubscriptions/Write für: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`.

### <a name="custom-topics"></a>Benutzerdefinierte Themen
Für benutzerdefinierte Themen benötigen Sie die Berechtigung zum Schreiben eines neuen Ereignisabonnements im Gültigkeitsbereich des Event Grid-Themas. Das Format der Ressource ist: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`.

Beispiel: Um das benutzerdefinierte Thema **mytopic** zu abonnieren, benötigen Sie die Berechtigung Microsoft.EventGrid/EventSubscriptions/Write für: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`.



## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
