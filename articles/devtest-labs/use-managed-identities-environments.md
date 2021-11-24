---
title: Verwenden von verwalteten Azure-Identitäten zum Erstellen von Umgebungen
description: Erfahren Sie, wie Sie verwaltete Identitäten in Azure verwenden, um Umgebungen in einem Lab in Azure DevTest Labs bereitzustellen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 220937d20c63420501e3ef6bb1b6c5f8a820613d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400317"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Verwenden von verwalteten Azure-Identitäten zum Bereitstellen von Umgebungen in einem Lab 

Als Lab-Besitzer können Sie eine verwaltete Identität verwenden, um Umgebungen in einem Lab bereitzustellen. Dieses Feature hilft in Szenarien, in denen die Umgebung Azure-Ressourcen enthält oder referenziert, die sich außerhalb der Ressourcengruppe der Umgebung befinden. Zu diesen Ressourcen gehören Schlüsseltresore, Kataloge mit freigegebenen Images und Netzwerke. Verwaltete Identitäten ermöglichen die Erstellung von Sandboxumgebungen, die nicht auf die Ressourcengruppe dieser Umgebung beschränkt sind. 

Wenn Sie eine Umgebung erstellen, erstellt das Lab standardmäßig eine vom System zugewiesene Identität, während die Azure Resource Manager Vorlage (ARM-Vorlage) bereitgestellt wird. Die vom System zugewiesene Identität greift im Namen eines Labbenutzers auf Azure-Ressourcen und -Dienste zu. DevTest Labs erstellt standardmäßig eine vom System zugewiesene Identität, wenn die Labumgebung zum ersten Mal erstellt wird. Erfahren Sie mehr darüber, [warum ein Lab eine systemseitig zugewiesene Identität erstellt](configure-lab-identity.md#scenarios-for-using-labs-system-assigned-identity). 

Als Labbesitzer können Sie der systemzugewiesenen Identität des Labs Berechtigungen für den Zugriff auf Azure-Ressourcen außerhalb des Labs gewähren. Sie können auch Ihre vom Benutzer zugewiesene Identität für das Szenario verwenden. Die systemseitig zugewiesene Identität des Labs ist nur für die Lebensdauer des Labs gültig. Wenn Sie das Lab löschen, wird die systemseitig zugewiesene Identität gelöscht. Wenn Sie Umgebungen in mehreren Labs haben, die eine Identität verwenden müssen, sollten Sie die Verwendung einer benutzerseitig zugewiesenen Identität in Betracht ziehen.  

> [!NOTE]
> Zurzeit wird eine einzige vom Benutzer zugewiesene Identität pro Lab unterstützt. 

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität über das Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Stellen Sie sicher, dass Ihre verwaltete Identität in der gleichen Region und im gleichen Abonnement wie ihr Lab erstellt wurde. Die verwaltete Identität muss sich nicht in derselben Ressourcengruppe befinden.

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals

In diesem Abschnitt verwenden Sie als Lab-Besitzer das Azure-Portal, um dem Lab eine vom Benutzer verwaltete Identität hinzuzufügen. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **DevTest-Labs**.
1. Wählen Sie in der Liste der Labs das gewünschte Lab aus.
1. Wählen Sie **Konfiguration und Richtlinien** -> **Identität (Vorschau)** aus. 
1. Wählen Sie die Registerkarte **Vom Benutzer zugewiesen** aus, um eine vom Benutzer zugewiesene Identität hinzuzufügen.
1. Klicken Sie auf **Hinzufügen**.
1. Wählen Sie einen vorhandenen Benutzer aus der Dropdownliste aus, den Sie erstellt haben und/oder auf den Sie Zugriff besitzen.
 
    ![Hinzufügen einer vom Benutzer verwalteten Identität](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Klicken Sie oben auf der Seite auf **Speichern**.

    Nach dem Speichern verwendet das Lab diese Identität während der Bereitstellung aller Lab-Umgebungen. Sie können auch auf die Identitätsressource in Azure zugreifen, indem Sie die Identität in der Liste auswählen. 

Der Labbesitzer muss nichts Besonderes tun, um eine Umgebung bereitzustellen. Die dem Lab hinzugefügte Identität muss über Berechtigungen für die externen Ressourcen verfügen, auf die die Umgebung zugreifen muss. 

Wenn Sie die dem Lab zugewiesene vom Benutzer verwaltete Identität ändern möchten, entfernen Sie zuerst die dem Lab angefügte Identität, und fügen Sie dann dem Lab eine weitere hinzu. Um eine dem Lab angefügte Identität zu entfernen, wählen Sie **... (Auslassungszeichen)** aus, und klicken Sie auf **Entfernen**. 

## <a name="use-api"></a>Verwenden der API

1. Notieren Sie sich nach dem Erstellen einer Identität die Ressourcen-ID dieser Identität. Sie sollte in etwa wie das folgende Beispiel aussehen: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.

1. Führen Sie eine PUT HTTPS-Methode für die Labressource aus, um eine benutzerseitig zugewiesene Identität hinzuzufügen oder eine systemseitig zugewiesene Identität für das Lab zu aktivieren.

   > [!NOTE]
   > Unabhängig davon, ob Sie eine benutzerseitig zugewiesene Identität erstellen, erstellt das Lab automatisch eine systemseitig zugewiesene Identität, wenn zum ersten Mal eine Laborumgebung erstellt wird. Wenn jedoch bereits eine benutzerseitig zugewiesene Identität für das Lab konfiguriert ist, verwendet der DevTest Lab-Dienst diese Identität weiterhin, um Labumgebungen bereitzustellen. 
 
    ```json
    
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{labname}

    {
        "location": "{location}",
        "properties": {
          **lab properties**
         } 
        "identity":{
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}":{}
            }
        } 
    }
    
    ```
 
Nachdem Sie dem Lab die vom Benutzer zugewiesene Identität hinzugefügt haben, verwendet der DevTest Labs-Dienst die Identität beim Bereitstellen Azure Resource Manager Umgebungen. Wenn Sie beispielsweise Ihre Resource Manager Vorlage benötigen, um auf ein Shared Image Gallery-Image zuzugreifen, stellen Sie sicher, dass die Identität über die erforderlichen Berechtigungen für die Shared Image Gallery-Ressource verfügt. 
