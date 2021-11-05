---
title: Verwaltete Azure IoT Hub-Identität | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ausgehende Konnektivität von Ihrem IoT-Hub und anderen Azure-Ressourcen mithilfe von verwalteten Identitäten zulassen.
author: miag
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: miag
ms.openlocfilehash: b6f050c99d57e077e9e60aeb70d0acb11d200adb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072729"
---
# <a name="iot-hub-support-for-managed-identities"></a>IoT Hub-Unterstützung für verwaltete Identitäten 

Verwaltete Identitäten stellen Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD auf sichere Weise bereit. Dadurch entfällt die Notwendigkeit, dass Entwickler Anmeldeinformationen durch Bereitstellung einer Identität verwalten müssen. Es gibt zwei Arten von verwalteten Identitäten: systemseitig und benutzerseitig zugewiesene Identitäten. IoT Hub unterstützt beides. 

In IoT Hub können verwaltete Identitäten für ausgehende Konnektivität von IoT Hub und anderen Azure-Diensten für Features wie [Nachrichtenrouting,](iot-hub-devguide-messages-d2c.md) [Dateiupload](iot-hub-devguide-file-upload.md) und [Massenimport/-export von Geräten](iot-hub-bulk-identity-mgmt.md) verwendet werden. In diesem Artikel erfahren Sie, wie Sie systemseitig zugewiesene und benutzerseitig zugewiesene verwaltete Identitäten in Ihrem IoT Hub für verschiedene Funktionen verwenden können.

## <a name="prerequisites"></a>Voraussetzungen

- Lesen Sie die Dokumentation zu [verwalteten Identitäten für Azure-Ressourcen](./../active-directory/managed-identities-azure-resources/overview.md), um die Unterschiede zwischen systemseitig zugewiesener und benutzerseitig zugewiesener verwalteter Identität zu verstehen.

- Wenn Sie keinen IoT Hub haben, [erstellen Sie einen](iot-hub-create-through-portal.md), bevor Sie den Vorgang fortsetzen.

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

### <a name="add-and-remove-a-system-assigned-managed-identity-in-azure-portal"></a>Hinzufügen und Entfernen einer systemseitig zugewiesenen verwalteten Identität im Azure-Portal

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrem gewünschten IoT-Hub.
2. Navigieren Sie im IoT Hub-Portal zu **Identität**.
3. Wählen Sie auf der Registerkarte **Systemseitig zugewiesen** die Option **Ein** aus, und klicken Sie auf **Speichern**.
4. Zum Entfernen einer systemseitig zugewiesenen verwalteten Identität aus einem IoT-Hub wählen Sie **Aus** aus, und klicken Sie auf **Speichern**.

    :::image type="content" source="./media/iot-hub-managed-identity/system-assigned.png" alt-text="Screenshot: Aktivieren der systemseitig zugewiesenen verwalteten Identität für einen IoT-Hub.":::

### <a name="enable-system-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>Aktivieren der systemseitig zugewiesenen verwalteten Identität zum Zeitpunkt der Huberstellung mithilfe einer ARM-Vorlage

Wenn Sie die systemseitig zugewiesene verwaltete Identität in Ihrem IoT Hub zum Zeitpunkt der Ressourcenbereitstellung aktivieren möchten, verwenden Sie die folgende ARM-Vorlage (Azure Resource Manager). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": 
    {
      "iotHubName": {
        "type": "string",
        "metadata": {
          "description": "Name of iothub resource"
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "S1",
        "metadata": {
          "description": "SKU name of iothub resource, by default is Standard S1"
        }
      },
      "skuTier": {
        "type": "string",
        "defaultValue": "Standard",
        "metadata": {
          "description": "SKU tier of iothub resource, by default is Standard"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
        }
      }
    },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
              "name": "[parameters('skuName')]",
              "tier": "[parameters('skuTier')]",
              "capacity": 1
              }
            }
          ] 
        }
      }
    }
  ]
}
```

Nachdem Sie die Werte für Ihre Ressourcen `name`, `location`, `SKU.name` und `SKU.tier` ersetzt haben, können Sie die Ressource mithilfe der Azure CLI in einer vorhandenen Ressourcengruppe bereitstellen, indem Sie Folgendes verwenden:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

Nachdem die Ressource erstellt wurde, können Sie die systemseitig zugewiesene verwaltete Identität, die Ihrem Hub zugewiesen wurde, mithilfe der Azure CLI abrufen:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie eine benutzerseitig zugewiesene verwaltete Identität über das Azure-Portal einer VM hinzufügen und von ihr entfernen können.

1. Zuerst müssen Sie eine benutzerseitig zugewiesene verwaltete Identität als eigenständige Ressource erstellen. Dazu können Sie die Anleitungen unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity) ausführen.
2. Wechseln Sie zu Ihrem IoT-Hub, und navigieren Sie im Io Hub-Portal zur **Identität**.
3. Klicken Sie unter der Registerkarte **Benutzerzugeordnet** auf **Zuordnen einer benutzerzugeordneten verwalteten Identität**. Wählen Sie die benutzerseitig zugewiesene verwaltete Identität aus, die Sie Ihrem Hub hinzufügen möchten, und klicken Sie auf **Auswählen**. 
4. Sie können eine benutzerseitig zugewiesene Identität aus einem IoT-Hub entfernen. Wählen Sie die benutzerseitig zugewiesene Identität aus, die Sie entfernen möchten, und klicken Sie auf die Schaltfläche **Entfernen**. Beachten Sie, dass Sie die benutzerseitig zugewiesene Identität nur aus IoT-Hub entfernen; sie wird dadurch nicht als Ressource gelöscht. Zum Löschen der benutzerseitig zugewiesenen Identität als Ressource führen Sie die Anleitungen unter [Löschen einer benutzerseitig zugewiesenen verwalteten Identität](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#delete-a-user-assigned-managed-identity) aus.

    :::image type="content" source="./media/iot-hub-managed-identity/user-assigned.png" alt-text="Screenshot: Hinzufügen einer benutzerseitig zugewiesenen verwalteten Identität für einen IoT-Hub.":::

### <a name="enable-user-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>Aktivieren der benutzerseitig zugewiesenen verwalteten Identität zum Zeitpunkt der Huberstellung mithilfe einer ARM-Vorlage

Die folgende Beispielvorlage kann verwendet werden, um einen Hub mit benutzerzugeordneter verwalteter Identität zu erstellen. Diese Vorlage erstellt eine vom Benutzer zugewiesene Identität mit dem Namen *[iothub-name-provided]-identity* und wird dem erstellten IoT-Hub zugewiesen. Sie können die Vorlage ändern, um bei Bedarf mehrere benutzerzugeordnete Identitäten hinzuzufügen.
 
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "iotHubName": {
      "type": "string",
      "metadata": {
        "description": "Name of iothub resource"
      }
    },
  "skuName": {
    "type": "string",
    "defaultValue": "S1",
    "metadata": {
      "description": "SKU name of iothub resource, by default is Standard S1"
    }
  },
  "skuTier": {
    "type": "string",
    "defaultValue": "Standard",
    "metadata": {
      "description": "SKU tier of iothub resource, by default is Standard"
    }
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]",
    "metadata": {
      "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
    }
  }
},
  "variables": {
    "identityName": "[concat(parameters('iotHubName'), '-identity')]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
              "name": "[variables('identityName')]",
              "apiVersion": "2018-11-30",
              "location": "[resourceGroup().location]"
            },
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                  "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]": {}
                }
              },
              "sku": {
                "name": "[parameters('skuName')]",
                "tier": "[parameters('skuTier')]",
                "capacity": 1
              },
              "dependsOn": [
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]"
              ]
            }
          ]
        }
      }
    }
  ]
}
```

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

Nachdem die Ressource erstellt wurde, können Sie die benutzerseitig zugewiesene verwaltete Identität in Ihrem Hub mithilfe der Azure CLI abrufen:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Ausgehende Konnektivität von IoT Hub zu anderen Azure-Ressourcen

Verwaltete Identitäten können für die ausgehende Konnektivität von IoT Hub zu anderen Azure-Diensten für [Nachrichtenweiterleitung](iot-hub-devguide-messages-d2c.md), [Datei-Upload](iot-hub-devguide-file-upload.md) und [Massengeräteimport/-export](iot-hub-bulk-identity-mgmt.md) verwendet werden. Sie können auswählen, welche verwaltete Identität für jede ausgehende IoT Hub-Konnektivität mit kundeneigenen Endpunkten verwendet werden soll, einschließlich Speicherkonten, Event Hubs und Service Bus-Endpunkten.

> [!NOTE]
> Verwenden Sie eine systemseitig zugewiesene verwaltete Identität, um auf private Ressourcen zuzugreifen.

## <a name="configure-message-routing-with-managed-identities"></a>Konfigurieren des Nachrichtenroutings mit verwalteten Identitäten

In diesem Abschnitt wird das [Nachrichten-Routing](iot-hub-devguide-messages-d2c.md) zu einem benutzerdefinierten Ereignis-Hub-Endpunkt als Beispiel verwendet. Das Beispiel gilt auch für andere benutzerdefinierte Routing-Endpunkte. 

1. Zuerst müssen Sie im Azure-Portal zu Ihrem Event Hub wechseln, um der verwalteten Identität den richtigen Zugriff zuzuweisen. Navigieren Sie in Ihrem Event Hub zur Registerkarte **Zugriffssteuerung (IAM)** , klicken Sie auf **Hinzufügen** und dann auf **Rollenzuweisung hinzufügen**. Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „Rollenzuweisung hinzufügen“ deaktiviert.

2. Wählen Sie **Event Hubs-Datensender** als „Rolle“ aus.

    > [!NOTE]
    > Wählen Sie für das Speicherkonto **Mitwirkender an Storage-Blobdaten** ([*nicht* „Mitwirkender“ oder „Speicherkontomitwirkender“](../storage/blobs/assign-azure-role-data-access.md)) als **Rolle** aus. Wählen Sie für Service Bus **Service Bus-Datensender** als **Rolle** aus.

3. Wählen Sie für „benutzerseitig zugewiesen“ unter **Zugriff zuweisen zu** die Option **Benutzerseitig zugewiesene verwaltete Identität** aus. Wählen Sie in der Dropdownliste Ihr Abonnement und Ihre benutzerseitig zugewiesene verwaltete Identität aus. Klicken Sie auf die Schaltfläche **Save** .

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-user-assigned.png" alt-text="Screenshot: Nachrichtenrouting mit zugewiesenen Benutzern":::

4. Wählen Sie für „systemseitig zugewiesen“ unter **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** und dann in der Dropdownliste den Ressourcennamen Ihres IoT-Hubs aus. Klicken Sie auf **Speichern**.

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-system-assigned.png" alt-text="Screenshot: Nachrichtenrouting mit zugewiesenen System":::

    Wenn Sie die Konnektivität mit Ihrem benutzerdefinierten Endpunkt über ein VNet einschränken müssen, müssen Sie die Ausnahme des vertrauenswürdigen Erstanbieters von Microsoft aktivieren, um Ihrem IoT-Hub Zugriff auf den spezifischen Endpunkt zu gewähren. Wenn Sie beispielsweise einen benutzerdefinierten Event Hub-Endpunkt hinzufügen, navigieren Sie in Ihrem Event Hub zur Registerkarte **Firewalls und virtuelle Netzwerke**, und aktivieren Sie die Option **Zugriff aus ausgewählten Netzwerken erlauben**. Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen für **Allow trusted Microsoft services to access event hubs** (Vertrauenswürdigen Microsoft-Diensten den Zugriff auf Event Hubs erlauben). Klicken Sie auf die Schaltfläche **Save** . Dies gilt auch für Speicherkonto und Service Bus. Informieren Sie sich genauer über [IoT Hub-Unterstützung für virtuelle Netzwerke](./virtual-network-support.md).

    > [!NOTE]
    > Sie müssen die vorstehenden Schritte ausführen, um der verwalteten Identität den richtigen Zugriff zuzuweisen, bevor Sie in IoT Hub den Event Hub als benutzerdefinierten Endpunkt hinzufügen. Bitte warten Sie einige Minuten, bis die Rollenzuweisung weitergegeben wurde.

5. Wechseln Sie als Nächstes zu Ihrem IoT-Hub. Navigieren Sie in Ihrem Hub zu **Nachrichtenrouting**, und klicken Sie auf **Benutzerdefinierte Endpunkte**. Klicken Sie auf **Hinzufügen**, und wählen Sie den Typ des Endpunkts aus, den Sie verwenden möchten. In diesem Abschnitt verwenden Sie Event Hub als Beispiel.

6. Wählen Sie unten auf der Seite Ihren bevorzugten **Authentifizierungstyp** aus. In diesem Abschnitt verwenden Sie **Benutzerseitig zugewiesen** als Beispiel. Wählen Sie in der Dropdownliste die bevorzugte benutzerseitig zugewiesene verwaltete Identität aus, und klicken Sie auf **Erstellen**.

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-routing-endpoint.png" alt-text="Screenshot: Event Hub mit zugewiesenen Benutzern":::

7. Der benutzerdefinierte Endpunkt wurde erfolgreich erstellt.

8. Nach der Erstellung können Sie den Authentifizierungstyp weiterhin ändern. Wählen Sie im linken Navigationsbereich **Nachrichtenrouting** und dann **Benutzerdefinierte Endpunkte** aus. Wählen Sie den benutzerdefinierten Endpunkt aus, dessen Authentifizierungstyp Sie ändern möchten, und klicken Sie auf **Authentifizierungstyp ändern**.

9. Wählen Sie den neuen Authentifizierungstyp aus, der bei diesem Endpunkt aktualisiert werden soll, und klicken Sie auf **Speichern**.

## <a name="configure-file-upload-with-managed-identities"></a>Konfigurieren des Dateiuploads mit verwalteten Identitäten

Das IoT Hub-Feature [Dateiupload](iot-hub-devguide-file-upload.md) ermöglicht Geräten das Hochladen von Dateien in ein kundeneigenes Speicherkonto. IoT Hub muss mit dem Speicherkonto verbunden sein, damit der Dateiuploadvorgang funktioniert. Ähnlich wie beim Nachrichtenrouting können Sie den bevorzugten Authentifizierungstyp und die verwaltete Identität für ausgehende IoT Hub-Konnektivität mit Ihrem Azure Storage-Konto auswählen. 

1. Navigieren Sie im Azure-Portal zur Registerkarte **Zugriffssteuerung (IAM)** Ihres Speicherkontos, und klicken Sie im Abschnitt **Rollenzuweisung hinzufügen** auf **Hinzufügen**.
2. Wählen Sie **Mitwirkender an Storage-Blobdaten** (nicht „Mitwirkender“ oder „Speicherkontomitwirkender“) als Rolle aus.
3. Wählen Sie für „benutzerseitig zugewiesen“ unter „Zugriff zuweisen zu“ die Option **Benutzerseitig zugewiesene verwaltete Identität** aus. Wählen Sie in der Dropdownliste Ihr Abonnement und Ihre benutzerseitig zugewiesene verwaltete Identität aus. Klicken Sie auf die Schaltfläche **Save** .
4. Wählen Sie für „systemseitig zugewiesen“ unter **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** und dann in der Dropdownliste den Ressourcennamen Ihres IoT-Hubs aus. Klicken Sie auf **Speichern**.

    Wenn Sie die Konnektivität mit Ihrem Speicherkonto über ein VNet einschränken müssen, müssen Sie die Ausnahme des vertrauenswürdigen Erstanbieters von Microsoft aktivieren, um Ihrem IoT-Hub Zugriff auf das Speicherkonto zu gewähren. Navigieren Sie auf der Ressourcenseite für Ihr Speicherkonto zur Registerkarte **Firewalls und virtuelle Netzwerke**, und aktivieren Sie die Option **Allow access from selected networks** (Zugriff von ausgewählten Netzwerken zulassen). Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen für **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**. Klicken Sie auf die Schaltfläche **Save** . Informieren Sie sich genauer über [IoT Hub-Unterstützung für virtuelle Netzwerke](./virtual-network-support.md). 

    > [!NOTE]
    > Sie müssen die vorstehenden Schritte ausführen, um der verwalteten Identität den richtigen Zugriff zuzuweisen, bevor Sie das Speicherkonto in IoT Hub für einen Dateiupload mithilfe der verwalteten Identität speichern. Bitte warten Sie einige Minuten, bis die Rollenzuweisung weitergegeben wurde.
5. Navigieren Sie auf der Seite für Ihre IoT Hub-Ressource zur Registerkarte **Dateiupload**.
6. Wählen Sie auf der angezeigten Seite den Container aus, den Sie in Ihrem Blobspeicher verwenden möchten, und konfigurieren Sie die **Dateibenachrichtigungseinstellungen, SAS TTL, Standard-TTL und Anzahl maximaler Zustellungen** nach Bedarf. Wählen Sie den bevorzugten Authentifizierungstyp aus, und klicken Sie auf **Speichern**. Wenn bei diesem Schritt eine Fehlermeldung angezeigt wird, legen Sie Ihr Speicherkonto vorübergehend so fest, dass der Zugriff aus **Allen Netzwerken** ermöglicht wird, und wiederholen Sie dann den Vorgang. Sie können die Firewall für das Speicherkonto konfigurieren, sobald die Konfiguration des Dateiuploads beendet ist.

    :::image type="content" source="./media/iot-hub-managed-identity/file-upload.png" alt-text="Screenshot: Dateiupload mit msi.":::

    > [!NOTE]
    > Im Dateiuploadszenario müssen sowohl der Hub als auch Ihr Gerät eine Verbindung mit Ihrem Speicherkonto herstellen. Die vorstehenden Schritte dienen zum Verbinden Ihres IoT-Hubs mit Ihrem Speicherkonto mit dem gewünschten Authentifizierungstyp. Sie müssen Ihr Gerät weiterhin mithilfe des SAS-URI mit dem Speicher verbinden. Heutzutage wird der SAS-URI mithilfe einer Verbindungszeichenfolge generiert. Wir werden bald Unterstützung zum Generieren eines SAS-URI mit verwalteter Identität hinzufügen. Führen Sie bitte die Schritte unter [Dateiupload](iot-hub-devguide-file-upload.md) aus.

## <a name="configure-bulk-device-importexport-with-managed-identities"></a>Konfigurieren des Massenimports/-exports von Geräten mit verwalteten Identitäten

IoT Hub unterstützt die Funktion zum [Importieren und Exportieren von Geräteinformationen](iot-hub-bulk-identity-mgmt.md) per Massenvorgang aus/in vom Kunden bereitgestellte(n) Azure Storage Blob-Instanzen. Diese Funktion erfordert Konnektivität von IoT Hub mit dem Speicherkonto. 

1. Navigieren Sie im Azure-Portal zur Registerkarte **Zugriffssteuerung (IAM)** Ihres Speicherkontos, und klicken Sie im Abschnitt **Rollenzuweisung hinzufügen** auf **Hinzufügen**.
2. Wählen Sie **Mitwirkender an Storage-Blobdaten** (nicht „Mitwirkender“ oder „Speicherkontomitwirkender“) als Rolle aus.
3. Wählen Sie für „benutzerseitig zugewiesen“ unter „Zugriff zuweisen zu“ die Option **Benutzerseitig zugewiesene verwaltete Identität** aus. Wählen Sie in der Dropdownliste Ihr Abonnement und Ihre benutzerseitig zugewiesene verwaltete Identität aus. Klicken Sie auf die Schaltfläche **Save** .
4. Wählen Sie für „systemseitig zugewiesen“ unter **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** und dann in der Dropdownliste den Ressourcennamen Ihres IoT-Hubs aus. Klicken Sie auf **Speichern**.

### <a name="using-rest-api-or-sdk-for-import-and-export-jobs"></a>Verwenden der REST-API oder des SDK für Import- und Exportaufträge

Sie können jetzt die Azure IoT REST-APIs zum Erstellen von Import- und Exportaufträgen verwenden. Dazu müssen Sie im Anforderungstext die folgenden Eigenschaften angeben:

- **storageAuthenticationType**: Legen Sie den Wert auf **identityBased** fest. 
- **inputBlobContainerUri**: Legen Sie diese Eigenschaft nur im Importauftrag fest.
- **outputBlobContainerUri**: Legen Sie diese Eigenschaft für die Import- und Exportaufträge fest.
- **identity**: Legen Sie den Wert auf die verwaltete Identität fest, die verwendet werden soll.

Azure IoT Hub SDKs unterstützen diese Funktion im Registrierungs-Manager des Dienstclients. Der folgende Codeausschnitt zeigt, wie ein Importauftrag bzw. ein Exportauftrag mithilfe des C# SDK ausgelöst werden.

**C#-Codeausschnitt**

```csharp
    // Create an export job
 
    using RegistryManager srcRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForExportJob(
        outputBlobContainerUri: blobContainerUri,
        excludeKeysInExport: false,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```
 
```csharp
    // Create an import job
    
    using RegistryManager destRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForImportJob(
        inputBlobContainerUri: blobContainerUri,
        outputBlobContainerUri: blobContainerUri,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```  

**Python-Codeausschnitt**

```python
# see note below
iothub_job_manager = IoTHubJobManager("<IoT Hub connection string>")

# Create an import job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="import",
    input_blob_container_uri="<input container URI>",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    )
))

# Create an export job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="export",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    exclude_keys_in_export=True,
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    ) 
))
```

> [!NOTE]
> - Wenn **storageAuthenticationType** auf **identityBased** festgelegt wurde und die Eigenschaft **userAssignedIdentity** nicht **NULL** ist, verwenden die Aufträge die angegebene benutzerseitig zugewiesene verwaltete Identität.
> - Wenn der IoT Hub nicht mit der benutzerseitig zugewiesenen verwalteten Identität konfiguriert wurde, die in **userAssignedIdentity** angegeben ist, schlägt der Auftrag fehl.
> - Wenn **storageAuthenticationType** auf **identityBased** festgelegt wurde, hat die Eigenschaft **userAssignedIdentity** den Wert „NULL“. Die Aufträge verwenden systemseitig zugewiesene Identität.
> - Wenn der IoT-Hub nicht mit der benutzerseitig zugewiesenen verwalteten Identität konfiguriert wurde, schlägt der Auftrag fehl.
> - Wenn **storageAuthenticationType** auf **identityBased** festgelegt wurde und weder **benutzerseitig zugewiesene** noch **systemseitig zugewiesene** verwaltete Identitäten auf dem Hub konfiguriert sind, schlägt der Auftrag fehl.

## <a name="sdk-samples"></a>SDK-Beispiele
- [Beispiel für das .NET SDK](https://aka.ms/iothubmsicsharpsample)
- [Beispiel für das Java SDK](https://aka.ms/iothubmsijavasample)
- [Beispiel für das Python SDK](https://aka.ms/iothubmsipythonsample)
- Node.js SDK-Beispiele: [Massenimport von Geräten](https://aka.ms/iothubmsinodesampleimport), [Massenexport von Geräten](https://aka.ms/iothubmsinodesampleexport)

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die unten angegebenen Links, um weitere Informationen zu IoT Hub-Features zu erhalten:

* [Nachrichtenrouting](./iot-hub-devguide-messages-d2c.md)
* [Hochladen von Dateien mit IoT Hub](./iot-hub-devguide-file-upload.md)
* [Importieren und Exportieren von IoT Hub-Geräteidentitäten per Massenvorgang](./iot-hub-bulk-identity-mgmt.md)
