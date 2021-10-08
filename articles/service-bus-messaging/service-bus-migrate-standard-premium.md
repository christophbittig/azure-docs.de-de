---
title: Migrieren von Azure Service Bus-Namespaces – von Standard zu Premium
description: Anleitung zur Migration von vorhandenen Azure Service Bus-Standardnamespaces zu Premium
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: eea34edddf641e3ee1c07bea92b20364e7aeaf34
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602067"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrieren von vorhandenen Azure Service Bus-Standardnamespaces zum Premium-Tarif

Bisher bot der Azure Service Bus Namespaces nur im Standard-Tarif an. Namespaces sind mehrinstanzenfähige Setups, die für niedrige Durchsätze und Entwicklerumgebungen optimiert wurden. Der Premium-Tarif bietet dedizierte Ressourcen pro Namespace für vorhersagbare Latenz und höheren Durchsatz zu einem festen Preis. Der Premium-Tarif ist für hohen Durchsatz und Produktionsumgebungen optimiert, die zusätzliche Enterprisefeatures erfordern.

In diesem Artikel wird beschrieben, wie Sie vorhandene Standard-Tarif-Namespaces zum Premium-Tarif migrieren.  

>[!WARNING]
> Mit der Migration wird ein Upgrade von Service Bus-Standardnamespaces zum Premium-Tarif durchgeführt. Ein Herabstufen wird vom Migrationstool nicht unterstützt.

Beachten Sie folgende Punkte:

- Diese Migration erfolgt direkt, was bedeutet, dass vorhandene Sender- und Empfängeranwendungen **keine Code- oder Konfigurationsänderungen erfordern**. Die bestehende Verbindungszeichenfolge verweist automatisch auf den neuen Premiumnamespace.
- Der **Premiumnamespace** sollte **keine Entitäten** enthalten, damit die Migration erfolgreich ausgeführt werden kann.
- Alle **Entitäten** im Standardnamespace werden während des Migrationsprozesses in den Premiumnamespace **kopiert**.
- Die Migration unterstützt **1.000 Entitäten pro Messagingeinheit** im Premium-Tarif. Um zu ermitteln, wie viele Messagingeinheiten Sie benötigen, beginnen Sie mit der Anzahl der Einheiten, die Sie in Ihrem aktuellen Standardnamespace haben.
- Sie können nicht direkt vom **Basic-Tarif** zum **Premium-Tarif** migrieren, jedoch indirekt, indem Sie zuerst vom Basic- zum Standard-Tarif und dann im nächsten Schritt vom Standard- zum Premium-Tarif migrieren.

## <a name="migration-steps"></a>Schritte bei der Migration

Einige Bedingungen sind mit dem Migrationsprozess verbunden. Machen Sie sich mit den folgenden Schritten vertraut, um die Fehlerwahrscheinlichkeit zu reduzieren. Der Migrationsprozess ist in diese Schritte gegliedert, und die Informationen zu den einzelnen Schritten finden Sie in den folgenden Abschnitten.

1. Erstellen Sie einen neuen Premiumnamespace.
1. Koppeln Sie Standard- und Premiumnamespace miteinander.
1. Synchronisieren (kopieren) Sie Entitäten aus dem Standardnamespace mit dem Premiumnamespace.
1. Committen Sie die Migration.
1. Entladen Sie die Entitäten im Standardnamespace unter Verwendung des Namens, den der Namespace nach der Migration hat.
1. Löschen Sie den Standardnamespace.

>[!IMPORTANT]
> Greifen Sie nach dem Committen der Migration auf den alten Standardnamespace zu, und entladen Sie die Warteschlangen und Abonnements. Nach dem Entladen der Nachrichten können diese an den neuen Premiumnamespace gesendet und von den Empfängeranwendungen verarbeitet werden. Nach dem Entladen der Warteschlangen und Abonnements sollten Sie den alten Standardnamespace löschen.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrieren mit Azure CLI oder PowerShell

Führen Sie die Migration Ihres Service Bus-Standardnamespace zu Premium mit der Azure CLI oder dem PowerShell-Tool mit den folgenden Schritten aus.

1. Erstellen Sie einen neuen Service Bus-Premiumnamespace. Sie können auf die [Azure Resource Manager-Vorlagen](service-bus-resource-manager-namespace.md) verweisen oder das [Azure-Portal](service-bus-create-namespace-portal.md) verwenden. Wählen Sie für den **serviceBusSku**-Parameter **premium** aus.

1. Legen Sie die folgenden Umgebungsvariablen fest, um die Migrationsbefehle zu vereinfachen.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Der Alias/Name nach der Migration (post_migration_dns_name) wird verwendet, um nach der Migration auf den alten Standardnamespace zuzugreifen. Entladen Sie hiermit die Warteschlangen und Abonnements, und löschen Sie dann den Namespace.

1. Koppeln Sie die Standard- und Premiumnamespaces, und starten Sie die Synchronisierung mit dem folgenden Befehl:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Überprüfen Sie den Migrationsstatus mithilfe des folgenden Befehls:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Die Migration wird als abgeschlossen betrachtet, wenn Sie die folgenden Werte sehen:

    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    Dieser Befehl zeigt außerdem die Migrationskonfiguration an. Stellen Sie sicher, dass die Werte richtig festgelegt sind. Überprüfen Sie außerdem den Premiumnamespace im Portal, um sicherzustellen, dass alle Warteschlangen und Themen angelegt wurden und mit dem übereinstimmen, was im Standardnamespace vorhanden war.

1. Committen Sie die Migration, indem Sie den folgenden Abschlussbefehl ausführen:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrieren über das Azure-Portal

Die Migration über das Azure-Portal hat den gleichen logischen Ablauf wie die Migration über die Befehle. Führen Sie die folgenden Schritte aus, um im Azure-Portal zu migrieren.

1. Wählen Sie im Menü **Navigation** im linken Bereich **Zu Premium migrieren** aus. Klicken Sie auf die Schaltfläche **Erste Schritte**, um auf der nächsten Seite fortzufahren.
    :::image type="content" source="./media/service-bus-standard-premium-migration/migrate-premium-page.png" alt-text="Das Bild zeigt die Seite Migrate to premium.":::
1. Sie sehen die folgende Seite **Namensräume einrichten**.

    :::image type="content" source="./media/service-bus-standard-premium-migration/setup-namespaces-page.png" alt-text="Bild zeigt die Seite Namespaces einrichten.":::
1. Führen Sie auf den Seiten **Namensräume einrichten** einen der folgenden Schritte aus: 
    1. Wenn Sie **Neuen Premium-Namensraum erstellen** wählen:
        1. Geben Sie auf der Seite **Namensraum erstellen** einen Namen für den Namensraum ein, und wählen Sie **Überprüfen + Erstellen**.
        1. Wählen Sie auf der Seite **Überprüfen + erstellen** die Option **Erstellen** aus.

            :::image type="content" source="./media/service-bus-standard-premium-migration/create-premium-namespace.png" alt-text="Die Abbildung zeigt die Seite &quot;Namensraum erstellen&quot; ":::.
    1. Wenn Sie **Wählen Sie einen bestehenden leeren Premium-Namensraum**:
        1. Wählen Sie das Azure-Abonnement und die Ressourcengruppe, die den Namespace enthält.
        1. Wählen Sie dann den Premium-Namespace.
        1. Klicken Sie dann auf **Auswählen**.
        
            :::image type="content" source="./media/service-bus-standard-premium-migration/select-existing-namespace.png" alt-text="Das Bild zeigt die Auswahl eines bestehenden Premium-Namensraums.":::
1. Geben Sie einen **Post-Migrationsnamen** ein, und wählen Sie dann **Weiter**. Mit diesem Namen greifen Sie nach Abschluss der Migration auf den Standardnamespace zu.

    :::image type="content" source="./media/service-bus-standard-premium-migration/enter-post-migration-name.png" alt-text="Das Bild zeigt den Post-Migrationsnamen für den Standard-Namensraum.":::
1. Wählen Sie **Synchronisation starten**, um Entitäten zwischen dem Standard- und dem Premium-Namensraum zu synchronisieren.

    :::image type="content" source="./media/service-bus-standard-premium-migration/start-sync-button.png" alt-text="Die Abbildung zeigt die Schaltfläche &quot;Sync starten&quot;.":::
1. Wählen Sie **Ja** im Dialogfeld, um die Synchronisierung zu bestätigen und zu starten. Warten Sie, bis die Synchronisierung abgeschlossen ist. Klicken Sie anschließend auf **Weiter**.

    >[!IMPORTANT]
    > Wenn Sie die Migration aus irgendeinem Grund abbrechen müssen, lesen Sie bitte die entsprechende Anleitung im Abschnitt „FAQ“ dieses Dokuments.    
1. Wählen Sie **Migration abschließen** auf der Seite **Wechsel**. 

    :::image type="content" source="./media/service-bus-standard-premium-migration/complete-migration.png" alt-text="Die Abbildung zeigt die Seite **Umschalten** des Migrationsassistenten.":::
1. Wählen Sie **Ja**, um die Umstellung Ihres Standard-Namensraums auf Premium zu bestätigen. Sobald die Umstellung abgeschlossen ist, wird der DNS-Name Ihres Standard-Namensraums auf Ihren Premium-Namensraum verweisen. Dieser Vorgang kann nicht rückgängig gemacht werden. Wenn die Migration abgeschlossen ist, sehen Sie die Seite **Erfolg**.

    :::image type="content" source="./media/service-bus-standard-premium-migration/success-page.png" alt-text="Bild der Erfolgsseite.":::

## <a name="caveats"></a>Vorbehalte

Einige der im Standard-Tarif von Azure Service Bus zur Verfügung stehenden Features werden im Premium-Tarif nicht unterstützt. Dies ist beabsichtigt, da der Premium-Tarif dedizierte Ressourcen für planbaren Durchsatz und planbare Wartezeit bietet.

Im Anschluss folgt eine Liste mit Features, die im Premium-Tarif nicht unterstützt werden, und entsprechenden Abhilfemaßnahmen:

### <a name="express-entities"></a>Expressentitäten

   Expressentitäten, die keine Nachrichtendaten im Speicher committen, werden im Premium-Tarif nicht unterstützt. Dedizierte Ressourcen sorgen für eine erhebliche Durchsatzverbesserung und stellen gleichzeitig eine dauerhafte Speicherung der Daten sicher, wie dies von einem Messagingsystem für Unternehmen erwartet wird.

   Bei der Migration werden alle Expressentitäten aus Ihrem Standard-Namespace als Nicht-Expressentitäten im Premium-Namespace erstellt.

   Bei Verwendung von ARM-Vorlagen (Azure Resource Manager) muss das Flag „enableExpress“ aus der Bereitstellungskonfiguration entfernt werden, damit Ihre automatisierten Workflows fehlerfrei ausgeführt werden.

### <a name="partitioned-entities"></a>Partitionierte Entitäten

   Partitionierte Entitäten wurden im Standard-Tarif unterstützt, um die Verfügbarkeit in einer Umgebung mit mehreren Mandanten zu verbessern. Dank der Bereitstellung dedizierter, pro Namespace verfügbarer Ressourcen ist dies im Premium-Tarif nicht mehr nötig.

   Bei der Migration werden alle partitionierten Entitäten aus Ihrem Standard-Namespace als nicht partitionierte Entitäten im Premium-Namespace erstellt.

   Falls „enablePartitioning“ in Ihrer ARM-Vorlage für eine bestimmte Warteschlange oder ein bestimmtes Thema auf „true“ festgelegt ist, wird sie bzw. es vom Broker ignoriert.

## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="what-happens-when-the-migration-is-committed"></a>Was geschieht, wenn die Migration committet ist?

Nach dem Committen der Migration zeigt die Verbindungszeichenfolge, die auf den Standardnamespace verweist, auf den Premiumnamespace.

Die Sender- und Empfängeranwendungen trennen die Verbindung zum Standardnamespace und stellen automatisch die Verbindung mit dem Premiumnamespace her.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Was mache ich, nachdem die Standard-zu-Premium-Migration abgeschlossen ist?

Die Standard-zu-Premium-Migration stellt sicher, dass die Entitätsmetadaten wie Themen, Abonnements und Filter aus dem Standard- in den Premiumnamespace kopiert werden. Die Nachrichtendaten, die in den Standardnamespace übertragen wurden, werden nicht aus dem Standardnamespace in den Premiumnamespace kopiert.

Der Standardnamespace kann einige Nachrichten enthalten, die während der laufenden Migration gesendet und committet wurden. Entladen Sie diese Nachrichten manuell aus dem Standardnamespace, und senden Sie sie manuell an den Premiumnamespace. Um die Nachrichten manuell zu entladen, verwenden Sie eine Konsolen-App oder ein Skript, das die Standardnamespace-Entitäten mithilfe des DNS-Namens nach der Migration entlädt, den Sie in den Migrationsbefehlen angegeben haben. Senden Sie diese Nachrichten an den Premiumnamespace, damit sie von den Empfängern verarbeitet werden können.

Nachdem die Nachrichten entladen wurden, löschen Sie den Standardnamespace.

>[!IMPORTANT]
> Nach der Entladung der Nachrichten aus dem Standardnamespace löschen Sie den Standardnamespace. Dies ist wichtig, da die Verbindungszeichenfolge, die zunächst auf den Standardnamespace verwies, nun auf den Premiumnamespace verweist. Sie benötigen den Standardnamespace nicht mehr. Wenn Sie den migrierten Standardnamespace löschen, können später weniger Unklarheiten auftreten.

### <a name="how-much-downtime-do-i-expect"></a>Wie viel Ausfallzeit kann ich erwarten?

Der Migrationsprozess soll die zu erwartende Downtime der Anwendungen reduzieren. Die Downtime wird durch die Verwendung der Verbindungszeichenfolge reduziert, mit der Sender- und Empfängeranwendungen auf den neuen Premiumnamespace verweisen.

Die Downtime der Anwendung ist auf die Zeit beschränkt, die benötigt wird, um den DNS-Eintrag zu aktualisieren und auf den Premiumnamespace zu verweisen. Die Downtime beträgt ca. fünf Minuten.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Muss ich während der Migration Konfigurationsänderungen vornehmen?

Nein, es sind keine Code- oder Konfigurationsänderungen erforderlich, um die Migration durchzuführen. Die Verbindungszeichenfolge, die Sender- und Empfängeranwendungen für den Zugriff auf den Standardnamespace verwenden, wird automatisch als Alias für den Premiumnamespace abgebildet.

### <a name="what-happens-when-i-abort-the-migration"></a>Was geschieht, wenn ich die Migration abbreche?

Die Migration kann entweder mit dem Befehl `Abort` oder über das Azure-Portal abgebrochen werden.

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure-Portal

Wählen Sie **Abbrechen** auf der Seite **Entitäten synchronisieren**. 

:::image type="content" source="./media/service-bus-standard-premium-migration/abort1.png" alt-text="Die Abbildung zeigt die Seite Abbrechen":::.

Wenn der Vorgang abgeschlossen ist, sehen Sie die folgende Seite: 

:::image type="content" source="./media/service-bus-standard-premium-migration/abort3.png" alt-text="Das Bild zeigt die Seite &quot;Abbrechen abgeschlossen&quot;.":::

Wenn der Migrationsprozess abgebrochen wird, wird das Kopieren der Entitäten (Themen, Abonnements und Filter) vom Standard- in den Premiumnamespace ab- und die Kopplung unterbrochen.

Die Verbindungszeichenfolge wird nicht so aktualisiert, dass sie auf den Premiumnamespace verweist. Ihre bestehenden Anwendungen funktionieren weiterhin so, wie vor Beginn der Migration.

Allerdings werden keine Entitäten im Premiumnamespace oder der Premiumnamespace gelöscht. Löschen Sie die Entitäten manuell, wenn Sie nicht mit der Migration fortfahren.

>[!IMPORTANT]
> Wenn Sie sich entscheiden, die Migration abzubrechen, löschen Sie den Premiumnamespace, den Sie für die Migration bereitgestellt haben, damit Ihnen die Ressourcen nicht in Rechnung gestellt werden.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Ich möchte die Nachrichten nicht entleeren. Wie gehe ich vor?

Es kann Nachrichten geben, die von den Senderanwendungen gesendet und auf den Speicher im Standardnamespace committet werden, während die Migration stattfindet, und zwar unmittelbar vor der Migration.

Während der Migration werden die tatsächlichen Nachrichtendaten / die tatsächliche Nutzlast aus dem Standard- in den Premiumnamespace kopiert. Die Nachrichten müssen manuell entladen und dann an den Premiumnamespace gesendet werden.

Wenn Sie jedoch während eines geplanten Wartungs-/Housekeepingfensters migrieren können und die Nachrichten nicht manuell entladen und versenden möchten, führen Sie die folgenden Schritte aus:

1. Stoppen Sie die Senderanwendungen. Die Empfängeranwendungen verarbeiten die Nachrichten, die sich derzeit im Standardnamespace befinden, und entladen die Warteschlange.
1. Sobald die Warteschlangen und Abonnements im Standardnamespace leer sind, führen Sie die oben beschriebene Vorgehensweise durch, um die Migration vom Standard- zum Premiumnamespace durchzuführen.
1. Sobald die Migration abgeschlossen ist, können Sie die Senderanwendungen neu starten.
1. Die Absender und Empfänger werden jetzt automatisch mit dem Premiumnamespace verbunden.

    >[!NOTE]
    > Sie müssen die Empfängeranwendungen für die Migration nicht stoppen.
    >
    > Nach Abschluss der Migration trennen die Empfängeranwendungen die Verbindung zum Standardnamespace und verbinden sich automatisch mit dem Premiumnamespace.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Unterschiede zwischen Standard- und Premiummessaging](./service-bus-premium-messaging.md).
* Erfahren Sie mehr über [Aspekte der Hochverfügbarkeit und Geo-Notfallwiederhierstellung für Service Bus Premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

