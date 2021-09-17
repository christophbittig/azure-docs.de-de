---
title: Aktivieren der verwalteten Identität im Azure Event Grid-Systemthema
description: In diesem Artikel wird beschrieben, wie Sie die verwaltete Dienstidentität für ein Azure Event Grid-Thema aktivieren.
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: d5d8c15c818f1a9735b6cf32fe48276fb311e8ea
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633568"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Weisen Sie eine systemverwalteten Identität einem Event-Grid-Systemthema hinzu
In diesem Artikel erfahren Sie, wie Sie einem vorhandenem Event Grid-Systemthema eine system- oder benutzerseitig zugewiesene Identität zuweisen. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> Sie können entweder eine system- oder eine benutzerseitig zugewiesene Identität für ein Systemthema aktivieren, aber nicht beide. Einem Systemthema können maximal zwei benutzerseitig zugewiesene Identitäten zugewiesen werden. 

## <a name="enable-managed-identity-for-an-existing-system-topic"></a>Aktivieren einer verwalteten Identität für ein vorhandenes Systemthema
In diesem Abschnitt erfahren Sie, wie Sie eine verwaltete Identität für ein vorhandenes Systemthema aktivieren. 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Suchen Sie oben in der Suchleiste nach **Ereignis-Grid-Systemthemen**.
3. Wählen Sie das **benutzerdefinierte Thema** aus, für das Sie die verwaltete Identität aktivieren möchten. 
4. Wählen Sie im linken Menü **Identität** aus. Diese Option wird für ein System Thema, das sich am globalen Speicherort befindet, nicht angezeigt. 

### <a name="enable-system-assigned-identity"></a>Aktivieren systemseitig zugewiesener Identitäten
1. **Aktivieren** Sie den Schalter, um die Identität zu aktivieren. 
1. Wählen Sie auf der Symbolleiste **Speichern** aus, um die Einstellung zu speichern. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Seite „Identität“ für ein Systemthema"::: 
1. Klicken Sie in der Bestätigungsmeldung auf **Ja**. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Zuweisen der Identität zu einem Systemthema: Bestätigung"::: 
1. Bestätigen Sie, dass Sie die Objekt-ID der vom System zugewiesenen verwalteten Identität sehen und einen Link zum Zuweisen von Rollen sehen. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Zuweisen der Identität zu einem Systemthema: abgeschlossen"::: 

### <a name="enable-user-assigned-identity"></a>Aktivieren einer vom Benutzer zugewiesenen Identität

1. Erstellen Sie zunächst anhand der Anweisungen im Artikel [Verwalten von benutzerseitig zugewiesenen verwalteten Identitäten](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) eine benutzerseitig zugewiesene Identität. 
1. Wechseln Sie auf der Seite **Identität** im rechten Bereich zur Registerkarte **Benutzerseitig zugewiesen**, und wählen Sie dann auf der Symbolleiste **+ Hinzufügen** aus.

    :::image type="content" source="./media/managed-service-identity/system-topic-user-identity-add-button.png" alt-text="Abbildung der Schaltfläche „Hinzufügen“ auf der Registerkarte „Benutzerseitig zugewiesen“ auf der Seite „Identität“":::
1. Führen Sie im Fenster **Benutzerseitig zugewiesene verwaltete Identität hinzufügen** die folgenden Schritte aus:
    1. Wählen Sie das **Azure-Abonnement** mit der benutzerseitig zugewiesenen Identität aus. 
    1. Wählen Sie die **benutzerseitig zugewiesene Identität** aus. 
    1. Wählen Sie **Hinzufügen**. 
1. Aktualisieren Sie die Liste auf der Registerkarte **Benutzerseitig zugewiesen**, um die hinzugefügte benutzerseitig zugewiesene Identität anzuzeigen.

## <a name="enable-managed-identity-when-creating-a-system-topic"></a>Aktivieren einer verwalteten Identität bei der Erstellung eines Systemthemas

1. Suchen Sie im Azure-Portal auf der Suchleiste nach **Event Grid-Systemthemen**, und wählen Sie die Option aus. 
1. Wählen Sie auf der Symbolleiste der Seite **Event Grid-Systemthemen** die Option **Erstellen** aus. 
1. Führen Sie auf der Seite **Grundlagen** des Erstellungs-Assistenten die folgenden Schritte aus: 
    1. Wählen Sie unter **Thementypen** den Typ des Themas aus, der ein Systemthema unterstützt. Im folgenden Beispiel ist **Speicherkonten** ausgewählt. 
    2. Wählen Sie unter **Abonnement** das Azure-Abonnement mit der Azure-Ressource aus. 
    1. Wählen Sie die **Ressourcengruppe** aus, die die Azure-Ressource enthält. 
    1. Wählen Sie dann die **Ressource** aus. 
    1. Geben Sie einen **Namen** für das Systemthema ein.
    1. Aktivieren Sie die verwaltete Identität:
        1. Um die systemseitig zugewiesene Identität zu aktivieren, wählen Sie **Systemseitig zugewiesene Identität aktivieren** aus. 
        
            :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-managed-identity.png" alt-text="Screenshot des Assistenten zum Erstellen von Systemthemen mit ausgewählter Option „Systemseitig zugewiesene Identität aktivieren“":::            
        1. So aktivieren Sie eine benutzerseitig zugewiesene Identität 
            1. Wählen Sie **Benutzerseitig zugewiesene Identität** und dann **Benutzerseitig zugewiesene Identität hinzufügen** aus. 
        
                :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-user-identity.png" alt-text="Screenshot des Assistenten zum Erstellen von Systemthemen mit ausgewählter Option für eine benutzerseitig zugewiesene Identität":::            
            1. Führen Sie im Fenster **Benutzerseitig zugewiesene verwaltete Identität hinzufügen** die folgenden Schritte aus:
                1. Wählen Sie das **Azure-Abonnement** mit der benutzerseitig zugewiesenen Identität aus. 
                1. Wählen Sie die **benutzerseitig zugewiesene Identität** aus. 
                1. Wählen Sie **Hinzufügen**.                         

> [!NOTE]
> Derzeit ist es nicht möglich, eine verwaltete Identität für ein neues Systemthema zu erstellen, wenn Sie ein Ereignisabonnement für eine Azure-Ressource erstellen, die Systemthemen unterstützt. 


## <a name="global-azure-sources"></a>Globale Azure-Quellen
Sie können die vom System verwaltete Identität nur für die regionalen Azure-Ressourcen aktivieren. Sie können Sie nicht für Systemthemen aktivieren, die mit globalen Azure-Ressourcen wie Azure-Abonnements, Ressourcengruppen oder Azure Maps verknüpft sind. Die Systemthemen für diese globalen Quellen sind auch keiner bestimmten Region zugeordnet. Sie sehen die Seite **Identität** nicht für das Systemthema, dessen Standort auf **Global** eingestellt ist. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="System Thema mit auf Global eingestelltem Standort"::: 



## <a name="next-steps"></a>Nächste Schritte
Fügen Sie die Identität einer geeigneten Rolle (z. B. Azure Service Bus-Datensender) für das Ziel (z. B. Service Bus-Warteschlange) zu. Ausführliche Schritte finden Sie unter [Gewähren des Zugriffs auf ein Event Grid-Ziel für die verwaltete Identität](add-identity-roles.md). 