---
title: Konfigurieren Ihres eigenen Schlüssels zum Verschlüsseln ruhender Azure Event Hubs-Daten
description: Dieser Artikel enthält Informationen dazu, wie Sie einen eigenen Schlüssel für die Verschlüsselung ruhender Azure Event Hubs-Daten konfigurieren.
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: ed230cc1e0a377a580e0f23feeac74f6e0b5489d
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515707"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verschlüsselung von ruhenden Azure Event Hubs-Daten
Azure Event Hubs ermöglicht die Verschlüsselung ruhender Daten mit Azure Storage Service Encryption (Azure SSE). Der Event Hubs-Dienst verwendet zum Speichern der Daten Azure Storage. Alle in Azure Storage gespeicherten Daten werden mit von Microsoft verwalteten Schlüsseln verschlüsselt. Wenn Sie einen eigenen Schlüssel verwenden – Bring Your Own Key (BYOK) oder kundenseitig verwalteter Schlüssel –, werden die Daten trotzdem mit dem von Microsoft verwalteten Schlüssel verschlüsselt. Zusätzlich wird der von Microsoft verwaltete Schlüssel jedoch mit dem kundenseitig verwalteten Schlüssel verschlüsselt. Mit dieser Funktion können Sie kundenseitig verwaltete Schlüssel, die zum Verschlüsseln der von Microsoft verwalteten Schlüssel verwendet werden, erstellen, rotieren, deaktivieren und den Zugriff darauf widerrufen. Die Aktivierung der BYOK-Funktion ist ein einmaliger Setupvorgang für Ihren Namespace.

> [!IMPORTANT]
> - Die BYOK-Funktion wird von den **Premium**- und **Dedicated**-Tarifen von Event Hubs unterstützt.
> - Die Verschlüsselung kann nur für neue oder leere Namespaces aktiviert werden. Wenn der Namespace Event Hubs enthält, tritt beim Verschlüsselungsvorgang ein Fehler auf.

Verwenden Sie Azure Key Vault, um Ihre Schlüssel zu verwalten und die Schlüsselverwendung zu überwachen. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. Weitere Informationen zum Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md)

In diesem Artikel erfahren Sie, wie Sie einen Schlüsseltresor mit kundenseitig verwalteten Schlüsseln mithilfe des Azure-Portals konfigurieren. Informationen zum Erstellen eines Schlüsseltresors über das Azure-Portal finden Sie unter [Schnellstart: Erstellen Sie eine Azure Key Vault-Instanz mithilfe des Azure-Portals](../key-vault/general/quick-create-portal.md).

## <a name="enable-customer-managed-keys-azure-portal"></a>Aktivieren von kundenseitig verwalteten Schlüsseln (Azure-Portal)
Gehen Sie folgendermaßen vor, um kundenseitig verwaltete Schlüssel im Azure-Portal zu aktivieren. Wenn Sie den Dedicated-Tarif verwenden, navigieren Sie zuerst zu Ihrem Event Hubs Dedicated-Cluster.

1. Wählen Sie den Namespace aus, für den Sie BYOK aktivieren möchten.
1. Wählen Sie auf der Seite **Einstellungen** des Event Hubs-Namespace die Option **Verschlüsselung** aus. 
1. Wählen Sie die **Verschlüsselung im Ruhezustand mit kundenseitig verwalteten Schlüsseln**  wie in der folgenden Abbildung gezeigt aus. 

    ![Kundenseitig verwaltete Schlüssel aktivieren](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Einrichten eines Schlüsseltresors mit Schlüsseln
Nachdem Sie kundenseitig verwaltete Schlüssel aktiviert haben, müssen Sie den kundenseitig verwalteten Schlüssel Ihrem Azure Event Hubs-Namespace zuordnen. Event Hubs unterstützt nur Azure Key Vault. Wenn Sie die Option **Verschlüsselung mit kundenseitig verwaltetem Schlüssel** im vorherigen Abschnitt aktivieren, muss der Schlüssel in Azure Key Vault importiert werden. Außerdem müssen die Eigenschaften **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen) für den Schlüssel konfiguriert sein. Diese Einstellungen können mithilfe von [PowerShell](../key-vault/general/key-vault-recovery.md) oder der [CLI](../key-vault/general/key-vault-recovery.md) konfiguriert werden.

1. Befolgen Sie zum Erstellen eines neuen Schlüsseltresors die [Schnellstartanleitung](../key-vault/general/overview.md) für Azure Key Vault. Weitere Informationen zum Importieren vorhandener Schlüssel finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../key-vault/general/about-keys-secrets-certificates.md).

    > [!IMPORTANT]
    > Für die Verwendung kundenseitig verwalteter Schlüssel mit Azure Event Hubs müssen für den Schlüsseltresor zwei erforderliche Eigenschaften konfiguriert werden. Sie lauten wie folgt:  **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen). Diese Eigenschaften sind standardmäßig aktiviert, wenn Sie im Azure-Portal einen neuen Schlüsseltresor erstellen. Wenn Sie diese Eigenschaften jedoch für einen vorhandenen Schlüsseltresor aktivieren möchten, müssen Sie PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden.
1. Verwenden Sie den Befehl [az keyvault create](/cli/azure/keyvault#az_keyvault_create), um beim Erstellen eines Tresors sowohl das vorläufige Löschen als auch den Bereinigungsschutz zu aktivieren.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Verwenden Sie den Befehl [az keyvault update](/cli/azure/keyvault#az_keyvault_update), um einem vorhandenen Tresor (für den bereits das vorläufige Löschen aktiviert ist) den Bereinigungsschutz hinzuzufügen.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Führen Sie diese Schritte durch, um Schlüssel zu erstellen:
    1. Wählen Sie zum Erstellen eines neuen Schlüssels **Generieren/Importieren** im Menü **Schlüssel** unter **Einstellungen** aus.
        
        ![Auswählen der Schaltfläche „Generate/Import“ (Generieren/Importieren)](./media/configure-customer-managed-key/select-generate-import.png)
    1. Legen Sie **Optionen** auf **Generieren** fest, und benennen Sie den Schlüssel.

        ![Erstellen eines Schlüssels](./media/configure-customer-managed-key/create-key.png) 
    1. Sie können diesen Schlüssel jetzt aus der Dropdownliste auswählen, um ihn dem Event Hubs-Namespace zum Verschlüsseln zuzuordnen. 

        ![Auswählen des Schlüssels aus dem Schlüsseltresor](./media/configure-customer-managed-key/select-key-from-key-vault.png)

        > [!NOTE]
        > Um Redundanz zu gewährleisten, können Sie bis zu drei Schlüssel hinzufügen. Wenn einer der Schlüssel abgelaufen ist oder nicht darauf zugegriffen werden kann, werden die anderen Schlüssel für die Verschlüsselung verwendet.
    1. Geben Sie die Details für den Schlüssel ein, und klicken Sie auf **Auswählen**. Dadurch wird die Verschlüsselung des von Microsoft verwalteten Schlüssels mit Ihrem Schlüssel (kundenseitig verwalteter Schlüssel) ermöglicht. 

## <a name="managed-identities"></a>Verwaltete Identitäten
Es gibt zwei Arten von verwalteten Identitäten, die Sie einem Event Hubs-Namespace zuweisen können.

- **Systemseitig zugewiesen**: Sie können eine verwaltete Identität direkt in einem Event Hubs-Namespace aktivieren. Wenn Sie eine systemseitig zugewiesene verwaltete Identität aktivieren, wird in Azure AD eine Identität erstellt, die an den Lebenszyklus des jeweiligen Event Hubs-Namespace gebunden ist. Daher löscht Azure automatisch die Identität, wenn der Namespace gelöscht wird. Entwurfsbedingt kann nur diese Azure-Ressource (der Namespace) diese Identität zum Anfordern von Token von Azure AD verwenden.
- **Benutzerseitig zugewiesen**: Sie können auch eine verwaltete Identität als eigenständige Azure-Ressource erstellen, die als benutzerseitig zugewiesene Identität bezeichnet wird. Sie können eine benutzerseitig zugewiesene verwaltete Identität erstellen und einem oder mehreren Event Hubs-Namespaces zuweisen. Bei benutzerseitig zugewiesenen verwalteten Identitäten wird die Identität getrennt von den Ressourcen verwaltet, für die sie verwendet wird. Sie ist nicht an den Lebenszyklus des Namespace gebunden. Sie können eine benutzerseitig zugewiesene Identität explizit löschen, wenn Sie sie nicht mehr benötigen.    

    Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).


## <a name="encrypt-using-system-assigned-identities-template"></a>Verschlüsseln mithilfe systemseitig zugewiesener Identitäten (Vorlage)
In diesem Abschnitt wird gezeigt, wie die folgenden Aufgaben mithilfe von **Azure Resource Manager-Vorlagen** ausgeführt werden: 

1. Erstellen eines **Event Hubs-Namespace** mit einer verwalteten Dienstidentität
2. Erstellen eines **Schlüsseltresors** und Gewähren von Zugriff für die Dienstidentität 
3. Aktualisieren des Event Hubs-Namespace mit den Schlüsseltresorinformationen (Schlüssel/Wert) 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Erstellen eines Event Hubs-Clusters und -Namespace mit verwalteter Dienstidentität
In diesem Abschnitt erfahren Sie, wie Sie mit einer Azure Resource Manager-Vorlage und PowerShell einen Azure Event Hubs-Namespace mit verwalteter Dienstidentität erstellen. 

1. Erstellen Sie eine Azure Resource Manager-Vorlage für die Erstellung eines Event Hubs-Namespace mit einer verwalteten Dienstidentität. Nennen Sie die Datei **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Erstellen Sie eine Vorlagenparameterdatei mit dem folgenden Namen: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Ersetzen Sie die folgenden Werte: 
    > - `<EventHubsClusterName>`: Der Name Ihres Event Hubs-Clusters.    
    > - `<EventHubsNamespaceName>`: Der Name Ihres Event Hubs-Namespace.
    > - `<Location>`: Der Standort Ihres Event Hubs-Namespace.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Führen Sie den folgenden PowerShell-Befehl aus, um die Vorlage bereitzustellen und einen Event Hubs-Namespace zu erstellen. Rufen Sie dann die ID des Event Hubs-Namespace ab. Sie wird später noch benötigt. Ersetzen Sie `{MyRG}` durch den Namen der Ressourcengruppe, bevor Sie den Befehl ausführen.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Gewähren von Zugriff auf den Schlüsseltresor für die Event Hubs-Namespaceidentität

1. Führen Sie den folgenden Befehl aus, um einen Schlüsseltresor zu erstellen, für den **Bereinigungsschutz** und **vorläufiges Löschen** aktiviert sind: 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (ODER)    
    
    Führen Sie den folgenden Befehl aus, um einen **vorhandenen Schlüsseltresor** zu aktualisieren. Geben Sie vor dem Ausführen des Befehls den Namen der Ressourcengruppe und des Schlüsseltresors an. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Legen Sie die Schlüsseltresor-Zugriffsrichtlinie so fest, dass die verwaltete Identität des Event Hubs-Namespace auf den Schlüsselwert im Schlüsseltresor zugreifen kann. Verwenden Sie die ID des Event Hubs-Namespace aus dem vorherigen Abschnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Verschlüsseln von Daten im Event Hubs-Namespace mit einem kundenseitig verwalteten Schlüssel aus dem Schlüsseltresor
Bisher haben Sie die folgenden Schritte ausgeführt: 

1. Sie haben einen Premium-Namespace mit einer verwalteten Identität erstellt.
2. Sie haben einen Schlüsseltresor erstellt und der verwalteten Identität Zugriff auf den Schlüsseltresor gewährt. 

In diesem Schritt aktualisieren Sie den Event Hubs-Namespace mit Schlüsseltresorinformationen. 

1. Erstellen Sie eine JSON-Datei mit dem Namen **CreateEventHubClusterAndNamespace.json** und folgendem Inhalt: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Erstellen Sie eine Vorlagenparameterdatei: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Ersetzen Sie die folgenden Werte: 
    > - `<EventHubsClusterName>`: Der Name Ihres Event Hubs-Clusters.        
    > - `<EventHubsNamespaceName>`: Der Name Ihres Event Hubs-Namespace.
    > - `<Location>`: Der Standort Ihres Event Hubs-Namespace.
    > - `<KeyVaultName>`: Der Name Ihres Schlüsseltresors.
    > - `<KeyName>`: Der Name des Schlüssels im Schlüsseltresor.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Führen Sie den folgenden PowerShell-Befehl aus, um die Resource Manager-Vorlage bereitzustellen. Ersetzen Sie `{MyRG}` durch den Namen Ihrer Ressourcengruppe, bevor Sie den Befehl ausführen. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="encrypt-using-user-assigned-identities-template"></a>Verschlüsseln mithilfe benutzerseitig zugewiesener Identitäten (Vorlage)

1. Erstellen Sie eine **benutzerseitig zugewiesene Identität**.
1. Erstellen Sie einen **Schlüsseltresor**, und gewähren Sie über Zugriffsrichtlinien Zugriff auf die benutzerseitig zugewiesene Identität.
1. Erstellen Sie einen **Event Hubs-Namespace** mit der verwalteten Benutzeridentität und den Schlüsseltresorinformationen.

### <a name="create-a-user-assigned-identity"></a>Erstellen einer benutzerseitig zugewiesenen Identität
Befolgen Sie die Anweisungen im Artikel [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity), um eine benutzerseitig zugewiesene Identität zu erstellen. Sie können eine benutzerseitig zugewiesene Identität auch über die [CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md), mit [PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), mit einer [Azure Resource Manager-Vorlage](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) und über eine [REST-API](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) erstellen. 

> [!NOTE]
> Sie können einem Namespace bis zu **vier** Benutzeridentitäten zuweisen. Diese Zuordnungen werden gelöscht, wenn der Namespace gelöscht wird oder wenn Sie `identity -> type` in der Vorlage als `None` übergeben. 

### <a name="create-a-key-vault-and-grant-access-to-user-assigned-identity"></a>Erstellen eines Schlüsseltresors und Gewähren des Zugriffs auf eine benutzerseitig zugewiesene Identität 

1. Führen Sie den folgenden Befehl aus, um einen Schlüsseltresor zu erstellen, für den Bereinigungsschutz und vorläufiges Löschen aktiviert ist.

    ```azurepowershell-interactive
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName} -Location "{location}" -EnableSoftDelete -EnablePurgeProtection           
    ```
    
    (ODER)

    Führen Sie den folgenden Befehl aus, um einen vorhandenen Schlüsseltresor zu aktualisieren. Geben Sie vor dem Ausführen des Befehls Werte für Ressourcengruppen und Schlüsseltresornamen an.

    ```azurepowershell-interactive
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force            
    ```
2. Verwenden Sie den folgenden PowerShell-Befehl, um die **Dienstprinzipal-ID** für die Benutzeridentität abzurufen. Im Beispiel ist `ud1` die benutzerseitig zugewiesene Identität, die für die Verschlüsselung verwendet werden soll.

    ```azurepowershell-interactive
    $servicePrincipal=Get-AzADServicePrincipal -SearchString "ud1"    
    ```
3. Gewähren Sie der benutzerseitig zugewiesenen Identität Zugriff auf den Schlüsseltresor, indem Sie eine Zugriffsrichtlinie zuweisen.     

    ```azurepowershell-interactive
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $servicePrincipal.Id -PermissionsToKeys get,wrapKey,unwrapKey,list    
    ```

    > [!NOTE]
    > Sie können bis zu **drei** Schlüssel hinzufügen, aber die für die Verschlüsselung verwendete Benutzeridentität muss für alle Schlüssel identisch sein. Derzeit wird nur eine einzige Verschlüsselungsidentität unterstützt. 

### <a name="create-an-event-hubs-namespace-with-user-identity-and-key-vault-information"></a>Erstellen eines Event Hubs-Namespace mit Benutzeridentität und Schlüsseltresorinformationen
Das Beispiel in diesem Abschnitt zeigt, wie Sie die folgenden Aufgaben mithilfe einer Azure Resource Manager-Vorlage ausführen. 

- Weisen Sie einem Event Hubs-Namespace eine benutzerseitig verwaltete Identität zu.

    ```json
                "identity": {
                    "type": "UserAssigned",
                    "userAssignedIdentities": {
                        "[parameters('identity').userAssignedIdentity]": {}
                    }
                },
    ```    
- Aktivieren Sie die Verschlüsselung im Namespace, indem Sie einen Schlüssel aus Ihrem Schlüsseltresor und die benutzerseitig verwaltete Identität angeben, über die auf den Schlüssel zugegriffen wird. 

    ```json
                    "encryption":{
                       "keySource":"Microsoft.KeyVault",
                       "keyVaultProperties":[
                            {
                                "keyName": "[parameters('keyName')]",
                                "keyVaultUri": "[parameters('keyVaultUri')]",
                                "identity": {
                                    "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                                }
                            }
                       ]
                    }
    ```
   

1. Erstellen Sie eine JSON-Datei mit dem Namen **CreateEventHubsNamespaceWithUserIdentityAndEncryption.json** und folgendem Inhalt:

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
        "clusterName":{
            "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             },
         "identity": {
            "type": "Object",
            "defaultValue": {
                "userAssignedIdentity": ""
            },
            "metadata": {
                "description": "user-assigned identity."
            }
         }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2021-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[parameters('identity').userAssignedIdentity]": {}
                }
            },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                        {
                            "keyName": "[parameters('keyName')]",
                            "keyVaultUri": "[parameters('keyVaultUri')]",
                            "identity": {
                                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                            }
                        }
                   ]
                }
             }
          }
       ]
    }        
    ```  
1. Erstellen Sie eine Vorlagenparameterdatei: **CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json**.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "identity": {
            "value": {
                "userAssignedIdentity": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER MANAGED IDENTITY NAME>"
            }
         }
       }
    }
    ```

    Ersetzen Sie in der Parameterdatei Platzhalter durch entsprechende Werte.
    
    | Platzhalter | value | 
    | ----------- | ----- | 
    | `<EventHubsNamespaceName>` | Der Name des Event Hubs-Namespace. | 
    | `<Location>` | Der Speicherort, in dem der Namespace erstellt werden soll. | 
    | `<KeyVaultName>` | Der Name des Schlüsseltresors. | 
    | `<KeyName>` | Der Name des Schlüssels im Schlüsseltresor. | 
    | `<AZURE SUBSCRIPTION ID>` | die Azure-Abonnement-ID |
    | `<RESOURCE GROUP NAME>` | Die Ressourcengruppe der benutzerseitig verwalteten Identität. | 
    | `<USER MANAGED IDENTITY NAME>` | Der Name der benutzerseitig verwalteten Identität. | 

3. Führen Sie den folgenden PowerShell-Befehl aus, um die Resource Manager-Vorlage bereitzustellen. Ersetzen Sie `{MyRG}` durch den Namen Ihrer Ressourcengruppe, bevor Sie den Befehl ausführen.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name CreateEventHubsNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryption.json -TemplateParameterFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json        
    ```


## <a name="use-both-user-assigned-and-system-assigned-identities"></a>Verwenden von benutzer- und systemseitig zugewiesenen Identitäten
Ein Namespace kann gleichzeitig sowohl über systemseitig als auch über benutzerseitig zugewiesene Identitäten verfügen. In diesem Fall würde die `type`-Eigenschaft „`SystemAssigned`, `UserAssigned`“ lauten, wie im folgenden Beispiel gezeigt. 

```json
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userIdentity1>" : {}
    }
}
```

In diesem Szenario können Sie entweder die systemseitig oder die benutzerseitig zugewiesene Identität zum Verschlüsseln der ruhenden Daten auswählen.  

Wenn Sie in der Resource Manager-Vorlage kein `identity`-Attribut angeben, wird die systemseitig verwaltete Identität verwendet. Hier sehen Sie einen Beispielcodeausschnitt. 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

Im folgenden Beispiel wird die benutzerseitig verwaltete Identität für die Verschlüsselung verwendet. Beachten Sie, dass das `identity`-Attribut auf die benutzerseitig verwaltete Identität festgelegt ist. 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]",
            "identity": {
                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
            }
         }
      ]
   }
}
```

## <a name="enable-infrastructure-or-double-encryption-of-data"></a>Aktivieren der Infrastrukturverschlüsselung (bzw. Mehrfachverschlüsselung) von Daten
Wenn Sie Ihre Daten noch besser schützen möchten, können Sie die Verschlüsselung auf Infrastrukturebene (auch Mehrfachverschlüsselung genannt) aktivieren. 

Bei aktivierter Infrastrukturverschlüsselung werden Daten im Event Hubs-Namespacekonto zweimal (einmal auf Dienstebene und einmal auf Infrastrukturebene) mit zwei unterschiedlichen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln verschlüsselt. Somit schützt die Infrastrukturverschlüsselung von Event Hubs-Daten vor Szenarien, in denen einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde.

Sie können die Infrastrukturverschlüsselung aktivieren, indem Sie in der weiter oben erwähnten Datei **CreateEventHubClusterAndNamespace.json** die Azure Resource Manager-Vorlage mit der Eigenschaft `requireInfrastructureEncryption` aktualisieren, wie im Folgenden gezeigt. 

```json
"properties":{
   "isAutoInflateEnabled":false,
   "maximumThroughputUnits":0,
   "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "requireInfrastructureEncryption":true,
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

## <a name="rotate-revoke-and-cache-encryption-keys"></a>Rotieren, Widerrufen und Zwischenspeichern von Verschlüsselungsschlüsseln

### <a name="rotate-your-encryption-keys"></a>Rotieren der Verschlüsselungsschlüssel
Sie können Ihren Schlüssel im Schlüsseltresor mit dem Rotationsmechanismus von Azure Key Vault rotieren. Aktivierungs- und Ablaufdaten können auch festgelegt werden, um die Schlüsselrotation zu automatisieren. Der Event Hubs-Dienst erkennt neue Schlüsselversionen und beginnt automatisch mit ihrer Verwendung.

### <a name="revoke-access-to-keys"></a>Widerrufen des Zugriffs auf Schlüssel
Wenn Sie den Zugriff auf die Verschlüsselungsschlüssel widerrufen, werden die Daten dadurch nicht aus Event Hubs gelöscht. Der Zugriff auf die Daten ist jedoch nicht über den Event Hubs-Namespace möglich. Sie können den Verschlüsselungsschlüssel mithilfe von Zugriffsrichtlinien oder durch das Löschen des Schlüssels widerrufen. Unter [Sicherer Zugriff auf einen Schlüsseltresor](../key-vault/general/security-features.md) erfahren Sie mehr über Zugriffsrichtlinien und das Sichern Ihres Schlüsseltresors.

Nachdem der Verschlüsselungsschlüssel widerrufen wurde, funktioniert der Event Hubs-Dienst im verschlüsselten Namespace nicht mehr. Wenn der Zugriff auf den Schlüssel aktiviert ist oder der gelöschte Schlüssel wiederhergestellt wurde, wählt der Event Hubs-Dienst den Schlüssel aus, sodass Sie aus dem verschlüsselten Event Hubs-Namespace auf die Daten zugreifen können.

### <a name="caching-of-keys"></a>Zwischenspeichern von Schlüsseln
Die Event Hubs-Instanz (der Ereignishub) fragt die aufgelisteten Verschlüsselungsschlüssel alle fünf Minuten ab. Sie werden zwischengespeichert und bis zum nächsten Abruf nach weiteren fünf Minuten verwendet. Solange mindestens ein Schlüssel verfügbar ist, kann auf den Ereignishub zugegriffen werden. Wenn bei der Abfrage auf keinen der aufgelisteten Schlüssel zugegriffen werden kann, sind sämtliche Ereignishubs nicht mehr verfügbar. 

Es folgen weitere Informationen: 

- Der Event Hubs-Dienst fragt alle fünf Minuten alle kundenseitig verwalteten Schlüssel ab, die im Datensatz des Namespace aufgelistet sind:
    - Wenn ein Schlüssel rotiert wurde, wird der Datensatz mit dem neuen Schlüssel aktualisiert.
    - Wenn ein Schlüssel widerrufen wurde, wird er aus dem Datensatz entfernt.
    - Wenn alle Schlüssel widerrufen wurden, wird der Verschlüsselungsstatus des Namespace auf **Widerrufen** festgelegt. Aus dem Event Hubs-Namespace kann nicht auf die Daten zugegriffen werden.

## <a name="considerations-when-using-geo-disaster-recovery"></a>Überlegungen bei der Verwendung der georedundanten Notfallwiederherstellung

> [!IMPORTANT]
> Zum Aktivieren der georedundanten Notfallwiederherstellung in einem Namespace, der die BYOK-Verschlüsselung verwendet, muss im sekundären Namespace für die Kopplung eine systemseitig oder eine benutzerseitig zugewiesene verwaltete Identität aktiviert sein. 

### <a name="geo-disaster-recovery---encryption-with-system-assigned-identities"></a>Georedundante Notfallwiederherstellung: Verschlüsselung mit systemseitig zugewiesenen Identitäten
Um die Verschlüsselung der von Microsoft verwalteten Schlüssel mit einem kundenseitig verwalteten Schlüssel zu aktivieren, wird eine [Zugriffsrichtlinie](../key-vault/general/secure-your-key-vault.md) für die systemseitig zugewiesene verwaltete Identität in der angegebenen Azure Key Vault-Instanz eingerichtet. Dies gewährleistet den kontrollierten Zugriff auf Azure Key Vault aus dem Azure Event Hubs-Namespace.

Gehen Sie daher folgendermaßen vor:

- Wenn die [georedundante Notfallwiederherstellung](event-hubs-geo-dr.md) bereits für den Event Hubs-Namespace aktiviert ist und Sie den kundenseitig verwalteten Schlüssel aktivieren möchten, gehen Sie folgendermaßen vor:
    - Heben Sie die Kopplung auf.
    - [Legen Sie die Zugriffsrichtlinien](../key-vault/general/assign-access-policy-portal.md) für die systemseitig zugewiesene verwaltete Identität sowohl für den primären als auch für den sekundären Namespace auf die Key Vault-Instanz fest.
    - Richten Sie die Verschlüsselung für den primären Namespace ein.
    - Stellen Sie die Kopplung des primären und des sekundären Namespace wieder her.
- Wenn Sie die georedundante Notfallwiederherstellung für einen Event Hubs-Namespace aktivieren möchten, in dem bereits ein kundenseitig verwalteter Schlüssel eingerichtet ist, führen Sie die folgenden Schritte aus: 
    - [Richten Sie die Zugriffsrichtlinien](../key-vault/general/assign-access-policy-portal.md) für die verwaltete Identität für den sekundären Namespace auf den Schlüsseltresor ein.
    - Koppeln Sie den primären und den sekundären Namespace.

### <a name="geo-disaster-recovery---encryption-with-user-assigned-identities"></a>Georedundante Notfallwiederherstellung: Verschlüsselung mit benutzerseitig zugewiesenen Identitäten
Hier sind einige Vorschläge: 

1.  Erstellen Sie eine verwaltete Identität, und weisen Sie dieser Key Vault-Berechtigungen zu. 
2.  Fügen Sie die Identität als benutzerseitig zugewiesene Identität hinzu, und aktivieren Sie die Verschlüsselung mit der Identität in beiden Namespaces. 
3.  Koppeln von Namespaces 

Bedingungen für die Aktivierung der georedundanten Notfallwiederherstellung und der Verschlüsselung mit benutzerseitig zugewiesenen Identitäten:

1.  Für den sekundären Namespace muss die Verschlüsselung bereits mit einer benutzerseitig zugewiesenen Identität aktiviert sein, wenn er mit einem primären Namespace gekoppelt werden soll, für den die Verschlüsselung aktiviert ist. 
2.  Es ist nicht möglich, die Verschlüsselung für einen bereits gekoppelten primären Namespace zu aktivieren, auch wenn dem sekundären Namespace eine benutzerseitig zugewiesene Identität zugeordnet ist.

## <a name="set-up-diagnostic-logs"></a>Einrichten von Diagnoseprotokollen 
Durch das Einrichten von Diagnoseprotokollen für durch BYOK aktivierte Namespaces erhalten Sie die erforderlichen Informationen zu den Vorgängen. Diese Protokolle können aktiviert und später in einen Event Hub gestreamt, mithilfe von Protokollanalysen analysiert oder in den Speicher gestreamt werden, um benutzerdefinierte Analysen auszuführen. Weitere Informationen zu Diagnoseprotokollen finden Sie unter [Übersicht über Azure-Diagnoseprotokolle](../azure-monitor/essentials/platform-logs-overview.md). Informationen zum Schema finden Sie unter [Überwachen von Datenverweisen](monitor-event-hubs-reference.md#customer-managed-key-user-logs-schema).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:
- [Übersicht über Event Hubs](event-hubs-about.md)
- [Übersicht über Key Vault](../key-vault/general/overview.md)