---
title: 'Azure CLI-Skriptbeispiel: Ausführen eines Batchauftrags | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie mithilfe der Azure CLI einen Batchauftrag erstellen und diesem eine Reihe von Aufgaben hinzufügen. In diesem Artikel wird auch gezeigt, wie Sie einen Auftrag und seine Aufgaben überwachen.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: Batch, Batchauftrag, Überwachungsauftrag, Azure CLI-Beispiele, Azure CLI-Codebeispiele, Azure CLI-Skriptbeispiele
ms.openlocfilehash: 293e517a528b6a1630ff8597f4fa17c87008a78f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595175"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI-Beispiel: Ausführen eines Auftrags und von Aufgaben mit Azure Batch

Dieses Skript erstellt einen Batch-Auftrag und fügt eine Reihe von Aufgaben zum Auftrag hinzu. Außerdem wird die Überwachung eines Auftrags und seiner Aufgaben veranschaulicht. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Erstellt das Batch-Konto. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Erstellt einen Pool mit Computeknoten.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Erstellt einen Batch-Auftrag.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Fügt eine Aufgabe zum angegebenen Batch-Auftrag hinzu.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Aktualisiert die Eigenschaften eines Batch-Auftrags.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Ruft Details eines angegebenen Batch-Auftrags ab.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Ruft die Details einer Aufgabe aus dem angegebenen Batch-Auftrag ab.  |
| [az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
