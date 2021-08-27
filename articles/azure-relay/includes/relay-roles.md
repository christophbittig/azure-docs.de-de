---
author: spelluru
ms.service: service-bus-relay
ms.topic: include
ms.date: 07/19/2021
ms.author: spelluru
ms.openlocfilehash: e4fd2d455a294b247353e4983c379066d704c87a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801251"
---
## <a name="overview"></a>Übersicht
Wenn ein Sicherheitsprinzipal (ein Benutzer, eine Gruppe, eine Anwendung) versucht, auf eine Relay-Entität zuzugreifen, muss die Anforderung autorisiert werden. Mit Azure AD ist der Zugriff auf eine Ressource ein zweistufiger Prozess.

1. Zunächst wird die Identität des Sicherheitsprinzipals **authentifiziert** und ein OAuth 2.0-Token zurückgegeben. Der Ressourcenname zum Anfordern eines Tokens ist `https://relay.azure.net`. Wenn eine Anwendung in einer Azure-Entität, z.B. einem virtuellen Azure-Computer, einer VM-Skalierungsgruppe oder einer Azure Functions-App, ausgeführt wird, kann der Zugriff auf die Ressourcen über eine verwaltete Identität erfolgen.
2. Anschließend wird das Token als Teil einer Anforderung an den Relay-Dienst übergeben, um den Zugriff auf die angegebene Ressource (Hybridverbindungen, WCF-Relays) zu **autorisieren**. Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Azure Relay bietet eine Reihe integrierter Rollen mit allgemeinen Berechtigungen für den Zugriff auf Relay-Entitäten. Sie können auch benutzerdefinierte Rollen für den Zugriff auf die Daten definieren. Eine Liste der von Azure Relay unterstützten integrierten Rollen finden Sie unter [Integrierte Azure-Rollen für Azure Relay](#azure-built-in-roles-for-azure-relay). Native Anwendungen und Webanwendungen, die Anforderungen an Relay senden, können die Autorisierung ebenfalls mit Azure AD durchführen.  

## <a name="azure-built-in-roles-for-azure-relay"></a>Integrierte Azure-Rollen für Azure Relay
Bei Azure Relay ist die Verwaltung der Namespaces und aller zugehörigen Ressourcen über das Azure-Portal und die Azure-Ressourcenverwaltungs-API bereits durch das Azure RBAC-Modell geschützt. Azure stellt die folgenden integrierten Azure-Rollen zum Autorisieren des Zugriffs auf einen Relay-Namespace bereit:

| Rolle | BESCHREIBUNG | 
| ---- | ----------- | 
| [Azure Relay-Besitzer](../../role-based-access-control/built-in-roles.md#azure-relay-owner) | Mit dieser Rolle können Sie **Voll** zugriff auf Azure Relay-Ressourcen gewähren. |
| [Azure Relay-Listener](../../role-based-access-control/built-in-roles.md#azure-relay-listener) | Mit dieser Rolle können Sie **Lausch- und Entitätslese-** zugriff auf Azure Relay-Ressourcen gewähren. |
| [Azure Relay-Absender](../../role-based-access-control/built-in-roles.md#azure-relay-sender) | Mit dieser Rolle können Sie **Sende- und Entitätslese-** zugriff auf Azure Relay-Ressourcen gewähren. | 

## <a name="resource-scope"></a>Ressourcenumfang
Bevor Sie einem Sicherheitsprinzipal eine Azure-Rolle zuweisen, legen Sie den Zugriffsbereich fest, den der Sicherheitsprinzipal haben soll. Es hat sich als am besten bewährt, stets nur den kleinstmöglichen Umfang an Zugriffsrechten zu gewähren.

In der folgenden Liste werden die Ebenen beschrieben, auf denen Sie den Zugriff auf Azure Relay-Ressourcen einschränken können, beginnend mit dem kleinstmöglichen Umfang:

- **Relay-Entitäten**: Die Rollenzuweisung gilt für eine bestimmte Relay-Entität, z. B. eine Hybridverbindung oder ein WCF-Relay.
- **Relay-Namespace**: Die Rollenzuweisung gilt für alle Relay-Entitäten unter dem Namespace.
- **Ressourcengruppe**: Die Rollenzuweisung gilt für alle Relay-Ressourcen unter der Ressourcengruppe.
- **Abonnement**: Die Rollenzuweisung gilt für alle Relay-Ressourcen in allen Ressourcengruppen im Abonnement.

> [!NOTE]
> Denken Sie daran, dass die Weitergabe von Azure-Rollenzuweisungen bis zu fünf Minuten dauern kann. Weitere Informationen dazu, wie integrierte Rollen definiert sind, finden Sie unter [Grundlegendes zu Rollendefinitionen](../../role-based-access-control/role-definitions.md#management-and-data-operations). Informationen zum Erstellen von benutzerdefinierten Azure-Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md). 

