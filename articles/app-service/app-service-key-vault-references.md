---
title: Verwenden von Key Vault-Verweisen
description: Erfahren Sie, wie Sie Azure App Service und Azure Functions einrichten, um Azure Key Vault-Verweise zu verwenden. Machen Sie Key Vault-Geheimnisse für den Anwendungscode verfügbar.
author: mattchenderson
ms.topic: article
ms.date: 06/11/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 74341e9f4c2436768a73591878f29e7f392b1f3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006827"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Verwenden von Key Vault-Verweisen für App Service und Azure Functions

Dieses Thema zeigt Ihnen, wie Sie mit Geheimnissen von Azure Key Vault in Ihrer App Service- oder Azure Functions-Anwendung arbeiten können, ohne dass Codeänderungen erforderlich sind. [Azure Key Vault](../key-vault/general/overview.md) ist ein Dienst, der eine zentralisierte Verwaltung von Geheimnissen mit voller Kontrolle über Zugriffsrichtlinien und Überprüfungsverlauf ermöglicht.

## <a name="granting-your-app-access-to-key-vault"></a>Gewähren des Zugriffs auf Key Vault für Ihre App

Um Geheimnisse aus Key Vault auslesen zu können, müssen Sie einen Tresor erstellen und Ihrer App die Berechtigung erteilen, darauf zuzugreifen.

1. Erstellen Sie einen Schlüsseltresor anhand dieser [Key Vault-Schnellstartanleitung](../key-vault/secrets/quick-create-cli.md).

1. Erstellen Sie eine [verwaltete Identität](overview-managed-identity.md) für Ihre App.

    Key Vault-Verweise verwenden standardmäßig die systemseitig zugewiesene Identität der App, Sie können aber [eine benutzerseitig zugewiesene Identität angeben](#access-vaults-with-a-user-assigned-identity).

1. Erstellen Sie eine [Zugriffsrichtlinie im Schlüsseltresor](../key-vault/general/security-features.md#privileged-access) für die zuvor von Ihnen erstellte Anwendungsidentität. Aktivieren Sie die „Get“-Geheimnisberechtigung für diese Richtlinie. Konfigurieren Sie nicht die Einstellungen „Autorisierte Anwendung“ oder `applicationId`, da dies mit einer verwalteten Identität nicht kompatibel ist.

### <a name="access-network-restricted-vaults"></a>Zugriff auf Werte mit Netzwerkeinschränkungen

Wenn Ihr Tresor mit [Netzwerkeinschränkungen](../key-vault/general/overview-vnet-service-endpoints.md) konfiguriert ist, müssen Sie auch sicherstellen, dass die Anwendung über Netzwerkzugriff verfügt.

1. Stellen Sie sicher, dass für die Anwendung ausgehende Netzwerkfunktionen konfiguriert sind, wie in [App Service-Netzwerkfunktionen](./networking-features.md) und [Azure Functions-Netzwerkoptionen](../azure-functions/functions-networking-options.md) beschrieben.

    Linux-Anwendungen, die versuchen, private Endpunkte zu verwenden, erfordern außerdem, dass die App explizit so konfiguriert wird, dass der gesamte Datenverkehr über das virtuelle Netzwerk geleitet wird. Diese Anforderung entfällt in einem bevorstehenden Update. Um diese festzulegen, verwenden Sie den folgenden CLI-Befehl:

    ```azurecli
    az webapp config set --subscription <sub> -g <rg> -n <appname> --generic-configurations '{"vnetRouteAllEnabled": true}'
    ```

2. Stellen Sie sicher, dass die Konfiguration des Tresors das Netzwerk oder Subnetz berücksichtigt, über das Ihre App darauf zugreifen wird.

> [!NOTE]
> Windows-Container unterstützen derzeit keine Key Vault-Verweise über die VNet-Integration.

### <a name="access-vaults-with-a-user-assigned-identity"></a>Zugriff auf Tresore mit einer benutzerseitig zugewiesenen Identität

Einige Apps müssen zum Erstellungszeitpunkt auf Geheimnisse verweisen, wenn eine systemseitig zugewiesene Identität noch nicht verfügbar wäre. In diesen Fällen kann eine benutzerseitig zugewiesene Identität erstellt und im Voraus Zugriff auf den Tresor gewährt werden.

Nachdem Sie der benutzerseitig zugewiesenen Identität Berechtigungen erteilt haben, führen Sie die folgenden Schritte aus:

1. [Weisen Sie die Identität](./overview-managed-identity.md#add-a-user-assigned-identity) Ihrer Anwendung zu, sofern noch nicht erfolgt.

1. Konfigurieren Sie die App so, dass sie diese Identität für Key Vault-Verweisvorgänge verwendet, indem Sie die Eigenschaft `keyVaultReferenceIdentity` auf die Ressourcen-ID der benutzerseitig zugewiesenen Identität festlegen.

    ```azurecli-interactive
    userAssignedIdentityResourceId=$(az identity show -g MyResourceGroupName -n MyUserAssignedIdentityName --query id -o tsv)
    appResourceId=$(az webapp show -g MyResourceGroupName -n MyAppName --query id -o tsv)
    az rest --method PATCH --uri "${appResourceId}?api-version=2021-01-01" --body "{'properties':{'keyVaultReferenceIdentity':'${userAssignedIdentityResourceId}'}}"
    ```

Diese Konfiguration gilt für alle Verweise für die App.

## <a name="reference-syntax"></a>Verweissyntax

Ein Key Vault-Verweis hat die Form `@Microsoft.KeyVault({referenceString})`, wobei `{referenceString}` durch eine der folgenden Optionen ersetzt wird:

> [!div class="mx-tdBreakAll"]
> | Verweiszeichenfolge                                                            | BESCHREIBUNG                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** ist der vollständige URI der Datenebene eines Geheimnisses in Key Vault, optional einschließlich einer Version, z. B. `https://myvault.vault.azure.net/secrets/mysecret/` oder `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`.  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** ist erforderlich und muss der Name Ihrer Key Vault-Ressource sein. **SecretName** ist erforderlich und muss der Name des Zielgeheimnisses sein. **SecretVersion** ist optional, gibt aber, sofern vorhanden, die zu verwendende Version des Geheimnisses an. |

Ein vollständiger Verweis kann beispielsweise wie folgt aussehen:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Alternativ:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Drehung

Wenn in der Referenz keine Version angegeben ist, verwendet die App die neueste in Schlüsseltresor vorhandene Version. Sobald neuere Versionen verfügbar werden, z. B. bei einem Rotationsereignis, wird die App automatisch aktualisiert und beginnt innerhalb von 24 Stunden mit der Nutzung der neuesten Version. Die Verzögerung liegt daran, dass App Service die Werte der Schlüsseltresorverweise zwischenspeichert und alle 24 Stunden erneut abruft. Alle Konfigurationsänderungen an der App verursachen eine sofortige Neuabfrage aller Geheimnisse, auf die verwiesen wird.

## <a name="source-application-settings-from-key-vault"></a>Einbinden von Anwendungseinstellungen aus Key Vault

Key Vault-Verweise können als Werte für [Anwendungseinstellungen](configure-common.md#configure-app-settings) verwendet werden, sodass Sie Geheimnisse in Key Vault anstelle der Websitekonfiguration behalten können. Die Anwendungseinstellungen sind im Ruhezustand sicher verschlüsselt, aber wenn Sie die Funktion zum Verwalten von Geheimnissen benötigen, sollten sie in Key Vault aufgenommen werden.

Um einen Key Vault-Verweis für eine [Anwendungseinstellung](configure-common.md#add-or-edit) zu verwenden, legen Sie den Verweis als Wert für die Einstellung fest. Ihre App kann wie gewohnt durch seinen Schlüssel auf das Geheimnis verweisen. Es sind keine Codeänderungen erforderlich.

> [!TIP]
> Die meisten Anwendungseinstellungen, die Key Vault-Verweise verwenden, sollten als Slot-Einstellungen markiert werden, da Sie für jede Umgebung eigene Tresore verwenden sollten.

### <a name="considerations-for-azure-files-mounting"></a>Überlegungen zum Einbinden von Azure Files

Apps können die `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`-Anwendungseinstellung verwenden, um Azure Files als Dateisystem einzubinden. Diese Einstellung verfügt über zusätzliche Überprüfungen, um sicherzustellen, dass die App ordnungsgemäß gestartet werden kann. Die Plattform benötigt eine Inhaltsfreigabe in Azure Files und setzt einen Standardnamen voraus, es sei denn, ein Name wird über die `WEBSITE_CONTENTSHARE`-Einstellung angegeben. Bei Anforderungen, die diese Einstellungen ändern, versucht die Plattform zu überprüfen, ob diese Inhaltsfreigabe vorhanden ist, und versucht, sie zu erstellen, falls dies nicht der Fall ist. Wenn die Inhaltsfreigabe nicht gefunden oder erstellt werden kann, wird die Anforderung blockiert.

Wenn für diese Einstellung Key Vault-Verweise verwendet werden, schlägt diese Überprüfung standardmäßig fehl, da das Geheimnis selbst bei der Verarbeitung der eingehenden Anforderung nicht aufgelöst werden kann. Um dieses Problem zu vermeiden, können Sie die Überprüfung überspringen, indem Sie `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` auf „1“ festlegen. Dadurch werden alle Überprüfungen umgangen, und die Inhaltsfreigabe wird nicht für Sie erstellt. Stellen Sie sicher, dass sie im Voraus erstellt wird. 

> [!CAUTION]
> Wenn Sie die Überprüfung überspringen und entweder die Verbindungszeichenfolge oder die Inhaltsfreigabe ungültig ist, kann die App nicht ordnungsgemäß gestartet werden und gibt nur HTTP 500-Fehler aus.

Im Rahmen der Erstellung der Site ist es auch möglich, dass der Einbindeversuch der Inhaltsfreigabe fehlschlägt, weil Berechtigungen für verwaltete Identitäten nicht weitergegeben oder die Integration des virtuellen Netzwerks nicht eingerichtet wurde. Um dies zu berücksichtigen, können Sie die Einrichtung von Azure Files bis zu einem späteren Zeitpunkt in der Bereitstellungsvorlage verzögern. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellung](#azure-resource-manager-deployment). App Service verwendet ein Standarddateisystem, bis Azure Files eingerichtet ist, und Dateien werden nicht kopiert, weshalb Sie sicherstellen müssen, dass während des Übergangszeitraums keine Bereitstellungsversuche erfolgen, bevor Azure Files eingebunden wird.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-Bereitstellung

Wenn Sie Ressourcenbereitstellungen über Azure Resource Manager-Vorlagen automatisieren, müssen Sie möglicherweise Ihre Abhängigkeiten in einer bestimmten Reihenfolge sortieren, damit diese Funktion funktioniert. Beachten Sie, dass Sie Ihre Anwendungseinstellungen als eigene Ressource definieren müssen, anstatt eine `siteConfig`-Eigenschaft in der Websitedefinition zu verwenden. Grund dafür ist, dass zuerst die Website definiert werden muss, damit die systemseitig zugeordnete Identität gleichzeitig erstellt und in der Zugriffsrichtlinie verwendet werden kann.

Ein Beispiel für eine Pseudovorlage für eine Funktions-App könnte wie folgt aussehen:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.tenantId]",
                        "objectId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2019-09-01').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2019-09-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> In diesem Beispiel hängt die Bereitstellung der Quellcodeverwaltung von den Anwendungseinstellungen ab. Dies ist normalerweise ein unsicheres Verhalten, da sich das App-Einstellungsupdate asynchron verhält. Da wir aber die Anwendungseinstellung `WEBSITE_ENABLE_SYNC_UPDATE_SITE` integriert haben, verläuft das Update synchron. Dies bedeutet, dass die Bereitstellung der Quellcodeverwaltung erst dann beginnt, wenn die Anwendungseinstellungen vollständig aktualisiert wurden. Weitere App-Einstellungen finden Sie unter [Umgebungsvariablen und App-Einstellungen in Azure App Service](reference-app-settings.md).

## <a name="troubleshooting-key-vault-references"></a>Problembehandlung von Key Vault-Verweisen

Wird ein Verweis nicht ordnungsgemäß aufgelöst, wird stattdessen der Verweiswert verwendet. Dies bedeutet, dass für Anwendungseinstellungen eine Umgebungsvariable erstellt wird, deren Wert die `@Microsoft.KeyVault(...)`-Syntax hat. Dies kann dazu führen, dass die Anwendung Fehler auslöst, da sie ein Geheimnis einer bestimmten Struktur erwartet hat.

Meist ist die Ursache hierfür eine fehlerhafte Konfiguration der [Key Vault-Zugriffsrichtlinie](#granting-your-app-access-to-key-vault). Ursache kann jedoch auch sein, dass ein Geheimnis nicht mehr vorhanden ist, oder dass ein Syntaxfehler im Verweis vorliegt.

Ist die Syntax richtig, können Sie weitere Fehlerursachen anzeigen, indem Sie den aktuellen Auflösungsstatus im Portal überprüfen. Navigieren Sie zu Anwendungseinstellungen, und wählen Sie „Bearbeiten“ für den fraglichen Verweis aus. Unterhalb der Einstellungskonfiguration sollten Statusinformationen, einschließlich aller Fehler, angezeigt werden. Das Fehlen dieser Angaben impliziert, dass die Verweissyntax ungültig ist.

Sie können auch einen der integrierten Detektoren verwenden, um zusätzliche Informationen abzurufen.

### <a name="using-the-detector-for-app-service"></a>Verwenden der Erkennung für App Service

1. Navigieren Sie im Portal zu Ihrer App.
2. Wählen Sie **Probleme diagnostizieren und beheben** aus.
3. Wählen Sie **Leistung und Verfügbarkeit** aus, und wählen Sie **Web app down** aus.
4. Suchen Sie nach **Key Vault Application Settings Diagnostics**, und klicken Sie auf **Details**.


### <a name="using-the-detector-for-azure-functions"></a>Verwenden der Erkennung für Azure Functions

1. Navigieren Sie im Portal zu Ihrer App.
2. Navigieren Sie zu **Plattformfeatures**.
3. Wählen Sie **Probleme diagnostizieren und beheben** aus.
4. Wählen Sie **Leistung und Verfügbarkeit** aus, und wählen Sie **Function app down or reporting errors** aus.
5. Klicken Sie auf **Key Vault Application Settings Diagnostics**.
