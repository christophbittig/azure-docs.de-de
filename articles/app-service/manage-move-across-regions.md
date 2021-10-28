---
title: Verschieben von Apps in eine andere Region
description: Erfahren Sie, wie Sie App Service-Ressourcen von einer Region in eine andere Region verschieben.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 67a3b0c8f5fd6a78ef65bdffe5f201ae42f90efe
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216673"
---
# <a name="move-an-app-service-resource-to-another-region"></a>Verschieben einer App Service-Ressource in eine andere Region

In diesem Artikel wird beschrieben, wie Sie App Service-Ressourcen in eine andere Azure-Region verschieben. Sie können Ihre Ressourcen aus verschiedenen Gründen in eine andere Region verschieben. Beispielsweise, um eine neue Azure-Region zu nutzen, um Features oder Dienste bereitzustellen, die nur in bestimmten Regionen verfügbar sind, um interne Richtlinien- und Governanceanforderungen zu erfüllen oder als Reaktion auf die Anforderungen, die bei der Kapazitätsplanung bestimmt wurden.

App Service-Ressourcen sind regionsspezifisch und können nicht in andere Regionen verschoben werden. Stattdessen müssen Sie eine Kopie Ihrer vorhandenen App Service-Ressourcen in der Zielregion erstellen und Ihren Inhalt dann in die neue App verschieben. Wenn für Ihre Quell-App eine benutzerdefinierte Domäne verwendet wird, können Sie sie [in die neue App in der Zielregion](manage-custom-dns-migrate-domain.md) verschieben, sobald Sie den Vorgang abgeschlossen haben.

Um das Kopieren Ihrer App zu vereinfachen, können Sie [eine einzelne App Service-App in einem App Service-Plan in einer anderen Region klonen](app-service-web-app-cloning.md). Für diesen Vorgang gelten jedoch [Einschränkungen](app-service-web-app-cloning.md#current-restrictions) (insbesondere die fehlende Unterstützung für Linux-Apps).

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich die App Service-App in der Azure-Region befindet, aus der Sie sie verschieben möchten.
- Stellen Sie sicher, dass die Zielregion App Service und alle zugehörigen Dienste unterstützt, deren Ressourcen verschoben werden sollen.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Vorbereiten

Ermitteln Sie alle App Service-Ressourcen, die Sie aktuell verwenden. Beispiel:

- App Service-Apps
- [App Service-Pläne](overview-hosting-plans.md)
- [Bereitstellungsslots](deploy-staging-slots.md)
- [In Azure erworbene benutzerdefinierte Domänen](manage-custom-dns-buy-domain.md)
- [TLS-/SSL-Zertifikate](configure-ssl-certificate.md)
- [Integration des virtuellen Azure-Netzwerks](./overview-vnet-integration.md)
- [Hybridverbindungen](app-service-hybrid-connections.md)
- [Verwaltete Identitäten](overview-managed-identity.md)
- [Sicherungseinstellungen](manage-backup.md)

Bestimmte Ressourcen (z.B. importierte Zertifikate oder Hybridverbindungen) umfassen eine Integration in andere Azure-Dienste. Informationen dazu, wie Sie diese Ressourcen innerhalb von Regionen verschieben, finden Sie in der Dokumentation zu den jeweiligen Diensten.

## <a name="move"></a>Move

1. [Erstellen Sie eine Sicherung der Quell-App](manage-backup.md).
1. [Erstellen Sie eine App in einem neuen App Service-Plan in der Zielregion](app-service-plan-manage.md#create-an-app-service-plan).
2. [Stellen Sie die App aus der Sicherung in der Ziel-App wieder her](web-sites-restore.md).
2. Bei Verwendung einer benutzerdefinierten Domäne [binden Sie sie im Vorfeld an die Ziel-App](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) mit `awverify.` und [aktivieren Sie die Domäne in der Ziel-App](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Wählen Sie für alle anderen Einstellungen in Ihrer Ziel-App dieselben Einstellungen wie in der Quell-App, und überprüfen Sie Ihre Konfiguration.
4. Wenn alle Voraussetzungen erfüllt sind und die benutzerdefinierte Domäne auf die Ziel-App zeigen kann, [ordnen Sie den Domänennamen neu zu](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen

Löschen Sie die Quell-App und den App Service-Plan. [Für einen App Service-Plan eines kostenpflichtigen Tarifs fallen auch dann Gebühren an, wenn keine App ausgeführt wird.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Nächste Schritte

[Klonen der Azure App Service-App mit PowerShell](app-service-web-app-cloning.md)