---
title: 'Azure CLI-Skriptbeispiel: Erstellen eines Batch-Kontos – Batch-Dienst | Microsoft-Dokumentation'
description: In diesem Skriptbeispiel erfahren Sie, wie Sie ein Batch-Konto im Batch-Azure CLI erstellen. Dieses Skript zeigt auch, wie sie verschiedene Eigenschaften des Kontos abfragen oder aktualisieren.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: Batch, Azure CLI-Beispiele, Azure CLI-Codebeispiele, Azure CLI-Skriptbeispiele
ms.openlocfilehash: 0e909f644fddae91a664b11abdbba0ae8c71b2ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548387"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI-Beispiel: Erstellen eines Batch-Kontos im Modus „Batch-Dienst“

Dieses Skript erstellt ein Azure Batch-Konto im Modus „Batch-Dienst“ und zeigt, wie verschiedene Eigenschaften des Kontos abgefragt und aktualisiert werden. Wenn Sie ein Batch-Konto im Standardmodus „Batch-Dienst“ erstellen, werden die Computeknoten standardmäßig intern durch den Batch-Dienst zugewiesen. Zugeordnete Computeknoten unterliegen einem separaten vCPU-Kontingent (Kernkontingent), und das Konto kann entweder über Anmeldeinformationen eines gemeinsam verwendeten Schlüssels oder über ein Azure Active Directory-Token authentifiziert werden.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Erstellt ein Speicherkonto. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Aktualisiert die Eigenschaften des Batch-Kontos.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Ruft Details zum angegebenen Batch-Konto ab.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Ruft die Zugriffsschlüssel zum angegebenen Batch-Konto ab.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
