---
title: Hinzufügen einer verwalteten Identität zu einer Rolle am Azure Event Grid-Ziel
description: In diesem Artikel wird beschrieben, wie Sie Azure-Rollen verwaltete Identitäten an Zielen wie Azure Service Bus und Azure Event Hubs hinzufügen.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: c2bfc10f0019b6753e9290d20c84ba5e2bbb59fe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347149"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>Gewähren des Zugriffs auf ein Event Grid-Ziel für die verwaltete Identität
In diesem Abschnitt wird beschrieben, wie Sie die Identität für das Systemthema, das benutzerdefinierte Thema oder die benutzerdefinierte Domäne einer Azure-Rolle hinzufügen. 

## <a name="prerequisites"></a>Voraussetzungen
Weisen Sie anhand der Anweisungen in den folgenden Artikeln eine systemseitig zugewiesene verwaltete Identität zu:

- [Benutzerdefinierte Themen oder Domänen](enable-identity-custom-topics-domains.md)
- [Systemthemen](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Unterstützte Ziele und Azure-Rollen
Nachdem Sie die Identität für das benutzerdefinierte Event Grid-Thema bzw. die benutzerdefinierte Event Grid-Domäne aktiviert haben, erstellt Azure automatisch eine Identität in Azure Active Directory. Fügen Sie diese Identität den entsprechenden Azure-Rollen hinzu, damit das benutzerdefinierte Thema bzw. die benutzerdefinierte Domäne Ereignisse an unterstützte Ziele weiterleiten kann. Fügen Sie die Identität z. B. der Rolle **Azure Event Hubs-Datensender** für einen Azure Event Hubs-Namespace hinzu, damit das benutzerdefinierte Event Grid-Thema Ereignisse an Event Hubs in diesem Namespace weiterleiten kann. 

Derzeit unterstützt Azure Event Grid benutzerdefinierte Themen oder Domänen, die mit der vom System zugewiesenen verwalteten Identität konfiguriert wurden, um Ereignisse an die folgenden Ziele weiterzuleiten. Diese Tabelle enthält auch die Rollen, die die Identität aufweisen sollte, damit das benutzerdefinierte Thema die Ereignisse weiterleiten kann.

| Destination | Azure-Rolle | 
| ----------- | --------- | 
| Service Bus-Warteschlangen und -Themen | [Azure Service Bus-Datensender](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs-Datensender](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Mitwirkender an Speicherblobdaten](../storage/blobs/assign-azure-role-data-access.md) |
| Azure Queue Storage |[Absender der Speicherwarteschlangen-Datennachricht](../storage/blobs/assign-azure-role-data-access.md) | 

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals
Sie können das Azure-Portal verwenden, um die Identität des benutzerdefinierten Themas bzw. der benutzerdefinierten Domäne einer entsprechenden Rolle zuzuweisen, damit das Thema/die Domäne Ereignisse an das Ziel weiterleiten kann. 

Im folgenden Beispiel wird eine verwaltete Identität für ein benutzerdefiniertes Event Grid-Thema mit dem Namen **msitesttopic** der Rolle **Azure Service Bus-Datensender** für einen Service Bus-Namespace hinzugefügt, der eine Warteschlangen- oder Themenressource enthält. Wenn Sie das benutzerdefinierte Event Grid-Thema der Rolle auf Namespaceebene hinzufügen, kann es Ereignisse an alle Entitäten im Namespace weiterleiten. 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem **Service Bus-Namespace**. 
1. Klicken Sie im linken Bereich auf **Zugriffssteuerung**. 
1. Wählen Sie im Abschnitt **Rollenzuweisung hinzufügen (Vorschau)** die Option **Hinzufügen** aus. 

    :::image type="content" source="./media/add-identity-roles/add-role-assignment-menu.png" alt-text="Abbildung: Auswahl des Menüs „Rollenzuweisung hinzufügen (Vorschau)“":::
1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** die Option **Azure Service Bus-Datensender** und dann **Weiter** aus.  
    
    :::image type="content" source="./media/add-identity-roles/select-role.png" alt-text="Abbildung: Auswahl der Rolle „Azure Service Bus-Datensender“":::
1. Führen Sie auf der Registerkarte **Mitglieder** die folgenden Schritte aus: 
    1. Wählen Sie die Option **Benutzer, Gruppe oder Dienstprinzipal** aus, und klicken Sie auf **+ Mitglieder auswählen**. Für die Option **Verwaltete Identität** werden Event Grid-Identitäten noch nicht unterstützt. 
    1. Suchen Sie im Fenster **Mitglieder auswählen** nach dem Dienstprinzipal, der den gleichen Namen wie Ihr benutzerdefiniertes Thema hat, und wählen Sie ihn aus. Im folgenden Beispiel ist dies **spcustomtopic0728**.
    
        :::image type="content" source="./media/add-identity-roles/select-managed-identity-option.png" alt-text="Abbildung: Auswahl der Option „Benutzer, Gruppe oder Dienstprinzipal“":::    
    1. Klicken Sie im Fenster **Mitglieder auswählen** auf **Auswählen**. 

        :::image type="content" source="./media/add-identity-roles/managed-identity-selected.png" alt-text="Abbildung: Auswahl der Option „Verwaltete Identität“":::            
1. Wählen Sie auf der Registerkarte **Mitglieder** die Option **Weiter** aus. 

    :::image type="content" source="./media/add-identity-roles/members-select-next.png" alt-text="Abbildung: Auswahl der Schaltfläche „Weiter“ auf der Seite „Mitglieder“":::                
1. Wählen Sie auf der Seite **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, nachdem Sie die Einstellungen überprüft haben. 

Die Schritte entsprechen dem Hinzufügen einer Identität zu anderen Rollen, die in der Tabelle aufgeführt sind. 

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI
Im Beispiel in diesem Abschnitt wird gezeigt, wie Sie mit der Azure-Befehlszeilenschnittstelle eine Identität einer Azure-Rolle hinzufügen. Die Beispielbefehle beziehen sich auf benutzerdefinierte Event Grid-Themen. Die Befehle für Event Grid-Domänen sind ähnlich. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Abrufen der Prinzipal-ID für die Systemidentität des benutzerdefinierten Themas 
Rufen Sie zunächst die Prinzipal-ID der vom System verwalteten Identität des benutzerdefinierten Themas ab, und weisen Sie die Identität den entsprechenden Rollen zu.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Erstellen einer Rollenzuweisung für Event Hubs in verschiedenen Bereichen 
Im folgenden Beispiel für die Befehlszeilenschnittstelle wird veranschaulicht, wie die Identität eines benutzerdefinierten Themas der Rolle **Azure Event Hubs-Datensender** auf Namespaceebene oder auf Event Hub-Ebene hinzugefügt wird. Wenn Sie die Rollenzuweisung auf Namespaceebene erstellen, kann das benutzerdefinierte Thema Ereignisse an alle Event Hubs in diesem Namespace weiterleiten. Wenn Sie das Thema auf Event Hub-Ebene erstellen, kann das benutzerdefinierte Thema Ereignisse nur an diesen Event Hub weiterleiten. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Erstellen einer Rollenzuweisung für ein Service Bus-Thema in verschiedenen Bereichen 
Im folgenden Beispiel für die Befehlszeilenschnittstelle wird gezeigt, wie die Identität eines benutzerdefinierten Event Grid-Themas zur Rolle **Azure Service Bus-Datensender** auf Namespaceebene oder auf Ebene des Service Bus-Themas hinzugefügt wird. Wenn Sie die Rollenzuweisung auf Namespaceebene erstellen, kann das Event Grid-Thema Ereignisse an alle Entitäten (Service Bus-Warteschlangen oder -Themen) innerhalb dieses Namespace weiterleiten. Beim Erstellen einer Rollenzuweisung auf Service Bus-Warteschlangen- oder -Themenebene kann das benutzerdefinierte Event Grid-Thema Ereignisse nur an die betreffende Service Bus-Warteschlange oder das Service Bus-Thema weiterleiten. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Nächste Schritte
Sie haben dem Systemthema, dem benutzerdefinierten Thema oder der benutzerdefinierten Domäne eine systemseitig zugewiesene Identität zugewiesen und die Identität den entsprechenden Rollen an Zielen hinzugefügt. Lesen Sie nun die Informationen unter [Ereignisübermittlung mit einer verwalteten Identität](managed-service-identity.md), um zu erfahren, wie Sie mithilfe der Identität Ereignisse an Ziele übermitteln.