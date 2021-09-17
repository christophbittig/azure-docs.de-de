---
title: Aktivieren einer verwalteten Identität für benutzerdefinierte Azure Event Grid-Themen und -Domänen
description: In diesem Artikel wird beschrieben, wie Sie die verwaltete Dienstidentität für ein benutzerdefiniertes Azure Event Grid-Thema oder eine benutzerdefinierte Azure Event Grid-Domäne aktivieren.
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: 9a63e6ae65c1348e22418506bb3bf475aafc0c10
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633526"
---
# <a name="assign-a-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Zuweisen einer verwalteten Identität zu einem benutzerdefinierten Event Grid-Thema oder einer benutzerdefinierten Event Grid-Domäne 
In diesem Artikel wird beschrieben, wie Sie einem benutzerdefinierten Event Grid-Thema oder einer benutzerdefinierten Event Grid-Domäne eine system- oder benutzerseitig zugewiesene Identität zuweisen. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Sie können entweder eine systemseitig oder eine benutzerseitig zugewiesene Identität für ein Event Grid-Thema oder eine Event Grid-Domäne aktivieren, aber nicht beide. Einem Thema oder einer Domäne können maximal zwei benutzerseitig zugewiesene Identitäten zugewiesen werden. 

## <a name="enable-identity-when-creating-a-topic-or-domain"></a>Aktivieren der Identität beim Erstellen eines Themas oder einer Domäne

# <a name="azure-portal"></a>[Azure portal](#tab/portal)
Beim Erstellen im Azure-Portal können Sie einem benutzerdefinierten Thema oder einer benutzerdefinierten Domäne eine system- oder benutzerseitig zugewiesene Identität zuweisen. 

### <a name="enable-system-assigned-identity"></a>Aktivieren systemseitig zugewiesener Identitäten
Wählen Sie im Assistenten zum Erstellen von Themen oder Domänen auf der Seite **Erweitert** die Option **Systemseitig zugewiesene Identität aktivieren** aus. 

:::image type="content" source="./media/managed-service-identity/create-topic-identity.png" alt-text="Abbildung der ausgewählten Option „Systemseitig zugewiesene Identität aktivieren“":::

### <a name="enable-user-assigned-identity"></a>Aktivieren einer vom Benutzer zugewiesenen Identität
1. Wählen Sie im Assistenten zum Erstellen von Themen oder Domänen auf der Seite **Erweitert** die Option **Benutzerseitig zugewiesene Identität aktivieren** und dann **Benutzerseitig zugewiesene Identität hinzufügen** aus. 

    :::image type="content" source="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png" alt-text="Abbildung der ausgewählten Option „Benutzerseitig zugewiesene Identität aktivieren“":::
1. Wählen Sie im Fenster **Benutzerseitig zugewiesene Identität auswählen** das Abonnement mit der benutzerseitig zugewiesenen Identität und anschließend die **benutzerseitig zugewiesene Identität** aus, und klicken Sie dann auf **Auswählen**. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)
Sie können ein benutzerdefiniertes Thema oder eine benutzerdefinierte Domäne mit einer systemseitig zugewiesenen Identität auch mithilfe der Azure-Befehlszeilenschnittstelle erstellen. Verwenden Sie den Befehl `az eventgrid topic create`, wobei der Parameter `--identity` auf `systemassigned` festgelegt ist. Wenn Sie für diesen Parameter keinen Wert angeben, wird der Standardwert `noidentity` verwendet. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Entsprechend können Sie den Befehl `az eventgrid domain create` verwenden, um eine Domäne mit einer systemseitig zugewiesenen Identität zu erstellen.

> [!NOTE]
> Die Azure-Befehlszeilenschnittstelle unterstützt das Zuweisen von benutzerseitig zugewiesenen verwalteten Identität zu einem Event Grid-Thema oder einer Event Grid-Domäne noch nicht. 

---

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Aktivieren der Identität für ein vorhandenes benutzerdefiniertes Thema oder eine vorhandene benutzerdefinierte Domäne
In diesem Abschnitt erfahren Sie, wie Sie eine system- oder benutzerseitig zugewiesene Identität für ein vorhandenes benutzerdefiniertes Thema oder eine vorhandene benutzerdefinierte Domäne aktivieren. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)
Das folgende Verfahren zeigt, wie Sie eine systemseitig zugewiesene Identität für ein benutzerdefiniertes Thema aktivieren. Die Schritte zum Aktivieren einer Identität für eine Domäne sind ähnlich. 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Suchen Sie oben in der Suchleiste nach **Event Grid-Themen**.
3. Wählen Sie das **benutzerdefinierte Thema** aus, für das Sie die verwaltete Identität aktivieren möchten. 
4. Wählen Sie im linken Menü **Identität** aus.

### <a name="to-assign-a-system-assigned-identity-to-a-topic"></a>So weisen Sie einem Thema eine systemseitig zugewiesene Identität zu
1. **Aktivieren** Sie auf der Registerkarte **Systemseitig zugewiesen** den Schalter, um die Identität zu aktivieren. 
1. Wählen Sie auf der Symbolleiste **Speichern** aus, um die Einstellung zu speichern. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Seite „Identität“ für ein benutzerdefiniertes Thema"::: 

### <a name="to-assign-a-user-assigned-identity-to-a-topic"></a>So weisen Sie einem Thema eine benutzerseitig zugewiesene Identität zu
1. Erstellen Sie anhand der Anweisungen im Artikel [Verwalten von benutzerseitig zugewiesenen verwalteten Identitäten](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) eine benutzerseitig zugewiesene Identität. 
1. Wechseln Sie auf der Seite **Identität** im rechten Bereich zur Registerkarte **Benutzerseitig zugewiesen**, und wählen Sie dann auf der Symbolleiste **+ Hinzufügen** aus.

    :::image type="content" source="./media/managed-service-identity/user-assigned-identity-add-button.png" alt-text="Abbildung der Registerkarte „Benutzerseitig zugewiesene Identität“":::     
1. Führen Sie im Fenster **Benutzerseitig zugewiesene verwaltete Identität hinzufügen** die folgenden Schritte aus:
    1. Wählen Sie das **Azure-Abonnement** mit der benutzerseitig zugewiesenen Identität aus. 
    1. Wählen Sie die **benutzerseitig zugewiesene Identität** aus. 
    1. Wählen Sie **Hinzufügen**. 
1. Aktualisieren Sie die Liste auf der Registerkarte **Benutzerseitig zugewiesen**, um die hinzugefügte benutzerseitig zugewiesene Identität anzuzeigen.

Sie können ähnliche Schritte zum Aktivieren der Identität für eine Event Grid-Domäne verwenden.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)
Verwenden Sie den Befehl `az eventgrid topic update`, wobei Sie `--identity` auf `systemassigned` festlegen, um die vom System zugewiesene Identität für ein vorhandenes benutzerdefiniertes Thema zu aktivieren. Wenn Sie die Identität deaktivieren möchten, geben Sie als Wert `noidentity` an. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Der Befehl zum Aktualisieren einer vorhandenen Domäne lautet entsprechend (`az eventgrid domain update`).

> [!NOTE]
> Die Azure-Befehlszeilenschnittstelle unterstützt das Zuweisen von benutzerseitig zugewiesenen verwalteten Identität zu einem Event Grid-Thema oder einer Event Grid-Domäne noch nicht. 

---

## <a name="next-steps"></a>Nächste Schritte
Fügen Sie die Identität einer geeigneten Rolle (z. B. Azure Service Bus-Datensender) für das Ziel (z. B. Service Bus-Warteschlange) zu. Ausführliche Schritte finden Sie unter [Gewähren des Zugriffs auf ein Event Grid-Ziel für die verwaltete Identität](add-identity-roles.md). 
