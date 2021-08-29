---
title: 'Konfigurieren von Blockchain Data Manager mithilfe der Azure CLI: Azure Blockchain Service'
description: Erstellen und Verwalten von Blockchain Data Manager-Instanzen für Azure Blockchain Service mithilfe der Azure CLI
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: b4742e4ed71c5d7e53d810f1c59e8f465f566d7e
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2021
ms.locfileid: "122639655"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Konfigurieren von Blockchain Data Manager mithilfe der Azure CLI

Konfigurieren Sie Blockchain Data Manager für den Azure Blockchain Service, um Blockchaindaten zu erfassen und an ein Azure Event Grid-Thema zu senden.

[!INCLUDE [Retirement note](./includes/retirement.md)]

So konfigurieren Sie eine Blockchain Data Manager-Instanz

* Erstellen einer Blockchain Data Manager-Instanz
* Erstellen einer Eingabe für einen Azure Blockchain Service-Transaktionsknoten
* Erstellen einer Ausgabe für ein Azure Event Grid-Thema
* Hinzufügen einer Blockchainanwendung
* Starten einer Instanz

## <a name="prerequisites"></a>Voraussetzungen

* Installieren der neuesten [Azure CLI](/cli/azure/install-azure-cli) und Anmelden unter Verwendung von `az login`
* [Quickstart: Herstellen einer Verbindung mit einem Azure Blockchain Service-Konsortiumsnetzwerk mithilfe von Visual Studio Code](connect-vscode.md) aus. Der Tarif *Standard* von Azure Blockchain Service wird empfohlen, wenn Sie Blockchain Data Manager verwenden.
* Erstellen eines [Event Grid-Themas](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Weitere Informationen zu [Ereignishandlern in Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

Wenn Sie es vorziehen, die CLI lokal zu installieren und zu verwenden, müssen Sie für diese Schnellstartanleitung mindestens Version 2.0.51 der Azure CLI verwenden. Führen Sie `az --version` aus, um die Version zu ermitteln. Wenn Sie die CLI installieren oder aktualisieren müssen, finden Sie die entsprechende Anleitung unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Erstellen einer Instanz

Eine Blockchain Data Manager-Instanz überwacht einen Azure Blockchain Service-Transaktionsknoten. Eine Instanz erfasst alle Block- und Transaktionsrohdaten aus dem Transaktionsknoten. Blockchain Data Manager veröffentlicht eine **RawBlockAndTransactionMsg**-Nachricht, die eine Obermenge von Informationen ist, die von [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock)- und [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction)-web3.eth-Abfragen zurückgegeben werden.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| resource-group | Der Name der Ressourcengruppe, in der die Blockchain Data Manager-Instanz erstellt werden soll |
| name | Der Name der Blockchain Data Manager-Instanz |
| resource-type | Der Ressourcentyp für eine Blockchain Data Manager-Instanz ist **Microsoft.blockchain/watchers**. |
| is-full-object | Gibt an, dass Eigenschaften Optionen für die Watcherressource enthalten. |
| properties | Eine Zeichenfolge im JSON-Format, die Eigenschaften für die Watcherressource enthält. Kann als Zeichenfolge oder Datei übergeben werden.  |

### <a name="create-instance-examples"></a>Beispiele für das Erstellen einer Instanz

JSON-Konfigurationsbeispiel zum Erstellen einer Blockchain Manager-Instanz in der Region **USA, Osten**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | BESCHREIBUNG |
|---------|-------------|
| location | Die Region, in der die Watcherressource erstellt werden soll |
| properties | Eigenschaften, die beim Erstellen der Watcherressource festgelegt werden sollen |

Erstellen einer Blockchain Data Manager-Instanz namens *mywatcher* mithilfe einer JSON-Konfigurationszeichenfolge

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Erstellen einer Blockchain Data Manager-Instanz namens *mywatcher* mithilfe einer JSON-Konfigurationsdatei

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Erstellen einer Eingabe

Durch eine Eingabe wird Blockchain Data Manager mit einem Azure Blockchain Service-Transaktionsknoten verbunden. Nur Benutzer, die Zugriff auf den Transaktionsknoten haben, können eine Verbindung herstellen.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| resource-group | Der Name der Ressourcengruppe, in der die Eingaberessource erstellt werden soll |
| name | Der Name der Eingabe |
| Namespace | Verwenden Sie den Anbieternamespace **Microsoft.Blockchain**. |
| resource-type | Der Ressourcentyp für eine Blockchain Data Manager-Eingabe lautet **inputs**. |
| parent | Der Pfad zu dem Watcher, dem die Eingabe zugeordnet wird, beispielsweise **watchers/mywatcher** |
| is-full-object | Gibt an, dass Eigenschaften Optionen für die Eingaberessource enthalten. |
| properties | Eine Zeichenfolge im JSON-Format, die Eigenschaften für die Eingaberessource enthält. Kann als Zeichenfolge oder Datei übergeben werden. |

### <a name="input-examples"></a>Beispiele für Eingaben

JSON-Konfigurationsbeispiel zum Erstellen einer Eingaberessource in der Region *USA, Osten*, die mit \<Blockchain member\> verbunden ist.

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Element | BESCHREIBUNG |
|---------|-------------|
| location | Die Region, in der die Eingaberessource erstellt werden soll |
| inputType | Der Ledger-Typ des Azure Blockchain Service-Members. Derzeit wird **Ethereum** unterstützt. |
| resourceId | Der Transaktionsknoten, mit dem die Eingabe verbunden ist. Ersetzen Sie \<Subscription ID\>, \<Resource group\> und \<Blockchain member\> durch die Werte für die Transaktionsknotenressource. Die Eingabe wird mit dem Standardtransaktionsknoten für den Azure Blockchain Service-Member verbunden. |

Erstellen einer Eingabe namens *myInput* für *mywatcher* mithilfe einer JSON-Konfigurationszeichenfolge

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Erstellen einer Eingabe namens *myInput* für *mywatcher* mithilfe einer JSON-Konfigurationsdatei

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Erstellen einer Ausgabe

Eine ausgehende Verbindung sendet Blockchaindaten an Azure Event Grid. Sie können Blockchaindaten an ein einzelnes Ziel oder an mehrere Ziele senden. Blockchain Data Manager unterstützt ausgehende Verbindungen mit mehreren Event Grid-Themen für eine beliebige Blockchain Data Manager-Instanz.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| resource-group | Der Name der Ressourcengruppe, in der die Ausgaberessource erstellt werden soll |
| name | Der Name der Ausgabe |
| Namespace | Verwenden Sie den Anbieternamespace **Microsoft.Blockchain**. |
| resource-type | Der Ressourcentyp für eine Blockchain Data Manager-Ausgabe lautet **outputs**. |
| parent | Der Pfad zu dem Watcher, dem die Ausgabe zugeordnet wird, beispielsweise **watchers/mywatcher** |
| is-full-object | Gibt an, dass Eigenschaften Optionen für die Ausgaberessource enthalten. |
| properties | Eine Zeichenfolge im JSON-Format, die Eigenschaften für die Ausgaberessource enthält. Kann als Zeichenfolge oder Datei übergeben werden. |

### <a name="output-examples"></a>Beispiele für Ausgaben

JSON-Konfigurationsbeispiel zum Erstellen einer Ausgaberessource in der Region *USA, Osten*, die mit dem Event Grid-Thema \<event grid topic\> verbunden ist.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Element | BESCHREIBUNG |
|---------|-------------|
| location | Die Region, in der die Ausgaberessource erstellt werden soll |
| outputType | Der Typ der Ausgabe. Derzeit wird **EventGrid** unterstützt. |
| resourceId | Die Ressource, mit der die Ausgabe verbunden ist. Ersetzen Sie \<Subscription ID\>, \<Resource group\> und \<Blockchain member\> durch die Werte für die Event Grid-Ressource. |

Erstellen einer Ausgabe namens *myoutput* für *mywatcher*, die mit einem Event Grid-Thema verbunden wird, mithilfe einer JSON-Konfigurationszeichenfolge

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Erstellen einer Ausgabe namens *myoutput* für *mywatcher*, die mit einem Event Grid-Thema verbunden wird, mithilfe einer JSON-Konfigurationsdatei

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Hinzufügen einer Blockchainanwendung

Wenn Sie eine Blockchainanwendung hinzufügen, werden Ereignis- und Eigenschaftsstatus der Anwendung von Blockchain Data Manager decodiert. Andernfalls werden nur Block- und Transaktionsrohdaten gesendet. Blockchain Data Manager ermittelt auch Vertragsadressen, wenn der Vertrag bereitgestellt wird. Sie können einer Blockchain Data Manager-Instanz mehrere Blockchainanwendungen hinzufügen.


> [!IMPORTANT]
> Derzeit werden Blockchainanwendungen, die [Arraytypen](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) oder [Zuordnungstypen](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) von Solidity deklarieren, nicht vollständig unterstützt. Als Array- oder Zuordnungstypen deklarierte Eigenschaften werden in Meldungen vom Typ *ContractPropertiesMsg* oder *DecodedContractEventsMsg* nicht decodiert.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| resource-group | Der Name der Ressourcengruppe, in der die Anwendungsressource erstellt werden soll |
| name | Der Name der Anwendung |
| Namespace | Verwenden Sie den Anbieternamespace **Microsoft.Blockchain**. |
| resource-type | Der Ressourcentyp für eine Blockchain Data Manager-Anwendung lautet **artifacts**. |
| parent | Der Pfad zu dem Watcher, dem die Anwendung zugeordnet wird, beispielsweise **watchers/mywatcher** |
| is-full-object | Gibt an, dass Eigenschaften Optionen für die Anwendungsressource enthalten. |
| properties | Eine Zeichenfolge im JSON-Format, die Eigenschaften für die Anwendungsressource enthält. Kann als Zeichenfolge oder Datei übergeben werden. |

### <a name="blockchain-application-examples"></a>Beispiele für Blockchainanwendungen

JSON-Konfigurationsbeispiel zum Erstellen einer Anwendungsressource in der Region *USA, Osten*, die einen Smart Contract überwacht, der durch die Vertrags-ABI und Bytecode definiert wurde

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Element | BESCHREIBUNG |
|---------|-------------|
| location | Die Region, in der die Anwendungsressource erstellt werden soll |
| artifactType | Der Typ der Anwendung. Derzeit wird **EthereumSmartContract** unterstützt. |
| abiFileUrl | Die URL für die JSON-Datei der Smart Contract-ABI. Weitere Informationen zum Abrufen der Vertrags-ABI und zum Erstellen einer URL finden Sie unter [Abrufen von Vertrags-ABI und Bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) und [Erstellen von Vertrags-ABI und Bytecode-URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | Die URL für die JSON-Datei des per Smart Contract bereitgestellten Bytecodes. Weitere Informationen zum Abrufen des per Smart Contract bereitgestellten Bytecodes und zum Erstellen einer URL finden Sie unter [Abrufen von Vertrags-ABI und Bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) und [Erstellen von Vertrags-ABI und Bytecode-URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Hinweis: Blockchain Data Manager benötigt den **bereitgestellten Bytecode**. |
| queryTargetTypes | Die veröffentlichten Nachrichtentypen. Durch die Angabe von **ContractProperties** wird der Nachrichtentyp *ContractPropertiesMsg* veröffentlicht. Durch die Angabe von **ContractEvents** wird der Nachrichtentyp *DecodedContractEventsMsg* veröffentlicht. Hinweis: Die Nachrichtentypen *RawBlockAndTransactionMsg* und *RawTransactionContractCreationMsg* werden immer veröffentlicht. |

Erstellen einer Anwendung namens *myApplication* für *mywatcher*, die einen Smart Contract überwacht, der durch eine JSON-Zeichenfolge definiert wurde

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Erstellen einer Anwendung namens *myApplication* für *mywatcher*, die einen Smart Contract überwacht, der durch eine JSON-Konfigurationsdatei definiert wurde

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Starten einer Instanz

Beim Ausführen einer Blockchain Manager-Instanz werden Blockchainereignisse aus den definierten Eingaben überwacht und Daten an die definierten Ausgaben gesendet.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| action | Verwenden Sie **start**, um den Watcher auszuführen. |
| ids | Die Watcherressourcen-ID Ersetzen Sie \<Subscription ID\>, \<Resource group\> und \<Watcher name\> durch die Werte für die Watcherressource.|

### <a name="start-instance-example"></a>Beispiel zum Starten einer Instanz

Starten einer Blockchain Data Manager-Instanz namens *mywatcher*

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Beenden einer Instanz

Beenden einer Blockchain Data Manager-Instanz

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| action | Verwenden Sie **stop**, um den Watcher zu beenden. |
| ids | Der Name des Watchers. Ersetzen Sie \<Subscription ID\>, \<Resource group\> und \<Watcher name\> durch die Werte für die Watcherressource. |

### <a name="stop-watcher-example"></a>Beispiel für das Beenden des Watchers

Beenden einer Instanz namens *mywatcher*

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Löschen einer Instanz

Löschen einer Blockchain Data Manager-Instanz

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| resource-group | Der Name der Ressourcengruppe des zu löschenden Watchers |
| name | Der Name des zu löschenden Watchers |
| resource-type | Der Ressourcentyp für einen Blockchain Data Manager-Watcher ist **Microsoft.blockchain/watchers**. |

### <a name="delete-instance-example"></a>Beispiel für das Löschen einer Instanz

Löschen einer Instanz namens *mywatcher* in der Ressourcengruppe *myRG*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie im Rahmen des nächsten Tutorials, mit Blockchain Data Manager und Azure Cosmos DB einen Explorer für Blockchaintransaktionsnachrichten zu erstellen.

> [!div class="nextstepaction"]
> [Verwenden von Blockchain Data Manager zum Senden von Daten an Azure Cosmos DB](data-manager-cosmosdb.md)
