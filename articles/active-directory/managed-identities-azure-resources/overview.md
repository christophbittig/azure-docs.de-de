---
title: Verwaltete Identitäten für Azure-Ressourcen
description: Eine Übersicht über verwaltete Identitäten für Azure-Ressourcen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91728bfee6b8e303de2e7199e703a8acaa5139d8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124773586"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Was sind verwaltete Identitäten für Azure-Ressourcen?

Die Verwaltung von Geheimnissen und Anmeldeinformationen, die zum Absichern der Kommunikation zwischen verschiedenen Komponenten einer Lösung verwendet werden. Dank verwalteter Identitäten müssen Entwickler keine Anmeldeinformationen mehr verwalten. Verwaltete Identitäten stellen eine Identität bereit, die Anwendungen beim Herstellen einer Verbindung mit Ressourcen verwenden, die Azure Active Directory-Authentifizierung (Azure AD) unterstützen. Anwendungen können die verwaltete Identität verwenden, um Azure AD-Token abzurufen. Beispielsweise kann eine Anwendung eine verwaltete Identität verwenden, um auf Ressourcen wie [Azure Key Vault](../../key-vault/general/overview.md) zuzugreifen, in denen Entwickler Anmeldeinformationen auf sichere Weise speichern, oder um auf Speicherkonten zuzugreifen.

Sehen Sie sich an, wie Sie verwaltete Identitäten verwenden können:</br>

> [!VIDEO https://channel9.msdn.com/Shows/On-NET/Using-Azure-Managed-identities/player?format=ny]



Nachstehend sind einige Vorteile der Verwendung von verwalteten Identitäten beschrieben:

- Sie brauchen keine Anmeldeinformationen zu verwalten. Sie haben nicht einmal Zugriff auf die Anmeldeinformationen.
- Mit verwalteten Identitäten können Sie die Authentifizierung gegenüber jeder Ressource (einschließlich Ihrer eigenen Anwendungen) durchführen, die [Azure Active Directory-Authentifizierung](../authentication/overview-authentication.md) unterstützt.
- Die Nutzung von verwalteten Identitäten verursacht keine zusätzlichen Kosten.

> [!NOTE]
> „Verwaltete Identitäten für Azure-Ressourcen“ ist der neue Name für den Dienst, der früher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

## <a name="managed-identity-types"></a>Arten von verwalteten Identitäten

Es gibt zwei Arten von verwalteten Identitäten:

- **Systemseitig zugewiesen**: Bei einigen Azure-Diensten können Sie eine verwaltete Identität direkt in einer Dienstinstanz aktivieren. Wenn Sie eine systemseitig zugewiesene verwaltete Identität aktivieren, wird in Azure AD eine Identität erstellt, die an den Lebenszyklus der jeweiligen Dienstinstanz gebunden ist. Daher löscht Azure automatisch die Identität, wenn die Ressource gelöscht wird. Entwurfsbedingt kann nur diese Azure-Ressource diese Identität zum Anfordern von Token von Azure AD verwenden.
- **Benutzerseitig zugewiesen**: Sie können eine verwaltete Identität auch als eigenständige Azure-Ressource erstellen. Sie können eine [benutzerseitig zugewiesene verwaltete Identität erstellen](how-to-manage-ua-identity-portal.md) und diese einer oder mehreren Instanzen eines Azure-Diensts zuweisen. Bei benutzerseitig zugewiesenen verwalteten Identitäten wird die Identität getrennt von den Ressourcen verwaltet, für die sie verwendet wird. </br></br>


Die nachstehende Tabelle verdeutlicht die Unterschiede zwischen den beiden Arten von verwalteten Identitäten.

|  Eigenschaft    | Systemseitig zugewiesene verwaltete Identität | Benutzerseitig zugewiesene verwaltete Identität |
|------|----------------------------------|--------------------------------|
| Erstellung |  Als Teil einer Azure-Ressource (etwa eines virtuellen Azure-Computers oder einer Azure App Service-Instanz) | Als eigenständige Azure-Ressource |
| Lebenszyklus | Gemeinsamer Lebenszyklus mit der Azure-Ressource, für die die verwaltete Identität erstellt wurde. <br/> Wenn die übergeordnete Ressource gelöscht wird, wird auch die verwaltete Identität gelöscht. | Unabhängiger Lebenszyklus. <br/> Muss explizit gelöscht werden. |
| Gemeinsame Nutzung durch mehrere Azure-Ressourcen | Keine gemeinsame Nutzung möglich. <br/> Kann nur einer einzelnen Azure-Ressource zugeordnet werden. | Gemeinsame Nutzung möglich. <br/> Die gleiche benutzerseitig zugewiesene verwaltete Identität kann mehreren Azure-Ressourcen zugeordnet werden. |
| Gängige Anwendungsfälle | Workloads innerhalb einer einzelnen Azure-Ressource. <br/> Workloads, für die unabhängige Identitäten erforderlich sind. <br/> Beispielsweise eine Anwendung, die auf einem einzelnen virtuellen Computer ausgeführt wird. | Workloads, die auf mehrere Ressourcen ausgeführt werden und sich eine einzelne Identität teilen können. <br/> Workloads, die im Rahmen eines Bereitstellungsablaufs vorab für eine sichere Ressource autorisiert werden müssen. <br/> Workloads, bei denen Ressourcen häufig recycelt werden, die Berechtigungen aber konsistent bleiben sollen. <br/> Beispielsweise eine Workload, bei der mehrere virtuelle Computer auf die gleiche Ressource zugreifen müssen. |

> [!IMPORTANT]
> Ungeachtet der ausgewählten Identitätsart ist eine verwaltete Identität ein Dienstprinzipal der besonderen Art, der nur zusammen mit Azure-Ressourcen verwendet werden kann. Wenn die verwaltete Identität gelöscht wird, wird automatisch auch der entsprechende Dienstprinzipal entfernt.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Wie kann ich verwaltete Identitäten für Azure-Ressourcen verwenden?

![Einige Beispiele für die Verwendung von verwalteten Identitäten durch Entwickler für den Zugriff auf Ressourcen über deren Code ohne Verwaltung von Authentifizierungsinformationen](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>Welche Azure-Dienste unterstützen das Feature?<a name="which-azure-services-support-managed-identity"></a>

Verwaltete Identitäten für Azure-Ressourcen können zur Authentifizierung bei Diensten verwendet werden, die die Azure AD-Authentifizierung unterstützen. Eine Liste der Azure-Dienste, die die Funktion für verwaltete Identitäten für Azure-Ressourcen unterstützen, finden Sie unter [Services that support managed identities for Azure resources](./services-support-managed-identities.md) (Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen).

## <a name="which-operations-can-i-perform-using-managed-identities"></a>Welche Vorgänge kann ich mit verwalteten Identitäten ausführen?

Ressourcen, die systemseitig zugewiesene verwaltete Identitäten unterstützen, ermöglichen Folgendes:

- Aktivieren oder Deaktivieren verwalteter Identitäten auf Ressourcenebene
- [Erteilen von Berechtigungen](howto-assign-access-portal.md) mithilfe von RBAC-Rollen
- Anzeigen von Vorgängen zum Erstellen, Lesen, Aktualisieren und Löschen (CRUD-Vorgänge) in [Azure-Aktivitätsprotokollen](../../azure-monitor/essentials/activity-log.md)
- Anzeigen von Anmeldeaktivitäten in Azure AD-[Anmeldeprotokollen](../reports-monitoring/concept-sign-ins.md)

Falls Sie stattdessen eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten:

- Sie können die Identitäten [erstellen, lesen, aktualisieren und löschen](how-to-manage-ua-identity-portal.md).
- Sie können RBAC-Rollenzuweisungen zum [Erteilen von Berechtigungen](howto-assign-access-portal.md) verwenden.
- Benutzerseitig zugewiesene verwaltete Identitäten können für mehrere Ressourcen verwendet werden.
- CRUD-Vorgänge können in [Azure-Aktivitätsprotokollen](../../azure-monitor/essentials/activity-log.md) überprüft werden.
- Zeigen Sie Anmeldeaktivitäten in Azure AD-[Anmeldeprotokollen](../reports-monitoring/concept-sign-ins.md) an.

Vorgänge für verwaltete Identitäten können mithilfe einer ARM-Vorlage (Azure Resource Manager), über das Azure-Portal, die Azure CLI, PowerShell und REST-APIs ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

* [Use a Windows VM system-assigned managed identity to access Resource Manager](tutorial-windows-vm-access-arm.md) (Verwenden der systemseitig zugewiesenen verwalteten Identität einer Windows-VM für den Zugriff auf Resource Manager)
* [Use a Linux VM system-assigned managed identity to access Resource Manager](tutorial-linux-vm-access-arm.md) (Verwenden der systemseitig zugewiesenen verwalteten Identität einer Linux-VM für den Zugriff auf Resource Manager)
* [Verwenden verwalteter Identitäten für App Service und Azure Functions](../../app-service/overview-managed-identity.md)
* [Verwenden von verwalteten Identitäten mit Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Implementieren verwalteter Identitäten für Microsoft Azure-Ressourcen](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).
