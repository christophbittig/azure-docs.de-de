---
title: 'Tutorial: Verwalten von Abfragen im Azure-Portal'
description: In diesem Tutorial erstellen Sie im Azure-Portal eine Resource Graph-Abfrage und geben die neue Abfrage für andere Benutzer frei.
ms.date: 08/17/2021
ms.topic: tutorial
ms.openlocfilehash: 669864333dd20d87c13a9ad049250ef5ca20bd7e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324777"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Tutorial: Erstellen und Freigeben einer Azure Resource Graph-Abfrage im Azure-Portal

Mit dem Azure Resource Graph-Explorer können Sie Ihre Resource Graph-Abfragen direkt im Azure-Portal speichern. Es gibt zwei Arten von Abfragen: _Privat_ und _Freigegeben_. Eine private Abfrage wird in den Einstellungen Ihres Azure-Portals gespeichert. Eine freigegebene Abfrage ist dagegen eine Azure Resource Manager-Ressource, die mit der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) verwaltet und mit Ressourcensperren geschützt werden kann. Beide Abfragetypen werden im Ruhezustand verschlüsselt.

Durch Speichern von Abfragen im Azure-Portal sparen Sie Zeit für die Suche nach Ihren bevorzugten oder häufig verwendeten Abfragen. Wenn Sie Abfragen freigeben, unterstützen Sie Ihr Team dabei, durch Wiederholung Ziele in Bezug auf Konsistenz und Effizienz zu erreichen.

In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> - Erstellen und Löschen einer privaten Abfrage
> - Erstellen einer freigegebenen Abfrage
> - Entdecken von freigegebenen Abfragen
> - Löschen einer freigegebenen Abfrage

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement, um dieses Tutorial durcharbeiten zu können. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-and-delete-a-private-query"></a>Erstellen und Löschen einer privaten Abfrage

Private Abfragen sind nur für das Konto sichtbar und zugänglich, das die Abfragen erstellt hat. Da sie in den Azure-Portaleinstellungen eines Kontos gespeichert werden, können sie nur über das Azure-Portal erstellt, verwendet und gelöscht werden. Eine private Abfrage ist keine Resource Manager-Ressource. Führen Sie die folgenden Schritte aus, um eine neue private Abfrage zu erstellen:

1. Wählen Sie im Portalmenü die Option **Alle Dienste** aus, oder verwenden Sie das Azure-Suchfeld, das sich auf allen Seiten am oberen Rand befindet. Suchen Sie nach **Resource Graph-Explorer**, und wählen Sie diesen aus.

1. Geben Sie im Resource Graph-Explorer auf der Registerkarte **Abfrage 1** die folgende Abfrage ein:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Klicken Sie auf **Abfrage ausführen**, um die Abfrageergebnisse im unteren Bereich anzuzeigen.

   Weitere Informationen zu dieser Abfrage finden Sie unter [Beispiele: Anzahl von virtuellen Computern nach Betriebssystemtyp](../samples/starter.md#count-os).

1. Wählen Sie **Speichern** oder **Speichern unter** aus, geben Sie **VMs nach Betriebssystem zählen** als Namen ein, behalten Sie den Typ **Private Abfrage** bei, und klicken Sie dann unten im Bereich **Abfrage speichern** auf **Speichern**. Der Titel der Registerkarte ändert sich von **Abfrage 1** zu **VMs nach Betriebssystem zählen**.

1. Verlassen Sie den Azure Resource Graph-Explorer im Azure-Portal, und kehren Sie wieder zurück. Beachten Sie, dass die gespeicherte Abfrage nicht mehr angezeigt wird, und die Registerkarte **Abfrage 1** wieder zu sehen ist.

1. Wählen Sie **Abfrage öffnen** aus. Stellen Sie sicher, dass der Typ **Private Abfrage** lautet. Der gespeicherte Name **VMs nach Betriebssystem zählen** wird jetzt in der Liste **Abfragename** angezeigt. Wenn Sie den Titellink der gespeicherten Abfrage auswählen, wird sie in einer neuen Registerkarte mit dem Namen dieser Abfrage geladen.

   > [!NOTE]
   > Wenn eine gespeicherte Abfrage offen ist und die Registerkarte ihren Namen angezeigt, wird die Abfrage beim Klicken auf die Schaltfläche **Speichern** mit allen vorgenommenen Änderungen gespeichert. Um eine neue gespeicherte Abfrage aus einer offenen Abfrage zu erstellen, wählen Sie **Speichern unter** aus, und fahren Sie so fort, als würden Sie eine ganz neue Abfrage speichern.

1. Um eine gespeicherte Abfrage zu löschen, wählen Sie erneut **Abfrage öffnen** aus, und überprüfen Sie, ob der **Typ** als **Private Abfrage** festgelegt ist. Klicken Sie in der Zeile der gespeicherten Abfrage `Count VMs by OS` auf **Löschen** (Papierkorbsymbol). Klicken Sie im Bestätigungsdialogfeld auf **Ja**, um das Löschen der Abfrage abzuschließen.
   Schließen Sie danach den Bereich **Abfrage öffnen**.

## <a name="create-a-shared-query"></a>Erstellen einer freigegebenen Abfrage

Im Gegensatz zu einer privaten Abfrage ist eine freigegebene Abfrage eine Resource Manager-Ressource. Das bedeutet, dass die Abfrage in einer Ressourcengruppe gespeichert wird, mit Azure RBAC verwaltet und gesteuert und sogar mit Ressourcensperren geschützt werden kann. Da es sich um eine Ressource handelt, kann jeder Benutzer, der über die entsprechenden Berechtigungen verfügt, die Abfrage anzeigen und verwenden. Führen Sie die folgenden Schritte aus, um eine neue freigegebene Abfrage zu erstellen:

1. Wählen Sie im Portalmenü die Option **Alle Dienste** aus, oder verwenden Sie das Azure-Suchfeld, das sich auf allen Seiten am oberen Rand befindet, um **Resource Graph-Explorer** zu suchen und auszuwählen.

1. Geben Sie im Resource Graph-Explorer auf der Registerkarte **Abfrage 1** die folgende Abfrage ein:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Klicken Sie auf **Abfrage ausführen**, um die Abfrageergebnisse im unteren Bereich anzuzeigen.

   Weitere Informationen zu dieser Abfrage finden Sie unter [Beispiele: Anzahl von virtuellen Computern nach Betriebssystemtyp](../samples/starter.md#count-os).

1. Wählen Sie **Speichern** oder **Speichern unter** aus.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="Speichern der neuen Abfrage mit der Schaltfläche „Speichern“" border="false":::

1. Geben Sie im Bereich **Abfrage speichern** als Namen **VMs nach Betriebssystem zählen** ein.

1. Ändern Sie den Typ zu **Freigegebene Abfrage**, geben Sie als Beschreibung **Anzahl von virtuellen Computern nach Betriebssystemtyp** ein, und legen Sie **Abonnement** fest, um anzugeben, wo die Abfrageressource erstellt werden soll.

1. Lassen Sie das Kontrollkästchen **In Ressourcengruppe „resource-graph-queries“ veröffentlichen** aktiviert, und behalten Sie den **Ressourcengruppenstandort** als **(USA) USA, Westen-Mitte** bei.

1. Klicken Sie unten im Bereich **Abfrage speichern** auf **Speichern**. Der Titel der Registerkarte ändert sich von **Abfrage 1** zu **VMs nach Betriebssystem zählen**. Wenn die Ressourcengruppe **resource-graph-queries** zum ersten Mal verwendet wird, dauert der Speichervorgang länger als erwartet, weil die Ressourcengruppe zuerst erstellt werden muss.

   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="Speichern der neuen Abfrage als freigegebene Abfrage" border="false":::

   > [!NOTE]
   > Sie können das Kontrollkästchen **In Ressourcengruppe „resource-graph-queries“ veröffentlichen** deaktivieren, wenn Sie den Namen einer vorhandenen Ressourcengruppe angeben möchten, in der die Abfrage gespeichert werden soll. Die Verwendung des Standardnamens für Ressourcengruppen für Abfragen vereinfacht die Entdeckung von freigegebenen Abfragen. Dadurch wird auch der Zweck dieser Ressourcengruppe offensichtlicher. Sie können jedoch aus Sicherheitsgründen eine vorhandene Ressourcengruppe basierend auf vorhandenen Berechtigungen auswählen.

1. Verlassen Sie den Azure Resource Graph-Explorer im Azure-Portal, und kehren Sie wieder zurück. Beachten Sie, dass die gespeicherte Abfrage nicht mehr angezeigt wird, und die Registerkarte **Abfrage 1** wieder zu sehen ist.

1. Wählen Sie **Abfrage öffnen** aus. Vergewissern Sie sich beim Speichern von Abfragen, dass der Typ auf **Freigegebene Abfrage** festgelegt ist und die Kombination aus **Abonnement** und **Ressourcengruppe** stimmt. Das gespeicherte Element **VMs nach Betriebssystem zählen** wird jetzt in der Liste **Abfragename** angezeigt. Wählen Sie den Titellink der gespeicherten Abfrage aus, um diese in einer neuen Registerkarte mit dem Namen dieser Abfrage zu laden. Auf der Registerkarte wird ein Symbol neben dem Titel angezeigt, das darauf hinweist, dass es sich um eine freigegebene Abfrage handelt.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="Anzeige der freigegebenen Abfrage mit Symbol" border="false":::

   > [!NOTE]
   > Wenn eine gespeicherte Abfrage offen ist und die Registerkarte ihren Namen angezeigt, wird die Abfrage beim Klicken auf die Schaltfläche **Speichern** mit allen vorgenommenen Änderungen gespeichert. Um eine neue gespeicherte Abfrage zu erstellen, wählen Sie **Speichern unter** aus, und fahren Sie so fort, als würden Sie eine ganz neue Abfrage speichern.

## <a name="discover-shared-queries"></a>Entdecken von freigegebenen Abfragen

Da eine freigegebene Abfrage eine Resource Manager-Ressource ist, gibt es verschiedene Möglichkeiten, eine solche Abfrage zu finden:

- Im Resource Graph-Explorer. Klicken Sie auf **Abfrage öffnen**, und legen Sie den Typ auf **Freigegebene Abfrage** fest.
- Auf der Portalseite mit Resource Graph-Abfragen.
- In der Ressourcengruppe, in der die freigegebene Abfrage gespeichert wurde.
- Über eine Abfrage an Resource Graph.

### <a name="view-resource-graph-queries"></a>Anzeigen von Resource Graph-Abfragen

Im Azure-Portal zeigt die Seite mit Resource Graph-Abfragen freigegebene Abfragen an, auf die das angemeldete Konto Zugriff hat. Auf dieser Seite können Sie Resource Graph-Abfragen nach Name, Abonnement, Ressourcengruppe und anderen Eigenschaften filtern. Sie können Resource Graph-Abfragen in dieser Schnittstelle auch markieren, exportieren und löschen.

Indem Sie eine der Abfragen auswählen, wird die Seite mit der Resource Graph-Abfrage geöffnet. Ebenso wie bei anderen Resource Manager-Ressourcen bietet diese Seite eine interaktive Übersicht, das Aktivitätsprotokoll sowie Informationen zu Zugriffssteuerung und Tags. Sie können auf dieser Seite auch direkt eine Ressourcensperre anwenden.

Sie gelangen zur Seite mit Resource Graph-Abfragen, indem Sie im Portalmenü **Alle Dienste** auswählen oder das Azure-Suchfeld verwenden, das sich auf allen Seiten am oberen Rand befindet. Suchen Sie die Option **Resource Graph-Explorer**, und wählen Sie sie aus.

### <a name="list-resource-groups-resources"></a>Auflisten von Ressourcengruppenressourcen

Die Resource Graph-Abfrage wird neben anderen Ressourcen aufgelistet, die zu einer Ressourcengruppe gehören.
Durch Auswählen der Resource Graph-Abfrage wird die Seite für diese Abfrage geöffnet. Die über die Auslassungspunkte und im Kontextmenü verfügbaren Optionen, die durch Auswählen und Halten (oder per Rechtsklick) angezeigt werden, funktionieren genauso wie auf der Seite der Resource Graph-Abfrage.

### <a name="query-resource-graph"></a>Abfragen von Resource Graph

Sie können Resource Graph-Abfragen durch eine Abfrage an Resource Graph finden. Die folgende Resource Graph-Abfrage grenzt die Suche durch den Typ `Microsoft.ResourceGraph/queries` ein und verwendet dann `project`, um nur den Namen, die Uhrzeit der letzten Änderung und die Abfrage selbst aufzulisten:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>Ausführen einer freigegebenen Abfrage

Eine freigegebene Resource Graph-Abfrage kann mit der Syntax `{{shared-query-uri}}` (Vorschauversion) ausgeführt werden. Weitere Informationen finden Sie unter [Syntax für freigegebene Abfragen](../concepts/query-language.md#shared-query-syntax).

## <a name="delete-a-shared-query"></a>Löschen einer freigegebenen Abfrage

Wenn eine freigegebene Abfrage nicht mehr benötigt wird, löschen Sie sie. Indem Sie eine freigegebene Abfrage löschen, entfernen Sie die entsprechende Resource Manager-Ressource. Auf jedem Dashboard, auf dem das Ergebnisdiagramm angeheftet war, wird jetzt eine Fehlermeldung angezeigt. Wenn diese Fehlermeldung angezeigt wird, verwenden Sie die Schaltfläche **Von Dashboard entfernen**, um das Dashboard zu bereinigen.

Sie können eine freigegebene Abfrage über die folgenden Schnittstellen löschen:
- Seite mit Resource Graph-Abfragen
- Seite „Resource Graph-Abfrage“
- Seite **Abfrage öffnen** im Resource Graph-Explorer
- Seite mit Ressourcengruppen

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit diesem Tutorial fertig sind, löschen Sie die privaten und freigegebenen Abfragen, wenn Sie diese nicht mehr benötigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie private und freigegebene Abfragen erstellt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](../concepts/query-language.md).
