---
title: Gewähren des Zugriffs auf andere Azure-Ressourcen für eine Anwendung
description: In diesem Artikel wird erläutert, wie Sie Ihrer Service Fabric-Anwendung, die verwaltete Identitäten nutzen kann, Zugriff auf andere Azure-Ressourcen gewähren, die die Azure Active Directory-basierte Authentifizierung unterstützen.
ms.topic: article
ms.date: 12/09/2019
ms.custom: subject-rbac-steps
ms.openlocfilehash: ac383c3d517bbbab2b25c12dc4a06b4fe63e1b89
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122865046"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Gewähren des Zugriffs auf Azure-Ressourcen für die verwaltete Identität einer Service Fabric-Anwendung

Bevor eine Anwendung ihre verwaltete Identität für den Zugriff auf andere Ressourcen nutzen kann, müssen dieser Identität Berechtigungen in der geschützten Azure-Ressource gewährt werden, auf die zugegriffen werden soll. Das Gewähren von Berechtigungen ist in der Regel eine Verwaltungsaktion auf der Steuerungsebene des Azure-Diensts, der im Besitz der geschützten Ressource ist. Diese Aktion wird über den Azure Resource Manager ausgeführt, der alle anwendbaren rollenbasierten Zugriffsüberprüfungen erzwingt.

Die genaue Abfolge der Schritte richtet sich nach dem Azure-Ressourcentyp, auf den zugegriffen wird, sowie danach, mit welcher Sprache und welchem Client Berechtigungen gewährt werden. Für diesen Artikel wird davon ausgegangen, dass die Anwendung über eine benutzerseitig zugewiesene Identität verfügt. Der Artikel stellt eine Reihe typischer Beispiele zu Ihrer Information vor, ist jedoch in keiner Weise als erschöpfende Referenz für dieses Thema zu betrachten. Aktuelle Anleitungen zum Gewähren von Berechtigungen finden Sie in der Dokumentation des jeweiligen Azure-Diensts.  

## <a name="granting-access-to-azure-storage"></a>Gewähren des Zugriffs auf Azure Storage
Sie können die verwaltete Identität einer Service Fabric-Anwendung (in diesem Fall benutzerseitig zugewiesen) verwenden, um die Daten aus einem Azure Storage-Blob abzurufen. Erteilen Sie der Identität die erforderlichen Berechtigungen für das Speicherkonto, indem Sie der verwalteten Identität der Anwendung im Bereich *resource-group* die Rolle [Speicherblob-Datenleser](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) zuweisen.

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).


## <a name="granting-access-to-azure-key-vault"></a>Gewähren des Zugriffs auf Azure Key Vault
Ähnlich wie beim Zugriff auf Storage können Sie die verwaltete Identität einer Service Fabric-Anwendung für den Zugriff auf eine Azure Key Vault-Instanz nutzen. Die Schritte zum Gewähren des Zugriffs im Azure-Portal entsprechen den oben aufgeführten und werden an dieser Stelle nicht wiederholt. Die Unterschiede sehen Sie in der folgenden Abbildung.

![Key Vault-Zugriffsrichtlinie](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Das folgende Beispiel veranschaulicht das Gewähren des Zugriffs auf einen Tresor über eine Vorlagenbereitstellung. Fügen Sie die Codeausschnitte unten als weitere Einträge unter dem `resources`-Element der Vorlage hinzu. Das Beispiel veranschaulicht die Gewährung des Zugriffs für vom Benutzer zugewiesene bzw. vom System zugewiesene Identitätstypen. Wählen Sie die entsprechende Option aus.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
Vom System zugewiesene verwaltete Identitäten:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Weitere Informationen finden Sie unter [Tresore: Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen einer Service Fabric-Anwendung mit verwalteter Identität in einem verwalteten Cluster](how-to-managed-cluster-application-managed-identity.md)
* [Bereitstellen einer Azure Service Fabric-Anwendung mit einer systemseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Bereitstellen einer Azure Service Fabric-Anwendung mit einer benutzerseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
