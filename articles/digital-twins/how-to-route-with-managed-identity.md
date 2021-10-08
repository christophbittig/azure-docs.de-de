---
title: Weiterleiten von Ereignissen mit einer verwalteten Identität
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie eine systemseitig zugewiesene Identität für Azure Digital Twins aktivieren und zum Weiterleiten von Ereignissen mithilfe des Azure-Portals oder der Befehlszeilenschnittstelle verwenden.
author: baanders
ms.author: baanders
ms.date: 6/15/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: subject-rbac-steps, contperf-fy21q4
ms.openlocfilehash: 4d50c40426d5fb687b28a965b9d921ef6fc4df38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651914"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events"></a>Aktivieren einer verwalteten Identität für die Weiterleitung von Azure Digital Twins-Ereignissen

In diesem Artikel wird beschrieben, wie Sie eine [systemseitig zugewiesene Identität für eine Azure Digital Twins-Instanz](concepts-security.md#managed-identity-for-accessing-other-resources) aktivieren und die Identität zum Weiterleiten von Ereignissen an unterstützte Weiterleitungsziele verwenden. Das Einrichten einer verwalteten Identität ist für das Routing nicht erforderlich, aber es kann der Instanz helfen, einfach auf andere durch Azure AD geschützte Ressourcen wie [Event Hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)-Ziele und [Azure Storage-Container](../storage/blobs/storage-blobs-introduction.md) zuzugreifen.

Die folgenden Schritte werden in diesem Artikel behandelt: 

1. Erstellen Sie eine Azure Digital Twins-Instanz mit einer systemseitig zugewiesenen Identität, oder aktivieren Sie eine systemseitig zugewiesene Identität für eine vorhandene Azure Digital Twins-Instanz. 
1. Fügen Sie entsprechende Rollen zur Identität hinzu. Beispielsweise können Sie der Identität die Rolle **Azure Event Hub Data Sender** (Azure Event Hub-Datensender), wenn Sie einen Event Hub-Endpunkt verwenden, oder die Rolle **Azure Service Bus-Datensender** zuweisen, wenn Sie einen Service Bus-Endpunkt verwenden.
1. Erstellen Sie einen Endpunkt in Azure Digital Twins, der systemseitig zugewiesene Identitäten für die Authentifizierung verwenden kann.

## <a name="enable-system-managed-identity-for-the-instance"></a>Aktivieren der vom System verwalteten Identität für die Instanz 

Wenn Sie eine systemseitig zugewiesene Identität für Ihre Azure Digital Twins-Instanz aktivieren, erstellt Azure automatisch eine Identität in [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) für diese. Diese Identität kann dann für die Authentifizierung bei Azure Digital Twins-Endpunkten für die Ereignisweiterleitung verwendet werden.

Sie können systemseitig verwaltete Identitäten für eine Azure Digital Twins-Instanz **als Teil der ersten Einrichtung der Instanz** oder **später für eine bereits vorhandene Instanz** aktivieren.

Beide dieser Erstellungsmethoden bieten Ihnen dieselben Konfigurationsoptionen und dasselbe Endergebnis für Ihre Instanz. In diesem Abschnitt werden beide Vorgehensweisen beschrieben.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Hinzufügen einer systemseitig verwalteten Identität während der Instanzerstellung

In diesem Abschnitt erfahren Sie, wie Sie eine systemseitig verwaltete Identität für eine Azure Digital Twins-Instanz aktivieren, während diese erstellt wird. Sie können die Identität unabhängig davon aktivieren, ob Sie die Instanz über das [Azure-Portal](https://portal.azure.com) oder die [Azure-Befehlszeilenschnittstelle](/cli/azure/what-is-azure-cli) erstellen. Wählen Sie die Registerkarten unten aus, um die Anweisungen für Ihre bevorzugte Methode anzuzeigen.

# <a name="portal"></a>[Portal](#tab/portal) 

Um während der Instanzerstellung im Portal eine verwaltete Identität hinzuzufügen, beginnen Sie wie gewohnt mit dem [Erstellen einer Instanz](how-to-set-up-instance-portal.md).

Die Option für systemseitig verwaltete Identitäten befindet sich auf der Registerkarte **Erweitert** des Instanzsetups.

Wählen Sie auf dieser Registerkarte die Option **Ein** für **Systemseitig verwaltete Identität** aus, um dieses Feature zu aktivieren.

:::image type="content" source="media/how-to-route-with-managed-identity/create-instance-advanced.png" alt-text="Screenshot: Azure-Portal mit der Registerkarte „Erweitert“ des Dialogfelds „Ressource erstellen“ für Azure Digital Twins. Die systemseitig verwaltete Identität ist aktiviert.":::

Anschließend können Sie die Navigationsschaltflächen unten verwenden, um mit der Einrichtung der Instanz fortzufahren.
   
# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Sie können an der Befehlszeilenschnittstelle dem Befehl `az dt create` den Parameter `--assign-identity` hinzufügen, der zum Erstellen der Instanz verwendet wird. (Weitere Informationen zu diesem Befehl finden Sie in der zugehörigen [Referenzdokumentation](/cli/azure/dt#az_dt_create) oder in den [allgemeinen Anweisungen zum Einrichten einer Azure Digital Twins-Instanz](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).)

Fügen Sie zum Erstellen einer Instanz mit einer systemseitig verwalteten Identität den `--assign-identity`-Parameter wie folgt hinzu:

```azurecli-interactive
az dt create --dt-name <new-instance-name> --resource-group <resource-group> --assign-identity
```

---

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Hinzufügen einer systemseitig verwalteten Identität zu einer vorhandenen Instanz

In diesem Abschnitt fügen Sie eine systemseitig verwaltete Identität zu einer bereits vorhandenen Azure Digital Twins-Instanz hinzu. Wählen Sie die Registerkarten unten aus, um die Anweisungen für Ihre bevorzugte Methode anzuzeigen.

# <a name="portal"></a>[Portal](#tab/portal) 

Öffnen Sie als Erstes das [Azure-Portal](https://portal.azure.com) in einem Browser.

1. Suchen Sie über die Suchleiste des Portals nach dem Namen Ihrer Instanz, und wählen Sie diesen aus, um die Details anzuzeigen.

1. Klicken Sie im linken Menü auf **Identität**.

1. Aktivieren Sie auf dieser Seite die Option **Ein**, um das Feature zu aktivieren.

1. Wählen Sie **Speichern** und zum Bestätigen **Ja** aus.

    :::image type="content" source="media/how-to-route-with-managed-identity/identity-digital-twins.png" alt-text="Screenshot: Azure-Portal mit der Seite „Identität“ für eine Azure Digital Twins-Instanz.":::

Nachdem die Änderungen gespeichert wurden, werden auf dieser Seite weitere Felder für die **Objekt-ID** und **Berechtigungen** der neuen Identität angezeigt.

Sie können die **Objekt-ID** bei Bedarf hier kopieren und die Schaltfläche **Berechtigungen** verwenden, um die Azure-Rollen anzuzeigen, die der Identität zugewiesen sind. Im nächsten Abschnitt richten Sie einige Rollen ein.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Auch hier können Sie Ihrer Instanz die Identität mit dem Befehl `az dt create` und dem Parameter `--assign-identity` hinzufügen. Anstatt einen neuen Namen einer zu erstellenden Instanz anzugeben, können Sie den Namen einer bereits vorhandenen Instanz angeben, um den `--assign-identity`-Wert dieser Instanz zu ändern.

Der Befehl zum **Aktivieren** der verwalteten Identität stimmt mit dem Befehl zum Erstellen einer Instanz mit einer systemseitig verwalteten Identität überein. Es wird lediglich der Wert des Instanznamenparameters geändert:

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity
```

Verwenden Sie den folgenden ähnlichen Befehl zum Festlegen von `--assign-identity` auf `false`, um die verwaltete Identität für einer Instanz zu **deaktivieren**, für die sie derzeit aktiviert ist.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity false
```

---

## <a name="assign-azure-roles-to-the-identity"></a>Zuweisen von Azure-Rollen zur Identität 

Nachdem Sie eine systemseitig zugewiesene Identität für Ihre Azure Digital Twins-Instanz erstellt haben, müssen Sie dieser die entsprechenden Rollen für die Authentifizierung bei verschiedenen Arten von [Endpunkten](concepts-route-events.md) zum Weiterleiten von Ereignissen an unterstützte Ziele zuweisen. In diesem Abschnitt werden die Rollenoptionen und das Zuweisen dieser zur systemseitig zugewiesenen Identität beschrieben.

>[!NOTE]
> Hierbei handelt es sich um einen wichtigen Schritt. Wenn Sie diesen Schritt nicht durchführen, kann die Identität nicht auf Ihre Endpunkte zugreifen und Ereignisse werden nicht übermittelt.

### <a name="supported-destinations-and-azure-roles"></a>Unterstützte Ziele und Azure-Rollen 

Im Folgenden finden Sie die mindestens erforderlichen Rollen, die eine Identität je nach Art von Ziel für den Zugriff auf einen Endpunkt benötigt. Rollen mit höheren Berechtigungen (beispielsweise Datenbesitzerrollen) funktionieren ebenfalls.

| Destination | Azure-Rolle |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs-Datensender |
| Azure-Servicebus | Azure Service Bus-Datensender |
| Azure-Speichercontainer | Mitwirkender an Storage-Blobdaten |

Weitere Informationen zu Endpunkten, Routen und den verschiedenen unterstützten Zielen für das Routing in Azure Digital Twins finden Sie unter [Ereignisrouten](concepts-route-events.md).

### <a name="assign-the-role"></a>Zuweisen der Rolle

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Wählen Sie die Registerkarten unten aus, um die Anweisungen für Ihre bevorzugte Methode anzuzeigen.

# <a name="portal"></a>[Portal](#tab/portal) 

Öffnen Sie zum Zuweisen einer Rolle zur Identität zunächst das [Azure-Portal](https://portal.azure.com) in einem Browser.

1. Navigieren Sie zu Ihrer Endpunktressource (Event Hub-Instanz, Service Bus-Instanz oder Speichercontainer), indem Sie ihren Namen über die Suchleiste des Portals suchen. 

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie der verwalteten Identität Ihrer Azure Digital Twins-Instanz mithilfe der folgenden Informationen die gewünschte Rolle zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Role | Wählen Sie die gewünschte Rolle im Dropdownmenü aus. |
    | Zugriff zuweisen zu | Klicken Sie unter **Systemseitig zugewiesene verwaltete Identität** auf **Digital Twins**. |
    | Member | Wählen Sie die verwaltete Identität Ihrer Azure Digital Twins-Instanz aus, der die Rolle zugewiesen wird. Der Name der verwalteten Identität stimmt mit dem Name der Instanz überein, suchen Sie also nach dem Namen Ihrer Azure Digital Twins-Instanz. |
    
    ![Seite „Rollenzuweisung hinzufügen“](../../includes/role-based-access-control/media/add-role-assignment-page.png)

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Sie können dem Befehl `az dt create` den Parameter `--scopes` hinzufügen, um die Identität einem oder mehreren Bereichen mit einer bestimmten Rolle zuzuweisen. Der Befehl kann mit diesem Parameter bereits bei der Erstellung der Instanz oder auch später verwendet werden, indem Sie den Namen einer bereits vorhandenen Instanz übergeben.

Hier sehen Sie ein Beispiel, bei dem eine Instanz mit einer systemseitig verwalteten Identität erstellt und dieser Identität eine benutzerdefinierte Rolle namens `MyCustomRole` in einem Event Hub zugewiesen wird.

```azurecli-interactive
az dt create --dt-name <instance-name> --resource-group <resource-group> --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource-group>/providers/Microsoft.EventHub/namespaces/<Event-Hubs-namespace>/eventhubs/<event-hub-name>" --role MyCustomRole
```

Weitere Beispiele für Rollenzuweisungen mit diesem Befehl finden Sie in der Referenzdokumentation zu [az dt create](/cli/azure/dt#az_dt_create).

Sie können auch die Befehlsgruppe [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) verwenden, um Rollen zu erstellen und zu verwalten. Mit diesem Befehl können Sie weitere Szenarien unterstützen, in denen Sie die Rollenzuweisung nicht mit dem Erstellungsbefehl gruppieren möchten.

---

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Erstellen eines Endpunkts mit identitätsbasierter Authentifizierung

Nachdem Sie eine systemseitig verwaltete Identität für Ihre Azure Digital Twins-Instanz eingerichtet und dieser die entsprechenden Rollen zugewiesen haben, können Sie Azure Digital Twins-[Endpunkte](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins) erstellen, die diese Identität für die Authentifizierung verwenden können. Diese Option ist nur für Event Hub- und Service Bus-Endpunkte verfügbar (sie wird nicht für Event Grid unterstützt).

>[!NOTE]
> Sie können einen Endpunkt nicht bearbeiten, der bereits mit einer schlüsselbasierten Identität erstellt wurde, um zur identitätsbasierten Authentifizierung zu wechseln. Sie müssen den Authentifizierungstyp auswählen, wenn der Endpunkt erstellt wird.

Wählen Sie die Registerkarten unten aus, um die Anweisungen für Ihre bevorzugte Methode anzuzeigen.

# <a name="portal"></a>[Portal](#tab/portal) 

Befolgen Sie zunächst die [Anweisungen zum Erstellen eines Azure Digital Twins-Endpunkts](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins).

Wenn Sie zum Schritt zum Eingeben der erforderlichen Informationen zum Endpunkttyp gelangen, wählen Sie den Authentifizierungstyp **Identity-based** (identitätsbasiert) aus.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Screenshot: Erstellen eines Endpunkts vom Typ „Event Hub“" lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Schließen Sie das Setup Ihres Endpunkts ab, und klicken Sie auf **Speichern**.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Zum Erstellen des Endpunkts mithilfe der Befehlszeilenschnittstelle wird dem Befehl `az dt endpoint create` der Parameter `--auth-type` hinzugefügt. (Weitere Informationen zu diesem Befehl finden Sie in der zugehörigen [Referenzdokumentation](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) oder in den [allgemeinen Anweisungen zum Einrichten eines Azure Digital Twins-Endpunkts](how-to-manage-routes.md#create-the-endpoint).)

Zum Erstellen eines Endpunkts, der die identitätsbasierte Authentifizierung verwendet, legen Sie den Authentifizierungstyp `IdentityBased` mit dem Parameter `--auth-type` fest. Im folgenden Beispiel wird diese Funktion für einen Event Hubs-Endpunkt veranschaulicht.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <endpoint-name> --eventhub-resource-group <eventhub-resource-group> --eventhub-namespace <eventhub-namespace> --eventhub <eventhub-name> --auth-type IdentityBased --dt-name <instance-name>
```

---

## <a name="considerations-for-disabling-system-managed-identities"></a>Überlegungen zum Deaktivieren von systemseitig verwalteten Identitäten

Da Identitäten separat von den Endpunkten verwaltet werden, die sie verwenden, ist es wichtig, dass die möglichen Auswirkungen von Änderungen an der Identität oder ihrer Rollen auf die Endpunkte Ihrer Azure Digital Twins-Instanz berücksichtigt werden. Wenn die Identität deaktiviert ist oder eine notwendige Rolle für einen Endpunkt entfernt wird, kann der Endpunkt unzugänglich und der Ablauf der Ereignisse unterbrochen werden.

Sie müssen den Endpunkt löschen und mit einem anderen Authentifizierungstyp [neu erstellen](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins), um einen Endpunkt weiterhin zu verwenden, der mit einer verwalteten Identität eingerichtet wurde, die deaktiviert wurde. Es kann bis zu eine Stunde dauern, bis Ereignisse nach dieser Änderung wieder an den Endpunkt übermittelt werden.

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie mehr über verwaltete Identitäten in Azure AD: 
* [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md)