---
title: Aktivieren von verwalteten Identitäten für virtuelle Lab-Computer
description: In diesem Artikel wird gezeigt, wie ein Lab-Besitzer benutzerseitig zugewiesene verwaltete Identitäten auf Ihren virtuellen Lab-Computern aktivieren kann.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 824fc69fd7ff1b8d48de98b16daccc055aa34283
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624271"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Aktivieren von benutzerseitig zugewiesenen verwalteten Identitäten auf virtuellen Lab-Computern in Azure DevTest Labs
Als Lab-Besitzer können Sie benutzerseitig zugewiesene verwaltete Identitäten auf Ihren virtuellen Lab-Computern in Azure DevTest Labs aktivieren.

Eine verwaltete Identität kann für die Authentifizierung bei jedem Dienst verwendet werden, der die Azure Active Directory-Authentifizierung, einschließlich Key Vault, unterstützt, ohne dabei Anmeldeinformationen im Code übergeben zu müssen. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

Mit dieser Funktion können Lab-Benutzer Azure-Ressourcen wie Azure SQL-Datenbank im Kontext des Labs freigeben. Die Authentifizierung bei der Ressource wird von der Identität selbst übernommen. Nach der Konfiguration wird jede vorhandene/neu erstellte Lab-VM mit dieser Identität aktiviert. Lab-Benutzer können auf Ressourcen zugreifen, sobald sie bei ihren Computern angemeldet sind.

> [!NOTE]
> Sie können mehrere benutzerseitig zugewiesene verwaltete Identitäten hinzufügen, die auf Ihren Lab-VMs aktiviert werden sollen.

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals
Um eine benutzerseitig zugewiesene verwaltete Identität für Lab-VMs hinzuzufügen, führen Sie diese Schritte aus:

1. [Erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität in Ihrem Abonnement](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).
1. Navigieren Sie zu der Seite **Konfiguration und Richtlinien** Ihres Labs.
1. Wählen Sie im linken Menü **Identität (Preview)** aus.
1. Wählen Sie die Registerkarte **Virtueller Computer** aus.
1. Wählen Sie **Hinzufügen** aus, um eine vorhandene Identität aus einer voraufgefüllten Dropdownliste auszuwählen. 

    > [!div class="mx-imgBorder"]
    > ![Schaltfläche „Identität hinzufügen“](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Wählen Sie eine vorhandene **benutzerseitig verwaltete Identität** aus der Dropdownliste aus, und wählen Sie **OK** aus. 

    > [!div class="mx-imgBorder"]
    > ![Identität hinzufügen](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Verwenden der API

1.  Notieren Sie sich nach dem Erstellen einer Identität die Ressourcen-ID der Identität. Sie sollte in etwa wie das folgende Beispiel aussehen: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.
    
2. Führen Sie eine PUT HTTPS-Methode für die Labressource aus, um dem Feld **managementIdentities** mindestens eine vom Benutzer zugewiesene Identität hinzuzufügen.


    ```json
    {
        "location": "southeastasia",
        "properties": {
        ...
            "managementIdentities": {
               "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}": {}
        },
        ...
        },
    ...
    }
    ```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).
