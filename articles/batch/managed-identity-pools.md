---
title: Konfigurieren verwalteter Identitäten in Azure Batch-Pools
description: Erfahren Sie, wie Sie benutzerseitig zugewiesene verwaltete Identitäten auf Azure Batch-Pools aktivieren und verwaltete Identitäten innerhalb der Knoten verwenden.
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 903e173a6028e6bb574dfba618661da802702c2d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444804"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Konfigurieren verwalteter Identitäten in Azure Batch-Pools

Dank [verwalteter Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) brauchen Entwickler keine Anmeldeinformationen mehr zu verwalten. Für die Azure-Ressource in Azure Active Directory (Azure AD) wird eine Identität bereitgestellt, mit der Azure Active Directory (Azure AD)-Token abgerufen werden.

In diesem Thema wird erläutert, wie Sie benutzerseitig zugewiesene verwaltete Identitäten auf Azure Batch-Pools aktivieren und verwaltete Identitäten innerhalb der Knoten verwenden.

> [!IMPORTANT]
> Pools müssen über die [VM-Konfiguration](nodes-and-pools.md#virtual-machine-configuration) konfiguriert werden, um verwaltete Identitäten verwenden zu können.
>
> Das Erstellen von Pools mit verwalteten Identitäten kann mithilfe der [Batch .NET-Verwaltungsbibliothek](/dotnet/api/overview/azure/batch#management-library) erfolgen, wird aber derzeit nicht von der [Batch .NET-Clientbibliothek](/dotnet/api/overview/azure/batch#client-library) unterstützt.

## <a name="create-a-user-assigned-identity"></a>Erstellen einer benutzerseitig zugewiesenen Identität

[Erstellen Sie zunächst Ihre benutzerseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) im selben Mandanten wie Ihr Azure Batch-Konto. Diese verwaltete Identität muss sich nicht in derselben Ressourcengruppe oder unter demselben Abonnement befindet.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Erstellen eines Azure Batch-Pools mit benutzerseitig zugewiesenen verwalteten Identitäten

Nachdem Sie eine oder mehrere benutzerseitig zugewiesene verwaltete Identitäten erstellt haben, können Sie einen Azure Batch-Pool mit dieser verwalteten Identität erstellen, indem Sie die [Azure Batch .NET-Verwaltungsbibliothek](/dotnet/api/overview/azure/batch#management-library) verwenden.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        },
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, UserAssignedIdentities>
            {
                ["Your Identity Resource Id"] =
                    new UserAssignedIdentities()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Verwenden benutzerseitig zugeordneter verwalteter Identitäten in Azure Batch-Knoten

Viele Azure Batch-Technologien, die auf andere Azure-Ressourcen zugreifen, z. B. Azure Storage oder Azure Container Registry, unterstützen verwaltete Identitäten. Weitere Informationen zur Nutzung verwalteter Identitäten mit Azure Batch finden Sie unter folgenden Links:

- [Ressourcendateien](resource-files.md)
- [Ausgabedateien](batch-task-output-files.md#specify-output-files-using-managed-identity)
- [Azure Container Registry](batch-docker-container-workloads.md#managed-identity-support-for-acr)
- [Azure Blob-Containerdateisystem](virtual-file-mount.md#azure-blob-container)

Sie können Ihre Tasks auch manuell so konfigurieren, dass die verwalteten Identitäten direkt auf [Azure-Ressourcen, die verwaltete Identitäten unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) zugreifen können.

Innerhalb der Azure Batch-Knoten können Sie Token der verwalteten Identität abrufen und zur Authentifizierung durch Azure AD-Authentifizierung über den [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) verwenden.

Für Windows lautet das PowerShell-Skript zum Abrufen eines Zugriffstokens für die Authentifizierung wie folgt:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Für Linux lautet das Bash-Skript wie folgt:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Weitere Informationen finden Sie unter [Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer zum Abrufen eines Zugriffstokens](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).
- Erfahren Sie, wie Sie [vom Kunden verwaltete Schlüssel mit benutzerseitig verwalteten Identitäten](batch-customer-managed-key.md) verwenden.
- Erfahren Sie, wie Sie die [automatische Zertifikatsrotation in einem Batch-Pool aktivieren](automatic-certificate-rotation.md).
