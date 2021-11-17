---
title: Ziele und Funktionen in Azure Chaos Studio
description: Erfahren Sie, wie Sie das Onboarding von Ressourcen in Azure Chaos Studio mithilfe von Zielen und Funktionen steuern.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 74e3db42471fb4322a9c80cd63f30fee730d7a49
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371484"
---
# <a name="targets-and-capabilities-in-azure-chaos-studio"></a>Ziele und Funktionen in Azure Chaos Studio

Bevor Sie einen Fehler für eine Azure-Ressource injizieren können, müssen für die Ressource zuerst die entsprechenden Ziele und Funktionen aktiviert sein. Ziele und Funktionen steuern, welche Ressourcen für die Fehlerinjektion aktiviert sind und welche Fehler für diese Ressourcen ausgeführt werden können. Wenn Sie Ziele und Funktionen [zusammen mit anderen Sicherheitsmaßnahmen](chaos-studio-permissions-security.md) verwenden, können Sie versehentliche oder böswillige Fehlerinjektion mit Chaos Studio vermeiden. Mit Zielen und Funktionen können Sie beispielsweise zulassen, dass der CPU-Druckfehler auf ihren virtuellen Produktionscomputern ausgeführt wird, während gleichzeitig verhindert wird, dass der Kill-Prozess-Fehler für sie ausgeführt wird.

## <a name="targets"></a>Ziele

Ein Chaos **ziel** ermöglicht Chaos Studio die Interaktion mit einer Ressource für einen bestimmten Zieltyp. Ein **Zieltyp** stellt die Methode zum Injizieren von Fehlern für eine Ressource dar. Ressourcentypen, die nur dienstspezifische Fehler unterstützen, haben einen Zieltyp, z. B. den `Microsoft-CosmosDB`Typ für Azure Cosmos DB. Ressourcentypen, die dienst- und agentbasierte Fehler unterstützen, haben zwei Zieltypen: einen für die dienstbasierten Fehler (z. B. `Microsoft-VirtualMachine`) und einen für agentbasierte Fehler (immer `Microsoft-Agent`).

Ein Ziel ist eine Erweiterungsressource, die als untergeordnetes Element der Ressource erstellt wird, die in Chaos Studio integriert wird (z. B. ein virtueller Computer oder eine Netzwerksicherheitsgruppe). Ein Ziel definiert den Zieltyp, der für die Ressource aktiviert ist. Beispiel: Beim Onboarding einer Cosmos DB-Instanz mit dieser Ressourcen-ID:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB
```

Die Cosmos DB-Ressource hat eine untergeordnete Ressource, die wie folgt formatiert ist:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB
```

Nur Ressourcen, für die ein Ziel erstellt wurde, können für die Fehlerinjektion mit Chaos Studio verwendet werden.

## <a name="capabilities"></a>Funktionen

Mit einer **Funktion** kann Chaos Studio einen bestimmten Fehler für eine Ressource ausführen, z. B. das Herunterfahren eines virtuellen Computers. Funktionen sind pro Zieltyp eindeutig und stellen den Fehler dar, den sie aktivieren, z. B. `CPUPressure-1.0`. [Besuchen Sie die Chaos Studio-Fehlerbibliothek](chaos-studio-fault-library.md), um alle verfügbaren Fehler und die entsprechenden Funktionsnamen und Zieltypen zu verstehen.

Eine Funktion ist eine Erweiterungsressource, die als untergeordnetes Element eines Ziels erstellt wurde. Wenn Sie beispielsweise den Fehler beim Herunterfahren auf einem virtuellen Computer mit einer dienstbasierten Ziel-ID aktivieren:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine
```

Die Cosmos DB-Ressource hat ein untergeordnetes Element, das wie folgt formatiert ist:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0
```

Ein Experiment kann nur Fehler auf integrierten Zielen mit aktivierten entsprechenden Funktionen injizieren. 

## <a name="listing-capability-names-and-parameters"></a>Auflisten von Funktionsnamen und Parametern
Als Referenz ist eine Liste von Funktionsnamen, Fehler-URNs und Parametern [in unserer Fehlerbibliothek](chaos-studio-fault-library.md) verfügbar. Sie können jedoch die HTTP-Antwort verwenden, um eine Funktion oder eine GET-Anforderung für eine vorhandene Funktion zu erstellen, um diese Informationen bei Bedarf zu erhalten. Beispiel: Ausführen einer GET-Funktion zum Herunterfahren einer VM:

```bash
az rest --method get --url "https://management.azure.com/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-08-11-preview"
```

Dies gibt folgendes JSON zurück:

```JSON
{
  "id": "/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0",
  "name": "shutdown-1.0",
  "properties": {
    "description": null,
    "name": "shutdown-1.0",
    "parametersSchema": "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json",
    "publisher": "Microsoft",
    "targetType": "VirtualMachine",
    "type": "shutdown",
    "urn": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
    "version": "1.0"
  },
  "resourceGroup": "myRG",
  "systemData": {
    "createdAt": "2021-09-15T23:00:00.826575+00:00",
    "lastModifiedAt": "2021-09-15T23:00:00.826575+00:00"
  },
  "type": "Microsoft.Chaos/targets/capabilities"
}
```

Die `properties.urn`-Eigenschaft wird verwendet, um den Fehler zu definieren, den Sie in einem Chaosexperiment ausführen möchten. Um das Schema für die Parameter dieses Fehlers zu verstehen, können Sie das Schema, auf das von `properties.parametersSchema` verwiesen wird mittels GET erhalten.

```bash
az rest --method get --url "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json"
```

gibt folgendes JSON zurück:
```JSON
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "properties": {
    "abruptShutdown": {
      "type": "boolean"
    },
    "restartWhenComplete": {
      "type": "boolean"
    }
  },
  "type": "object"
}
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun verstehen, was Ziele und Funktionen sind, können Sie:
- [Erfahren Sie mehr über Fehler und Aktionen](chaos-studio-faults-actions.md)
- [Erstellen und Ausführen des ersten Experiments](chaos-studio-tutorial-service-direct-portal.md)
