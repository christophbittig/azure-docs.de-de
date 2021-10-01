---
title: 'Azure CLI-Skriptbeispiel: Erstellen eines Batch-Kontos – Benutzerabonnement | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie ein Azure Batch-Konto im Modus „Benutzerabonnement“ erstellen. Dieses Konto ordnet Computeknoten in Ihrem Abonnement zu.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: Batch, Azure CLI-Beispiele, Azure CLI-Beispiele, Azure CLI-Codebeispiele
ms.openlocfilehash: 087ba199787e36dccba58f37dd1ebb83c715d729
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658657"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI-Beispiel: Erstellen eines Batch-Kontos im Modus „Benutzerabonnement“

Dieses Skript erstellt ein Azure Batch-Konto im Modus „Benutzerabonnement“. Ein Konto, das Ihrem Abonnement Computeknoten zuordnet, muss über ein Azure Active Directory-Token authentifiziert werden. Die zugeordneten Computeknoten zählen zum vCPU-Kontingent (Kernkontingent) Ihres Abonnements.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.  

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az role assignment create](/cli/azure/role) | Erstellt eine neue Rollenzuweisung für einen Benutzer, eine Gruppe oder einen Dienstprinzipal. |
| [az group create](/cli/azure/group#az_group_create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az keyvault create](/cli/azure/keyvault#az_keyvault_create) | Erstellt einen Schlüsseltresor. |
| [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) | Aktualisiert die Sicherheitsrichtlinie des angegebenen Schlüsseltresors. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Erstellt das Batch-Konto.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az group delete](/cli/azure/group#az_group_delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
