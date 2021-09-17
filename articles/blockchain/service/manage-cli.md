---
title: Verwalten des Azure Blockchain-Diensts mit der Azure CLI
description: Verwalten von Azure Blockchain Service mit der Azure CLI
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 6920458795a84e44c19164d1342de1b9a33052d0
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2021
ms.locfileid: "122639660"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Verwalten des Azure Blockchain-Diensts mit der Azure CLI

Sie können Blockchain-Mitglieder und Transaktionsknoten für Ihren Azure Blockchain-Dienst nicht nur über das Azure-Portal, sondern auch über die Azure-Befehlszeilenschnittstelle verwalten.

[!INCLUDE [Retirement note](./includes/retirement.md)]
## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

Wenn Sie die CLI lieber lokal installieren und verwenden, finden Sie hierzu Informationen unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

1. Melden Sie sich an.

    Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az_login) an, falls Sie eine lokale Installation der Befehlszeilenschnittstelle verwenden.

    ```azurecli
    az login
    ```

    Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.

1. Installieren Sie die Erweiterung für die Azure-Befehlszeilenschnittstelle (Azure CLI).

    Bei der Verwendung von Erweiterungsverweisen für die Azure CLI müssen Sie die Erweiterung zunächst installieren.  Mit Azure CLI-Erweiterungen erhalten Sie Zugriff auf experimentelle Befehle und Vorabversionen von Befehlen, die noch nicht als Bestandteil der Kern-CLI bereitgestellt wurden.  Weitere Informationen zu Erweiterungen, u. a. zum Aktualisieren und Deinstallieren, finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview).

    Installieren Sie die [Erweiterung für Azure Blockchain Service](/cli/azure/blockchain), indem Sie den folgenden Befehl ausführen:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Erstellen eines Blockchainmitglieds

In folgenden Beispiel wird ein [Blockchainmitglied im Azure Blockchain-Dienst erstellt](/cli/azure/blockchain/member#az_blockchain_member_create), das das Quorum-Ledgerprotokoll in einem neuen Konsortium ausführt.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Name der Ressourcengruppe, in der die Ressourcen des Azure Blockchain-Diensts erstellt werden. |
| **name** | Ein eindeutiger Name, der Ihr Blockchainmitglied in Azure Blockchain identifiziert. Der Name wird für die Adresse eines öffentlichen Endpunkts verwendet. Beispiel: `myblockchainmember.blockchain.azure.com`. |
| **location** | Die Azure-Region, in der das Blockchainmitglied erstellt wird. Beispiel: `eastus`. Wählen Sie den Standort aus, der Ihren Benutzern oder anderen Azure-Anwendungen am nächsten liegt. Features sind unter Umständen in einigen Regionen nicht verfügbar. |
| **password** | Das Kennwort für den Standardtransaktionsknoten des Mitglieds. Verwenden Sie das Kennwort für die Standardauthentifizierung, wenn Sie eine Verbindung mit dem öffentlichen Endpunkt des Standardtransaktionsknotens des Blockchainmitglieds herstellen. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)).|
| **protocol** | Blockchain-Protokoll. Derzeit wird das *Quorum*-Protokoll unterstützt. |
| **consortium** | Name des Konsortiums, dem beigetreten oder das erstellt werden soll. Weitere Informationen zu Konsortien finden Sie unter [Azure Blockchain Service-Konsortium](consortium.md). |
| **consortium-management-account-password** | Das Kennwort für das Konsortium wird auch als Mitgliedskontokennwort bezeichnet. Das Mitgliedskontokennwort wird zum Verschlüsseln des privaten Schlüssels für das Ethereum-Konto verwendet, das für Ihr Mitglied erstellt wird. Sie verwenden das Mitgliedskonto und das Mitgliedskontokennwort für die Verwaltung des Konsortiums. |
| **sku** | Tarifart. *Standard* oder *Basic*. Verwenden Sie *Basic* für die Entwicklung, das Testen und Proof of Concept-Vorgänge. Verwenden Sie *Standard* für Bereitstellungen für die Produktion. Sie sollten den Tarif *Standard* auch wählen, wenn Sie Blockchain Data Manager verwenden oder eine große Menge privater Transaktionen senden. Das Wechseln zwischen den Tarifen „Basic“ und „Standard“ nach der Erstellung eines Mitglieds wird nicht unterstützt. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Ändern des Kennworts des Blockchainmitglieds oder der Firewallregeln

Im folgenden Beispiel werden das Kennwort [eines Blockchainmitglieds](/cli/azure/blockchain/member#az_blockchain_member_update), das Verwaltungskennwort des Konsortiums und die Firewallregel aktualisiert.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Name der Ressourcengruppe, in der die Ressourcen des Azure Blockchain-Diensts erstellt werden. |
| **name** | Der Name, der Ihr Azure Blockchain-Mitglied identifiziert. |
| **password** | Das Kennwort für den Standardtransaktionsknoten des Mitglieds. Verwenden Sie das Kennwort für die Standardauthentifizierung, wenn Sie eine Verbindung mit dem öffentlichen Endpunkt des Standardtransaktionsknotens des Blockchainmitglieds herstellen. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)).|
| **consortium-management-account-password** | Das Kennwort für das Konsortium wird auch als Mitgliedskontokennwort bezeichnet. Das Mitgliedskontokennwort wird zum Verschlüsseln des privaten Schlüssels für das Ethereum-Konto verwendet, das für Ihr Mitglied erstellt wird. Sie verwenden das Mitgliedskonto und das Mitgliedskontokennwort für die Verwaltung des Konsortiums. |
| **firewall-rules** | Die Start- und End-IP-Adresse für die Liste zugelassener IP-Adressen. |

## <a name="create-transaction-node"></a>Erstellen eines Transaktionsknotens

[Erstellen Sie einen Transaktionsknoten](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_create) in einem vorhandenen Blockchainmitglied. Durch Hinzufügen von Transaktionsknoten können Sie die Sicherheitsisolation erhöhen oder die Last verteilen. Sie können beispielsweise über einen Transaktionsknotenendpunkt für verschiedene Clientanwendungen verfügen.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Name der Ressourcengruppe, in der die Ressourcen des Azure Blockchain-Diensts erstellt werden. |
| **location** | Azure-Region des Blockchainmitglieds. |
| **member-name** | Der Name, der Ihr Azure Blockchain-Mitglied identifiziert. |
| **password** | Das Kennwort für den Transaktionsknoten. Verwenden Sie das Kennwort für die Standardauthentifizierung, wenn Sie eine Verbindung mit dem öffentlichen Endpunkt des Transaktionsknotens herstellen. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)).|
| **name** | Der Name des Transaktionsknotens. |

## <a name="change-transaction-node-password"></a>Ändern des Kennworts des Transaktionsknotens

Im folgenden Beispiel wird das Kennwort des [Transaktionsknotens aktualisiert](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_update).

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **member-name** | Der Name, der Ihr Azure Blockchain-Mitglied identifiziert. |
| **password** | Das Kennwort für den Transaktionsknoten. Verwenden Sie das Kennwort für die Standardauthentifizierung, wenn Sie eine Verbindung mit dem öffentlichen Endpunkt des Transaktionsknotens herstellen. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)).|
| **name** | Der Name des Transaktionsknotens. |

## <a name="list-api-keys"></a>Auflisten von API-Schlüsseln

Mit API-Schlüsseln erhalten Sie Zugriff auf Knoten, ähnlich wie mit einem Benutzernamen und einem Kennwort. Es gibt zwei API-Schlüssel, die die Schlüsselrotation unterstützen. Verwenden Sie den folgenden Befehl, um [Ihre API-Schlüssel aufzulisten](/cli/azure/blockchain/member#az_blockchain_transaction_node_list-api-key):

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds im Azure Blockchain-Dienst |

## <a name="regenerate-api-keys"></a>Erneutes Generieren von API-Schlüsseln

Verwenden Sie den folgenden Befehl, um [Ihre API-Schlüssel erneut zu generieren](/cli/azure/blockchain/member#az_blockchain_transaction_node_regenerate-api-key):

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds im Azure Blockchain-Dienst. |
| **keyName** | Ersetzen Sie \<keyValue\> durch „key1“, „key2“ oder beides. |

## <a name="delete-a-transaction-node"></a>Löschen eines Transaktionsknotens

Im folgenden Beispiel wird der [Transaktionsknoten eines Blockchainmitglieds gelöscht](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **member-name** | Der Name des Azure Blockchain Service-Blockchainmitglieds mit dem Namen des neuen Transaktionsknotens, der gelöscht werden soll |
| **name** | Der Name des zu löschenden Transaktionsknotens. |

## <a name="delete-a-blockchain-member"></a>Löschen eines Blockchainmitglieds

Im folgenden Beispiel wird ein [Blockchainmitglied gelöscht](/cli/azure/blockchain/member#az_blockchain_member_delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **resource-group** | Der Name der Ressourcengruppe, in der sich die Ressourcen des Azure Blockchain-Diensts befinden. |
| **name** | Der Name des Blockchainmitglieds im Azure Blockchain-Dienst, das gelöscht werden soll. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Gewähren des Zugriffs für einen Azure AD-Benutzer

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **role** | Der Name der Azure AD-Rolle. |
| **assignee** | Die Azure AD-Benutzer-ID. Zum Beispiel, `user@contoso.com` |
| **scope** | Der Geltungsbereich der Rollenzuweisung. Dies kann ein Blockchainmitglied oder ein Transaktionsknoten sein. |

**Beispiel:**

Gewähren Sie einem Azure AD-Benutzer Knotenzugriff auf ein **Blockchainmitglied**:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Beispiel:**

Gewähren Sie einem Azure AD-Benutzer Knotenzugriff auf einen **Blockchaintransaktionsknoten**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Gewähren des Knotenzugriffs für Azure AD-Gruppen oder Anwendungsrollen

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **role** | Der Name der Azure AD-Rolle. |
| **assignee-object-id** | Die Azure AD-Gruppen-ID oder -Anwendungs-ID. |
| **scope** | Der Geltungsbereich der Rollenzuweisung. Dies kann ein Blockchainmitglied oder ein Transaktionsknoten sein. |

**Beispiel:**

Gewähren des Knotenzugriffs für **Anwendungsrollen**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Entfernen des Azure AD-Knotenzugriffs

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | BESCHREIBUNG |
|---------|-------------|
| **role** | Der Name der Azure AD-Rolle. |
| **assignee** | Die Azure AD-Benutzer-ID. Zum Beispiel, `user@contoso.com` |
| **scope** | Der Geltungsbereich der Rollenzuweisung. Dies kann ein Blockchainmitglied oder ein Transaktionsknoten sein. |

## <a name="next-steps"></a>Nächste Schritte

Informationen zum [Konfigurieren von Azure Blockchain Service-Transaktionsknoten mit dem Azure-Portal](configure-transaction-nodes.md)
