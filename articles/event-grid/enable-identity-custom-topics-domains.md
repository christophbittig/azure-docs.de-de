---
title: Aktivieren einer verwalteten Identität für benutzerdefinierte Azure Event Grid-Themen und -Domänen
description: In diesem Artikel wird beschrieben, wie Sie die verwaltete Dienstidentität für ein benutzerdefiniertes Azure Event Grid-Thema oder eine benutzerdefinierte Azure Event Grid-Domäne aktivieren.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 1f9cae4c25c37d0fcb670804ae3450871dbd5259
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156585"
---
# <a name="assign-a-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Zuweisen einer verwalteten Identität zu einem benutzerdefinierten Event Grid-Thema oder einer benutzerdefinierten Event Grid-Domäne 
Dieser Artikel zeigt Ihnen, wie Sie das Azure-Portal und die Befehlszeilenschnittstelle (CLI) verwenden, um einem benutzerdefinierten Thema oder einer Domäne eine vom System zugewiesene oder eine vom Benutzer zugewiesene [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) zuzuweisen. 

## <a name="enable-identity-when-creating-a-topic-or-domain"></a>Aktivieren der Identität beim Erstellen eines Themas oder einer Domäne

# <a name="azure-portal"></a>[Azure portal](#tab/portal)
Im **Azure-Portal** können Sie beim Erstellen eines Themas oder einer Domäne entweder eine vom System zugewiesene Identität oder zwei vom Benutzer zugewiesene Identitäten zuweisen, aber nicht beide Arten von Identitäten. Sobald das Thema oder die Domäne erstellt ist, können Sie beide Arten von Identitäten zuweisen, indem Sie die Schritte im Abschnitt [Identität für ein bestehendes Thema oder eine Domäne aktivieren](#enable-identity-for-an-existing-custom-topic-or-domain) ausführen.

### <a name="enable-system-assigned-identity"></a>Aktivieren systemseitig zugewiesener Identitäten
Wählen Sie im Assistenten zum Erstellen von Themen oder Domänen auf der Seite **Erweitert** die Option **Systemseitig zugewiesene Identität aktivieren** aus. 

:::image type="content" source="./media/managed-service-identity/create-topic-identity.png" alt-text="Abbildung der ausgewählten Option „Systemseitig zugewiesene Identität aktivieren“":::

### <a name="enable-user-assigned-identity"></a>Aktivieren einer vom Benutzer zugewiesenen Identität
1. Wählen Sie im Assistenten zum Erstellen von Themen oder Domänen auf der Seite **Erweitert** die Option **Benutzerseitig zugewiesene Identität aktivieren** und dann **Benutzerseitig zugewiesene Identität hinzufügen** aus. 

    :::image type="content" source="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png" alt-text="Abbildung der ausgewählten Option „Benutzerseitig zugewiesene Identität aktivieren“":::
1. Wählen Sie im Fenster **Benutzerseitig zugewiesene Identität auswählen** das Abonnement mit der benutzerseitig zugewiesenen Identität und anschließend die **benutzerseitig zugewiesene Identität** aus, und klicken Sie dann auf **Auswählen**. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)
Sie können auch Azure CLI verwenden, um ein benutzerdefiniertes Thema oder eine Domäne mit einer vom System zugewiesenen Identität zu erstellen. Derzeit unterstützt Azure CLI nicht die Zuweisung einer vom Benutzer zugewiesenen Identität zu einem Thema oder einer Domäne.  

Verwenden Sie den Befehl `az eventgrid topic create`, wobei der Parameter `--identity` auf `systemassigned` festgelegt ist. Wenn Sie für diesen Parameter keinen Wert angeben, wird der Standardwert `noidentity` verwendet. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Entsprechend können Sie den Befehl `az eventgrid domain create` verwenden, um eine Domäne mit einer systemseitig zugewiesenen Identität zu erstellen.

---

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Aktivieren der Identität für ein vorhandenes benutzerdefiniertes Thema oder eine vorhandene benutzerdefinierte Domäne
In diesem Abschnitt erfahren Sie, wie Sie eine system- oder benutzerseitig zugewiesene Identität für ein vorhandenes benutzerdefiniertes Thema oder eine vorhandene benutzerdefinierte Domäne aktivieren. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)
Wenn Sie das Azure-Portal verwenden, können Sie einem bestehenden Thema oder einer Domäne eine systemzugeordnete Identität und bis zu zwei benutzerzugeordnete Identitäten zuweisen.

Die folgenden Verfahren zeigen Ihnen, wie Sie eine Identität für ein benutzerdefiniertes Thema aktivieren können. Die Schritte zum Aktivieren einer Identität für eine Domäne sind ähnlich. 

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
Sie können auch Azure CLI verwenden, um eine vom System zugewiesene Identität einem vorhandenen benutzerdefinierten Thema oder einer Domäne zuzuweisen. Derzeit unterstützt Azure CLI nicht die Zuweisung einer vom Benutzer zugewiesenen Identität zu einem Thema oder einer Domäne.

Verwenden Sie den Befehl `az eventgrid topic update`, wobei Sie `--identity` auf `systemassigned` festlegen, um die vom System zugewiesene Identität für ein vorhandenes benutzerdefiniertes Thema zu aktivieren. Wenn Sie die Identität deaktivieren möchten, geben Sie als Wert `noidentity` an. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Der Befehl zum Aktualisieren einer vorhandenen Domäne lautet entsprechend (`az eventgrid domain update`).

---

## <a name="next-steps"></a>Nächste Schritte
Fügen Sie die Identität einer geeigneten Rolle (z. B. Azure Service Bus-Datensender) für das Ziel (z. B. Service Bus-Warteschlange) zu. Ausführliche Schritte finden Sie unter [Gewähren des Zugriffs auf ein Event Grid-Ziel für die verwaltete Identität](add-identity-roles.md). 
