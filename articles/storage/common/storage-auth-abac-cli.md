---
title: 'Tutorial: Hinzufügen einer Rollenzuweisungsbedingung zum Einschränken des Zugriffs auf Blobs mithilfe der Azure CLI (Vorschau) – Azure ABAC'
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI und von Azure ABAC (Attribute-Based Access Control, attributbasierte Zugriffssteuerung) eine Rollenzuweisungsbedingung hinzufügen, um den Zugriff auf Blobs einzuschränken.
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: 4050d9452a8253bbfe468b180a8eaa701e2a3684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587459"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-cli-preview"></a>Tutorial: Hinzufügen einer Rollenzuweisungsbedingung zum Einschränken des Zugriffs auf Blobs mithilfe der Azure CLI (Vorschau)

> [!IMPORTANT]
> Azure RBAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In den meisten Fällen gewährt eine Rollenzuweisung die für Azure-Ressourcen benötigten Berechtigungen. Manchmal ist jedoch unter Umständen eine präzisere Zugriffssteuerung erforderlich, was durch Hinzufügen einer Rollenzuweisungsbedingung erreicht werden kann.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Hinzufügen einer Bedingung zu einer Rollenzuweisung
> - Einschränken des Zugriffs auf Blobs basierend auf einem Blobindextag

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu den Voraussetzungen für das Hinzufügen oder Bearbeiten von Rollenzuweisungsbedingungen finden Sie unter [Voraussetzungen für Bedingungen](../../role-based-access-control/conditions-prerequisites.md).

## <a name="condition"></a>Bedingung

In diesem Tutorial wird der Zugriff auf Blobs mit einem bestimmten Tag eingeschränkt. Hierzu wird beispielsweise einer Rollenzuweisung eine Bedingung hinzugefügt, die dafür sorgt, dass von Chandra nur Dateien mit dem Tag „Project=Cascade“ gelesen werden können.

![Diagramm: Rollenzuweisung mit einer Bedingung](./media/shared/condition-role-assignment-rg.png)

Wenn Chandra versucht, ein Blob ohne das Tag „Project=Cascade“ zu lesen, wird der Zugriff verweigert.

![Diagramm: Lesezugriff auf Blobs mit dem Tag „Project=Cascade“](./media/shared/condition-access.png)

Im Code sieht die Bedingung wie folgt aus:

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
    )
)
```

## <a name="step-1-sign-in-to-azure"></a>Schritt 1: Anmelden bei Azure

1. Verwenden Sie den Befehl [az login](/cli/azure/reference-index#az_login), und befolgen Sie die angezeigten Anweisungen, um sich als [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../../role-based-access-control/built-in-roles.md#owner) bei Ihrem Verzeichnis anzumelden.

    ```azurecli
    az login
    ```

1. Verwenden Sie [az account show](/cli/azure/account#az_account_show), um die ID Ihrer Abonnements abzurufen.

    ```azurecli
    az account show
    ```

1. Ermitteln Sie die Abonnement-ID, und initialisieren Sie die Variable.

    ```azurecli
    subscriptionId="<subscriptionId>"
    ```

## <a name="step-2-create-a-user"></a>Schritt 2: Erstellen eines Benutzers

1. Verwenden Sie [az ad user create](/cli/azure/ad/user#az_ad_user_create), um einen Benutzer zu erstellen oder nach einem vorhandenen Benutzer zu suchen. In diesem Tutorial wird Chandra als Beispiel verwendet.

1. Initialisieren Sie die Variable für die Objekt-ID des Benutzers.

    ```azurecli
    userObjectId="<userObjectId>"
    ```

## <a name="step-3-set-up-storage"></a>Schritt 3: Einrichten des Speichers

Der Zugriff auf Blobspeicher kann über die Azure-Befehlszeilenschnittstelle autorisiert werden. Hierzu können Sie entweder Azure AD-Anmeldeinformationen oder den Speicherkonto-Zugriffsschlüssel verwenden. In diesem Artikel wird die Autorisierung von Blobspeichervorgängen mithilfe von Azure AD gezeigt. Weitere Informationen finden Sie unter [Schnellstart: Erstellen, Herunterladen und Auflisten von Blobs mit der Azure-Befehlszeilenschnittstelle](../blobs/storage-quickstart-blobs-cli.md).

1. Erstellen Sie mithilfe von [az storage account](/cli/azure/storage/account) ein Speicherkonto, das mit der Blobindexfunktion kompatibel ist. Weitere Informationen finden Sie unter [Verwalten und Finden von Azure-Blobdaten mit Blobindextags (Vorschau)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support).

1. Erstellen Sie mithilfe von [az storage container](/cli/azure/storage/container) innerhalb des Speicherkontos einen neuen Container, und legen Sie die öffentliche Zugriffsebene auf **Privat (kein anonymer Zugriff)** fest.

1. Verwenden Sie [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload), um eine Textdatei in den Container hochzuladen.

1. Fügen Sie der Textdatei das folgende Blobindextag hinzu. Weitere Informationen finden Sie unter [Verwenden von Blobindextags (Vorschau) zum Verwalten und Suchen von Daten in Azure Blob Storage](../blobs/storage-blob-index-how-to.md).

    > [!NOTE]
    > Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden.

    | Schlüssel | Wert |
    | --- | --- |
    | Project  | Cascade |

1. Laden Sie eine zweite Textdatei in den Container hoch.

1. Fügen Sie der zweiten Textdatei das folgende Blobindextag hinzu:

    | Schlüssel | Wert |
    | --- | --- |
    | Project  | Baker |

1. Initialisieren Sie die folgenden Variablen mit den von Ihnen verwendeten Namen:

    ```azurecli
    resourceGroup="<resourceGroup>"
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameCascade="<blobNameCascade>"
    blobNameBaker="<blobNameBaker>"
    ```

## <a name="step-4-assign-a-role-with-a-condition"></a>Schritt 4: Zuweisen einer Rolle mit einer Bedingung

1. Initialisieren Sie die Rollenvariablen [Storage-Blobdatenleser](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader).

    ```azurecli
    roleDefinitionName="Storage Blob Data Reader"
    roleDefinitionId="2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. Initialisieren Sie den Bereich für die Ressourcengruppe.

    ```azurecli
    scope="/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. Initialisieren Sie die Bedingung.

    ```azurecli
    condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
    ```

    Wenn die Verlaufserweiterung in Bash aktiviert ist, wird aufgrund des Ausrufezeichens (!) unter Umständen die Meldung `bash: !: event not found` angezeigt. In diesem Fall können Sie die Verlaufserweiterung mit dem Befehl `set +H` deaktivieren. Verwenden Sie `set -H`, um die Verlaufserweiterung erneut zu aktivieren.

    In Bash hat ein Dollarzeichen ($) eine besondere Bedeutung für die Erweiterung. Wenn Ihre Bedingung ein Dollarzeichen ($) enthält, müssen Sie ihm ggf. einen umgekehrten Schrägstrich (\\) voranstellen. In dieser Bedingung werden beispielsweise Dollarzeichen zum Trennen der Tagschlüsselnamen verwendet. Weitere Informationen zu Regeln für Anführungszeichen in Bash finden Sie unter [Doppelte Anführungszeichen](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html).

1. Initialisieren Sie die Bedingungsversion und -beschreibung.

    ```azurecli
    conditionVersion="2.0"
    description="Read access to blobs with the tag Project=Cascade"
    ```

1. Verwenden Sie [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), um dem Benutzer in einem Ressourcengruppenbereich die Rolle [Storage-Blobdatenleser](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) zuzuweisen.

    ```azurecli
    az role assignment create --assignee-object-id $userObjectId --scope $scope --role $roleDefinitionId --description "$description" --condition "$condition" --condition-version $conditionVersion
    ```

    Beispiel für die Ausgabe:

    ```azurecli
    {
      "canDelegate": null,
      "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
      "conditionVersion": "2.0",
      "description": "Read access to blobs with the tag Project=Cascade",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "name": "{roleAssignmentId}",
      "principalId": "{userObjectId}",
      "principalType": "User",
      "resourceGroup": "{resourceGroup}",
      "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

## <a name="step-5-optional-view-the-condition-in-the-azure-portal"></a>(Optional) Schritt 5: Anzeigen der Bedingung im Azure-Portal

1. Öffnen Sie die Ressourcengruppe im Azure-Portal.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Navigieren Sie auf der Registerkarte „Rollenzuweisungen“ zur Rollenzuweisung.

1. Klicken Sie in der Spalte **Bedingung** auf **Anzeigen/bearbeiten**, um die Bedingung anzuzeigen.

    ![Screenshot: Bedingung „Rollenzuweisung hinzufügen“ im Azure-Portal](./media/shared/condition-view.png)

## <a name="step-6-test-the-condition"></a>Schritt 6: Testen der Bedingung

1. Öffnen Sie ein neues Befehlsfenster.

1. Verwenden Sie [az login](/cli/azure/reference-index#az_login), um sich als Chandra anzumelden.

    ```azurecli
    az login
    ```

1. Initialisieren Sie die folgenden Variablen mit den von Ihnen verwendeten Namen:

    ```azurecli
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameBaker="<blobNameBaker>"
    blobNameCascade="<blobNameCascade>"
    ```

1. Versuchen Sie, mithilfe von [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) die Eigenschaften der Datei für das Projekt „Baker“ zu lesen.

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameBaker --auth-mode login
    ```

    Im Anschluss sehen Sie ein Beispiel für die Ausgabe. Wie Sie sehen, können Sie die Datei aufgrund der hinzugefügten Bedingung **nicht** lesen.

    ```azurecli
    You do not have the required permissions needed to perform this operation.
    Depending on your operation, you may need to be assigned one of the following roles:
        "Storage Blob Data Contributor"
        "Storage Blob Data Reader"
        "Storage Queue Data Contributor"
        "Storage Queue Data Reader"

    If you want to use the old authentication method and allow querying for the right account key, please use the "--auth-mode" parameter and "key" value.
    ```

1. Lesen Sie die Eigenschaften der Datei für das Projekt „Cascade“.

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameCascade --auth-mode login 
    ```

    Im Anschluss sehen Sie ein Beispiel für die Ausgabe. Wie Sie sehen, können Sie die Eigenschaften der Datei lesen, da sie über das Tag „Project=Cascade“ verfügt.

    ```azurecli
    {
      "container": "<containerName>",
      "content": "",
      "deleted": false,
      "encryptedMetadata": null,
      "encryptionKeySha256": null,
      "encryptionScope": null,
      "isAppendBlobSealed": null,
      "isCurrentVersion": null,
      "lastAccessedOn": null,
      "metadata": {},
      "name": "<blobNameCascade>",
      "objectReplicationDestinationPolicy": null,
      "objectReplicationSourceProperties": [],
      "properties": {
        "appendBlobCommittedBlockCount": null,
        "blobTier": "Hot",
        "blobTierChangeTime": null,
        "blobTierInferred": true,
        "blobType": "BlockBlob",
        "contentLength": 7,
        "contentRange": null,

      ...

    }
    ```

## <a name="step-7-optional-edit-the-condition"></a>(Optional) Schritt 7: Bearbeiten der Bedingung

1. Verwenden Sie im anderen Befehlsfenster [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list), um die hinzugefügte Rollenzuweisung abzurufen.

    ```azurecli
    az role assignment list --assignee $userObjectId --resource-group $resourceGroup
    ```

    Die Ausgabe sieht etwa wie folgt aus:

    ```azurecli
    [
      {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ]
    ```

1. Erstellen Sie eine JSON-Datei im folgenden Format, und aktualisieren Sie die Eigenschaften `condition` und `description`.

    ```json
    {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Baker'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade or Project=Baker",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

1. Aktualisieren Sie mithilfe von [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) die Bedingung für die Rollenzuweisung.

    ```azurecli
    az role assignment update --role-assignment "./path/roleassignment.json"
    ```

## <a name="step-8-clean-up-resources"></a>Schritt 8: Bereinigen der Ressourcen

1. Verwenden Sie [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete), um die hinzugefügte Rollenzuweisung und Bedingung zu entfernen.

    ```azurecli
    az role assignment delete --assignee $userObjectId --role "$roleDefinitionName" --resource-group $resourceGroup
    ```

1. Löschen Sie das von Ihnen erstellte Speicherkonto.

1. Löschen Sie den von Ihnen erstellten Benutzer.

## <a name="next-steps"></a>Nächste Schritte

- [Exemplarische Azure-Rollenzuweisungsbedingungen (Vorschau)](storage-auth-abac-examples.md)
- [Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)](storage-auth-abac-attributes.md)
- [Format und Syntax von Azure-Rollenzuweisungsbedingungen](../../role-based-access-control/conditions-format.md)
