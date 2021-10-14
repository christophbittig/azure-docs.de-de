---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/20/2021
ms.author: tomfitz
ms.openlocfilehash: 0375a43e2cee9c973a957d96a9f74353d9f1ac2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910446"
---
Mithilfe privater Verbindungen können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-Dienste zugreifen. Wenn Sie private Verbindungen mit Azure Resource Manager-Vorgängen kombinieren, blockieren Sie die Verwaltung von Ressourcen durch Benutzer, die sich nicht am spezifischen Endpunkt befinden. Wenn ein böswilliger Benutzer Anmeldeinformationen für ein Konto in Ihrem Abonnement erlangt, kann dieser Benutzer die Ressourcen nur dann verwalten, wenn er sich an dem spezifischen Endpunkt befindet.

Eine private Verbindung bietet die folgende Sicherheitsvorteile:

* **Privater Zugriff**: Benutzer können Ressourcen aus einem privaten Netzwerk über einen privaten Endpunkt verwalten.
* **Datenexfiltration**: Benutzern wird der Zugriff auf Ressourcen verweigert, die nicht im Bereich enthalten sind.

> [!NOTE]
> Azure Kubernetes Service (AKS) unterstützt derzeit die Implementierung des privaten ARM-Endpunkts nicht.

## <a name="understand-architecture"></a>Informationen zur Architektur

Für dieses Release können Sie den Verwaltungszugriff für private Verbindungen nur auf der Ebene der [Stammverwaltungsgruppe](../articles/governance/management-groups/overview.md) anwenden. Diese Einschränkung bedeutet, dass der Zugriff über private Verbindungen mandantenweit angewendet wird.

Es gibt zwei Ressourcentypen, die Sie beim Implementieren der Verwaltung über eine private Verbindung verwenden.

* Private Ressourcenmanagementverbindung (Microsoft.Authorization/resourceManagementPrivateLinks)
* Zuordnung der privaten Verbindung (Microsoft.Authorization/privateLinkAssociations)

Die folgende Abbildung zeigt, wie Sie eine Lösung erstellen, die den Zugriff auf die Ressourcenverwaltung einschränkt.

:::image type="content" source="./media/resource-manager-create-rmpl/resource-management-private-link.svg" alt-text="Diagramm mit privater Ressourcenmanagementverbindung":::

Die Zuordnung der privaten Verbindung erweitert die Stammverwaltungsgruppe. Die Zuordnung der privaten Verbindung und die privaten Endpunkte verweisen auf die private Ressourcenmanagementverbindung.

## <a name="workflow"></a>Workflow

Um eine private Verbindung für Ressourcen einzurichten, führen Sie die folgenden Schritte durch. Die Schritte werden weiter unten in diesem Artikel ausführlicher beschrieben.

1. Erstellen Sie die private Ressourcenmanagementverbindung.
1. Erstellen Sie eine Zuordnung der privaten Verbindung. Die Zuordnung der privaten Verbindung erweitert die Stammverwaltungsgruppe. Außerdem verweist sie auf die Ressourcen-ID für die private Ressourcenmanagementverbindung.
1. Fügen Sie einen privaten Endpunkt hinzu, der auf die private Ressourcenmanagementverbindung verweist.

Nach Abschluss dieser Schritte können Sie Azure-Ressourcen innerhalb der Hierarchie des Bereichs verwalten. Sie verwenden einen privaten Endpunkt, der mit dem Subnetz verbunden ist.

Sie können den Zugriff auf die private Verbindung überwachen. Weitere Informationen finden Sie unter [Protokollierung und Überwachung](../articles/private-link/private-link-overview.md#logging-and-monitoring).

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Um die private Verbindung für die Ressourcenverwaltung einzurichten, benötigen Sie den folgenden Zugriff:

* Abonnementbesitzer. Dieser Zugriff ist erforderlich, um eine private Ressourcenmanagementverbindung zu erstellen.
* Besitzer oder Mitwirkender in der Stammverwaltungsgruppe. Dieser Zugriff ist erforderlich, um die Zuordnungsressource für die private Verbindung zu erstellen.
* Der globale Administrator für die Azure Active Directory-Instanz ist nicht automatisch zum Zuweisen von Rollen in der Stammverwaltungsgruppe berechtigt. Um das Erstellen privater Ressourcenmanagementverbindungen zu ermöglichen, muss der globale Administrator über die Berechtigung zum Lesen der Stammverwaltungsgruppe und zum [Erweitern der Zugriffsrechte](../articles/role-based-access-control/elevate-access-global-admin.md) verfügen, damit er für alle Abonnements und Verwaltungsgruppen im Mandanten die Berechtigung eines Benutzerzugriffsadministrators besitzt. Nachdem der globale Administrator die Berechtigung „Benutzerzugriffsadministrator“ erhalten hat, muss er dem Benutzer, der die Zuordnung der privaten Verbindung erstellt, die Berechtigung „Besitzer“ oder „Mitwirkender“ für die Stammverwaltungsgruppe erteilen.