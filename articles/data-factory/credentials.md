---
title: Verwenden von Anmeldeinformationen
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie mehr über die Verwendung von Azure-Anmeldeinformationen für Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: synapse
ms.openlocfilehash: a588bf8a02c6683143cff8096ee6e03c6b120234
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095981"
---
# <a name="credentials-in-azure-data-factory-and-azure-synapse"></a>Anmeldeinformationen in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="prerequisites"></a>Voraussetzungen

Benutzer*innen müssen über die Rolle „Operator für verwaltete Identität“ (Azure RBAC) oder eine benutzerdefinierte Rolle mit der RBAC-Aktion **Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action** verfügen, um eine benutzerseitig zugewiesene verwaltete Identität als Anmeldeinformation zu konfigurieren. Eine zusätzliche rollenbasierte Zugriffssteuerung ist erforderlich, um Anmeldeinformationen in Synapse zu erstellen und zu verwenden. [Weitere Informationen](../synapse-analytics/security/synapse-workspace-synapse-rbac-roles.md).

## <a name="using-credentials"></a>Verwenden von Anmeldeinformationen

Es werden Anmeldeinformationen eingeführt, die benutzerseitig zugewiesene verwaltete Identitäten und Dienstprinzipale enthalten können und auch die systemseitig zugewiesene verwaltete Identität auflisten, die Sie in den verknüpften Diensten verwenden können, die die AAD-Authentifizierung (Azure Active Directory) unterstützen. So können Sie alle Ihre AAD-basierten Anmeldeinformationen konsolidieren und verwalten.  

Im Folgenden finden Sie die allgemeinen Schritte für die Verwendung einer **benutzerseitig zugewiesene verwaltete Identität** in den verknüpften Diensten für die Authentifizierung. 

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1. Wenn Sie keine benutzerseitig zugewiesene verwaltete Identität in Azure erstellt haben, erstellen Sie zuerst eine im Azure-Portal auf der Seite [Verwaltete Identitäten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities).

1. Ordnen Sie der Data Factory-Instanz eine benutzerseitig zugewiesene verwaltete Identität mit dem Azure-Portal, dem SDK, PowerShell und der REST-API zu. Der folgende Screenshot zeigt die Verwendung des Microsoft Azure-Portals (Blatt „Data Factory“) zum Zuordnen einer benutzerseitig zugewiesenen verwalteten Identität.

   :::image type="content" source="media/credentials/uami-azure-portal.png" alt-text="Screenshot: Verwenden von Azure-Portal zum Zuordnen einer benutzerseitig zugewiesenen verwalteten Identität":::

1. Erstellen Sie interaktiv **Anmeldeinformationen** auf der Data Factory-Benutzeroberfläche. Sie können die benutzerseitig zugewiesene verwaltete Identität auswählen, die der Data Factory in Schritt 1 zugeordnet wurde. 

   :::image type="content" source="media/credentials/create-new-credential.png" alt-text="Screenshot: Erstellen neuer Anmeldeinformationen":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="Screenshot: Konfiguration neuer Anmeldeinformationen":::

1. Erstellen eines neuen verknüpften Diensts und Auswahl der Option **Benutzerseitig zugewiesene verwaltete Identität** unter „Authentifizierung“

   :::image type="content" source="media/credentials/create-new-linked-service.png" alt-text="Screenshot: Neuer verknüpfter Dienst mit Authentifizierung der benutzerseitig zugewiesenen verwalteten Identität":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="Screenshot der neuen Konfiguration des verknüpften Diensts mit benutzerseitig zugewiesener verwalteter Identität und Anmeldeinformationen":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

1. Wenn Sie keine benutzerseitig zugewiesene verwaltete Identität in Azure erstellt haben, erstellen Sie zuerst eine im Azure-Portal auf der Seite [Verwaltete Identitäten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities).

1. Ordnen Sie dem Arbeitsbereich eine benutzerseitig zugewiesene verwaltete Identität mit dem Azure-Portal, dem SDK, PowerShell und der REST-API zu. Der folgende Screenshot zeigt die Verwendung des Microsoft Azure-Portals (Blatt „Identität“) zum Zuordnen einer benutzerseitig zugewiesenen verwalteten Identität.

   :::image type="content" source="media/credentials/synapse-uami-azure-portal.png" alt-text="Screenshot: Verwenden von Azure-Portal zum Zuordnen einer benutzerseitig zugewiesenen verwalteten Identität":::

1. Erstellen Sie interaktiv **Anmeldeinformationen** in Synapse Studio. Sie können die benutzerseitig zugewiesene verwaltete Identität auswählen, die dem Arbeitsbereich in Schritt 1 zugeordnet wurde.

   :::image type="content" source="media/credentials/synapse-create-new-credential.png" alt-text="Screenshot: Erstellen neuer Anmeldeinformationen":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="Screenshot: Konfiguration neuer Anmeldeinformationen":::

1. Erstellen eines neuen verknüpften Diensts und Auswahl der Option **Benutzerseitig zugewiesene verwaltete Identität** unter „Authentifizierung“

   :::image type="content" source="media/credentials/synapse-create-new-linked-service.png" alt-text="Screenshot: Neuer verknüpfter Dienst mit Authentifizierung der benutzerseitig zugewiesenen verwalteten Identität":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="Screenshot der neuen Konfiguration des verknüpften Diensts mit benutzerseitig zugewiesener verwalteter Identität und Anmeldeinformationen":::

---

> [!NOTE] 
> Sie können ein SDK, PowerShell oder REST-APIs für die oben genannten Aktionen verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identität](data-factory-service-identity.md)

Lesen Sie die folgenden Themen, die Ihnen zeigen, wann und wie Sie die verwaltete Identität verwenden können:

- [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopieren von Daten von/zu Azure Data Lake Store mit verwalteten Identitäten für die Ressourcenauthentifizierung mit Azure](connector-azure-data-lake-store.md)

Weitere Hintergründe zu verwalteten Identitäten für Azure-Ressourcen (auf denen verwaltete Identitäten für Data Factory basieren) finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](../active-directory/managed-identities-azure-resources/overview.md).
