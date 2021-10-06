---
title: Gewähren des Zugriffs für eine Anwendung auf andere Azure-Ressourcen in einem verwalteten Service Fabric-Cluster
description: In diesem Artikel wird erläutert, wie Sie Ihrer Service Fabric-Anwendung, die verwaltete Identitäten nutzen kann, Zugriff auf andere Azure-Ressourcen gewähren, die die Azure Active Directory-basierte Authentifizierung in einem verwalteten Service Fabric-Cluster unterstützen.
ms.topic: article
ms.date: 5/10/2021
ms.openlocfilehash: ba85736779f44d5874bb4a080ce0da1c5ba764f8
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544695"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-on-a-service-fabric-managed-cluster"></a>Gewähren des Zugriffs auf Azure-Ressourcen für die verwaltete Identität einer Service Fabric-Anwendung in einem verwalteten Service Fabric-Cluster

Bevor eine Anwendung ihre verwaltete Identität für den Zugriff auf andere Ressourcen nutzen kann, müssen dieser Identität Berechtigungen in der geschützten Azure-Ressource gewährt werden, auf die zugegriffen werden soll. Das Gewähren von Berechtigungen ist in der Regel eine Verwaltungsaktion auf der Steuerungsebene des Azure-Diensts, der im Besitz der geschützten Ressource ist. Diese Aktion wird über den Azure Resource Manager ausgeführt, der alle anwendbaren rollenbasierten Zugriffsüberprüfungen erzwingt.

Die genaue Abfolge der Schritte richtet sich nach dem Azure-Ressourcentyp, auf den zugegriffen wird, sowie danach, mit welcher Sprache und welchem Client Berechtigungen gewährt werden. Für diesen Artikel wird davon ausgegangen, dass die Anwendung über eine benutzerseitig zugewiesene Identität verfügt. Der Artikel stellt eine Reihe typischer Beispiele zu Ihrer Information vor, ist jedoch in keiner Weise als erschöpfende Referenz für dieses Thema zu betrachten. Aktuelle Anleitungen zum Gewähren von Berechtigungen finden Sie in der Dokumentation des jeweiligen Azure-Diensts.  

## <a name="granting-access-to-azure-storage"></a>Gewähren des Zugriffs auf Azure Storage
Sie können die verwaltete Identität einer Service Fabric-Anwendung (in diesem Fall benutzerseitig zugewiesen) verwenden, um die Daten aus einem Azure Storage-Blob abzurufen. Gehen Sie folgendermaßen vor, um der Identität im Azure-Portal die erforderlichen Berechtigungen zu gewähren:

1. Navigieren Sie zum Speicherkonto.
2. Klicken Sie im linken Bereich auf den Link „Zugriffssteuerung (IAM)“.
3. (Optional) Überprüfen Sie den vorhandenen Zugriff: Wählen Sie im Steuerelement „Suchen“ die Option für eine systemseitig oder eine benutzerseitig zugewiesene verwaltete Identität aus, und wählen Sie die entsprechende Identität aus der Ergebnisliste aus.
4. Klicken Sie oben auf der Seite auf „+ Rollenzuweisung hinzufügen“, um der Identität der Anwendung eine neue Rollenzuweisung hinzuzufügen.
Wählen Sie unter „Rolle“ in der Dropdownliste die Option „Storage-Blobdatenleser“ aus.
5. Wählen Sie in der nächsten Dropdownliste unter „Zugriff zuweisen zu“ die Option `User assigned managed identity` aus.
6. Stellen Sie im nächsten Schritt sicher, dass in der Dropdownliste „Abonnement“ das richtige Abonnement aufgeführt ist, und legen Sie dann für die Ressourcengruppe die Option „Alle Ressourcengruppen“ fest.
7. Wählen Sie unter „Auswählen“ die benutzerseitig zugewiesene Identität aus, die der Service Fabric-Anwendung entspricht, und klicken Sie auf „Speichern“.

Die Unterstützung von systemseitig zugewiesenen verwalteten Identitäten für Service Fabric ist nicht in das Azure-Portal integriert. Wenn Ihre Anwendung eine systemseitig zugewiesene Identität verwendet, müssen Sie zuerst die Client-ID der Anwendungsidentität finden und dann die oben beschriebenen Schritte wiederholen. Wählen Sie in diesem Fall im Steuerelement „Suchen“ die Option `Azure AD user, group, or service principal` aus.

## <a name="granting-access-to-azure-key-vault"></a>Gewähren des Zugriffs auf Azure Key Vault
Ähnlich wie beim Zugriff auf Storage können Sie die verwaltete Identität einer Service Fabric-Anwendung für den Zugriff auf eine Azure Key Vault-Instanz nutzen. Die Schritte zum Gewähren des Zugriffs im Azure-Portal entsprechen den oben aufgeführten und werden an dieser Stelle nicht wiederholt. Die Unterschiede sehen Sie in der folgenden Abbildung.

![Key Vault-Zugriffsrichtlinie](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Das folgende Beispiel veranschaulicht das Gewähren des Zugriffs auf einen Tresor über eine Vorlagenbereitstellung. Fügen Sie die Codeausschnitte unten als weitere Einträge unter dem `resources`-Element der Vorlage hinzu. Das Beispiel veranschaulicht die Gewährung des Zugriffs für vom Benutzer zugewiesene bzw. vom System zugewiesene Identitätstypen. Wählen Sie die entsprechende Option aus.

```json
{
  "variables": {
    "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
  },
  "resources": [
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
              "keys": [ "get", "list" ],
              "secrets": [ "get", "list" ],
              "certificates": [ "get", "list" ]
            }
          }
        ]
      }
    }
  ]
}
```
Vom System zugewiesene verwaltete Identitäten:
```json
{
  "variables": {
    "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/managedClusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
  },
  "resources": [
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
              "certificates": [
                "get",
                "list"
              ]
            }
          }
        ]
      }
    }
  ]
}
```

Weitere Informationen finden Sie unter [Tresore: Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen einer Azure Service Fabric-Anwendung mit einer benutzer- oder systemseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
