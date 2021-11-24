---
title: Zuweisung einer verwalteten Identität zu einer Anwendungsrolle mit Azure CLI - Azure AD
description: Schritt-für-Schritt-Anleitung für die Zuweisung eines verwalteten Identitätszugriffs an die Rolle einer anderen Anwendung mithilfe von Azure CLI.
services: active-directory
documentationcenter: ''
author: christoc
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2021
ms.author: christoc
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 846085c773c1923f581968c06c02c5c4f8372e65
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180207"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-azure-cli"></a>Zuweisung eines verwalteten Identitätszugriffs zu einer Anwendungsrolle mit Azure CLI

Verwaltete Identitäten für Azure-Ressourcen stellen Azure-Diensten eine Identität in Azure Active Directory bereit. Für die Funktionalität sind keine Anmeldeinformationen in Ihrem Code erforderlich. Azure-Dienste nutzen diese Identitäten, um sich bei Diensten zu authentifizieren, die die Azure AD-Authentifizierung unterstützen. Anwendungsrollen stellen eine Art rollenbasierter Zugriffssteuerung dar und ermöglichen es einem Dienst, Autorisierungsregeln zu implementieren.

In diesem Artikel erfahren Sie, wie Sie eine verwaltete Identität einer Anwendungsrolle zuweisen, die von einer anderen Anwendung mithilfe von Azure CLI ausgesetzt wird.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#managed-identity-types)** bewusst.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Zuweisen des Zugriffs einer verwalteten Identität auf die App-Rolle einer anderen Anwendung

1. Aktivieren Sie die verwaltete Identität für eine Azure-Ressource, [zum Beispiel einen virtuellen Azure-Computer](qs-configure-cli-windows-vm.md).

1. Suchen Sie die Objekt-ID des Dienstprinzipals der verwalteten Identität.

   **Bei einer systemseitig zugewiesenen verwalteten Identität** finden Sie die Objekt-ID im Azure-Portal auf der Seite **Identität** der Ressource. Sie können auch das folgende Skript verwenden, um die Objekt-ID zu ermitteln. Sie benötigen die Ressourcen-ID der in Schritt 1 erstellten Ressource, die Sie im Azure-Portal auf der Seite **Eigenschaften** der Ressource finden.

    ```azurecli
    resourceIdWithManagedIdentity="/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}"
    
    oidForMI=$(az resource show --ids $resourceIdWithManagedIdentity --query "identity.principalId" -o tsv | tr -d '[:space:]')
    echo "object id for managed identity is: $oidForMI"
    ```

    **Bei einer benutzerseitig zugewiesenen verwalteten Identität** finden Sie die Objekt-ID der verwalteten Identität im Azure-Portal auf der Seite **Übersicht** der Ressource. Sie können auch das folgende Skript verwenden, um die Objekt-ID zu ermitteln. Sie benötigen die Ressourcen-ID der benutzerseitig zugewiesenen verwalteten Identität.

    ```azurecli
    userManagedIdentityResourceId="/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}"
    
    oidForMI=$(az resource show --id $userManagedIdentityResourceId --query "properties.principalId" -o tsv | tr -d '[:space:]')
    echo "object id for managed identity is: $oidForMI"
    ```

1. Erstellen Sie eine neue Anwendungsregistrierung, um den Dienst zu repräsentieren, an den Ihre verwaltete Identität eine Anforderung senden soll. Wenn die API oder der Dienst, die bzw. der die Gewährung der App-Rolle für die verwaltete Identität verfügbar macht, bereits über einen Dienstprinzipal in Ihrem Azure AD-Mandanten verfügt, überspringen Sie diesen Schritt.

1. Suchen Sie die Objekt-ID des Dienstprinzipals der Dienstanwendung. Sie finden diese im Azure-Portal. Wechseln Sie zu Azure Active Directory, und öffnen Sie die Seite **Unternehmensanwendungen**. Suchen Sie nach der Anwendung und dort nach der **Objekt-ID**. Sie können die Objekt-ID des Dienstherrn auch mit dem folgenden Skript anhand seines Anzeigenamens ermitteln:

    ```azurecli
    appName="{name for your application}"
    serverSPOID=$(az ad sp list --filter "displayName eq 'My App'" --query '[0].objectId' -o tsv | tr -d '[:space:]')
    echo "object id for server service principal is: $serverSPOID"
    ```

    > [!NOTE]
    > Anzeigenamen für Anwendungen sind nicht eindeutig, daher müssen Sie überprüfen, ob Sie den Dienstprinzipal der richtigen Anwendung abrufen.

    Alternativ können Sie die Objekt-ID auch über die eindeutige Anwendungs-ID für Ihre Anwendungsregistrierung finden:

    ```azurecli
    appID="{application id for your application}"
    serverSPOID=$(az ad sp list --filter "appId eq '$appID'" --query '[0].objectId' -o tsv | tr -d '[:space:]')
    echo "object id for server service principal is: $serverSPOID"
    ```

1. Fügen Sie der in Schritt 3 erstellten Anwendung eine [App-Rolle](../develop/howto-add-app-roles-in-azure-ad-apps.md) hinzu. Sie können die Rolle im Azure-Portal oder mithilfe von Microsoft Graph erstellen. Sie können beispielsweise folgendermaßen eine Rolle hinzufügen:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Weisen Sie der verwalteten Identität die App-Rolle zu. Sie benötigen die folgenden Informationen, um die App-Rolle zuzuweisen:
    * `managedIdentityObjectId`: die Objekt-ID des Dienstprinzipals der verwalteten Identität, die Sie in Schritt 2 ermittelt haben
    * `serverServicePrincipalObjectId`: die Objekt-ID des Dienstprinzipals der Serveranwendung, die Sie in Schritt 4 ermittelt haben
    * `appRoleId`: die ID der App-Rolle, die von der Server-App verfügbar gemacht wird und die Sie in Schritt 5 generiert haben; im Beispiel lautet die App-Rollen-ID `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6`
   
   Führen Sie das folgende Skript aus, um die Rollenzuweisung hinzuzufügen.  Beachten Sie, dass diese Funktion nicht direkt in der Azure CLI verfügbar ist und hier stattdessen ein REST-Befehl verwendet wird:

    ```azurecli
    roleguid="0566419e-bb95-4d9d-a4f8-ed9a0f147fa6"
    az rest -m POST -u https://graph.microsoft.com/beta/servicePrincipals/$oidForMI/appRoleAssignments -b "{\"principalId\": \"$oidForMI\", \"resourceId\": \"$serverSPOID\",\"appRoleId\": \"$roleguid\"}"
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
- Informationen zum Aktivieren einer verwalteten Identität auf einem virtuellen Azure-Computer finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von PowerShell](qs-configure-cli-windows-vm.md).
