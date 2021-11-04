---
title: Wie man Ressourcen registriert
titleSuffix: Azure Network Function Manager
description: Erfahren Sie, wie Sie Ressourcen registrieren und benutzerdefinierte verwaltete Identitäten erstellen können.
author: prmitt
ms.service: network-function-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: e42527e5fdca01b58c08458bc360268b5d36d7d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095603"
---
# <a name="how-to-register-resources-and-create-user-assigned-managed-identities"></a>Registrieren von Ressourcen und Erstellen von benutzerzugeordneten verwalteten Identitäten

Dieser Artikel hilft Ihnen, die Anforderungen für die Erstellung von Geräteressourcen, verwalteten Anwendungsressourcen und benutzerzugewiesenen verwalteten Identitäten für die Bereitstellung von Netzwerkfunktionen zu verstehen.

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>Registrierung bei und Berechtigungen von Ressourcenanbietern

Die Netzwerkfunktionsmanager-Ressourcen befinden sich beim Ressourcenanbieter Microsoft.HybridNetwork. Registrieren Sie die Abonnement-ID beim Microsoft.HybridNetwork-Ressourcenanbieter. Weitere Informationen zur Registrierung finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="device-resource-accounts"></a>Geräte-Ressourcen-Konten

Die Konten, die Sie zum Erstellen der Netzwerkfunktionsmanager-Geräteressource verwenden, müssen einer benutzerdefinierten Rolle zugeordnet werden, der die erforderlichen Aktionen aus der folgenden Tabelle zugewiesen sind. Weitere Informationen finden Sie unter [Benutzerdefinierte Rollen](../role-based-access-control/custom-roles.md).

| Name | Aktion|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|Erforderlich für das Lesen der Azure Stack Edge-Ressource, auf der die Netzwerkfunktionen bereitgestellt werden. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Erforderlich für das Lesen der Eigenschaften der Azure Stack Edge-Ressource. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Erforderlich für das Erstellen der Netzwerkfunktionsmanager-Geräteressource auf der Azure Stack Edge-Ressource.|
| Microsoft.HybridNetwork/devices/* | Erforderlich für das Erstellen, Aktualisieren und Löschen der Netzwerkfunktionsmanager-Geräteressource. |

### <a name="managed-applications-resource-accounts"></a>Verwaltete Ressourcenkonten für Anwendungen

Die Konten, die Sie für das Erstellen der Azure Managed Applications-Ressource verwenden, müssen einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md) zugeordnet werden, der die erforderlichen Aktionen aus der folgenden Tabelle zugewiesen sind: 

|Name |Action |
|---|---|
|[Rolle „Mitwirkender für verwaltete Anwendungen“](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|Erforderlich für das Erstellen von verwalteten App-Ressourcen.|

## <a name="managed-identity"></a><a name="managed-identity"></a>Verwaltete Identität

Netzwerkfunktionspartner, die ihre verwalteten Azure-Anwendungen mit dem Netzwerkfunktionsmanager anbieten, ermöglichen Ihnen eine optimale Benutzererfahrung, bei der Sie eine verwaltete Anwendung bereitstellen können, die an eine vorhandene Netzwerkfunktionsmanager-Geräteressource angefügt ist. Wenn Sie die vom Partner verwaltete Anwendung im Azure-Portal bereitstellen, müssen Sie eine dem Azure-Benutzer zugewiesene verwaltete Identitätsressource bereitstellen, die Zugriff auf die Network Function Manager-Geräteressource hat. Durch die benutzerseitig zugewiesene verwaltete Identität erhalten der Ressourcenanbieter der verwalteten Anwendung und der Herausgeber der Netzwerkfunktion die notwendigen Berechtigungen für die Netzwerkfunktionsmanager-Geräteressource, die außerhalb der verwalteten Ressourcengruppe bereitgestellt wird. Weitere Informationen finden Sie unter [Verwalten einer benutzerseitig zugewiesenen verwalteten Identität im Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

So erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität für die Bereitstellung von Netzwerkfunktionen:

1. Erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität und ordnen Sie diese einer benutzerdefinierten Rolle mit Berechtigungen für Microsoft.HybridNetwork/devices/join/action zu. Weitere Informationen finden Sie unter [Verwalten einer benutzerseitig zugewiesenen verwalteten Identität im Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Geben Sie diese verwaltete Identität an, wenn Sie die verwaltete Anwendung eines Partners im Azure-Portal erstellen. Weitere Informationen finden Sie unter [Zuweisen des Zugriffs einer verwalteten Identität auf eine Ressource über das Azure-Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den [Fragen und Antworten zum Netzwerkfunktionsmanager](faq.md).
