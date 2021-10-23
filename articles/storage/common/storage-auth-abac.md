---
title: Autorisieren des Zugriffs auf Blobs mithilfe von Bedingungen für die Azure-Rollenzuweisung (Vorschau)
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie mithilfe von Bedingungen für die Azure-Rollenzuweisung und Azure ABAC (Attribute-Based Access Control, attributbasierte Zugriffssteuerung) den Zugriff auf Blobs autorisieren können. Definieren Sie Bedingungen für Rollenzuweisungen mithilfe von Speicherattributen.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 0aeb8766822bbfe19dfdd70ed242e7176dd44117
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062781"
---
# <a name="authorize-access-to-blobs-using-azure-role-assignment-conditions-preview"></a>Autorisieren des Zugriffs auf Blobs mithilfe von Bedingungen für die Azure-Rollenzuweisung (Vorschau)

Die attributbasierte Zugriffssteuerung (Attribute-Based Access Control, ABAC) ist eine Autorisierungsstrategie, mit der der Zugriff anhand von Attributen definiert wird, die Sicherheitsprinzipalen, Ressourcen, Anforderungen und der Umgebung zugeordnet sind. Azure ABAC baut auf der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) auf, indem [Azure-Rollenzuweisungen Bedingungen](../../role-based-access-control/conditions-overview.md) im vorhandenen Identitäts- und Zugriffsverwaltungssystem (Identity and Access Management, IAM) hinzugefügt werden. Diese Vorschau bietet Unterstützung für Rollenzuweisungsbedingungen für Blobs und Data Lake Storage Gen2. Sie ermöglicht es Ihnen, Rollenzuweisungsbedingungen basierend auf Ressourcen- und Anforderungsattributen zu autorisieren.

> [!IMPORTANT]
> Azure ABAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-conditions-in-azure-storage"></a>Übersicht über Bedingungen in Azure Storage

Azure Storage ermöglicht die [Verwendung von Azure Active Directory](authorize-data-access.md) (Azure AD) zum Autorisieren von Anforderungen an Blob-, Warteschlangen- und Tabellenressourcen. Azure AD autorisiert Zugriffsrechte auf gesicherte Ressourcen mithilfe von Azure RBAC. Azure Storage definiert eine Reihe von in Azure [integrierten Rollen](../../role-based-access-control/built-in-roles.md#storage) mit allgemeinen Berechtigungssätzen für den Zugriff auf Blob- oder Warteschlangendaten. Sie können auch benutzerdefinierte Rollen mit einem ausgewählten Berechtigungssatz definieren. Azure Storage unterstützt Rollenzuweisungen für Speicherkonten oder Blobcontainer.

In einigen Fällen müssen Sie jedoch möglicherweise einen feineren Zugriff auf Ressourcen aktivieren oder die Hunderte von Rollenzuweisungen für eine Speicherressource vereinfachen. Sie können [Bedingungen für Rollenzuweisungen](../../role-based-access-control/conditions-overview.md) für [DataActions](../../role-based-access-control/role-definitions.md#dataactions) konfigurieren, um diese Ziele zu erreichen. Sie können Bedingungen bei einer [benutzerdefinierten Rolle](../../role-based-access-control/custom-roles.md) verwenden oder integrierte Rollen auswählen. Beachten Sie, dass Bedingungen für [Verwaltungsaktionen](../../role-based-access-control/role-definitions.md#actions) über den [Speicherressourcenanbieter](/rest/api/storagerp) nicht unterstützt werden.

Bedingungen in Azure Storage werden für Blobs unterstützt. Sie können Bedingungen bei Konten verwenden, für die das Feature [hierarchischer Namespace](../blobs/data-lake-storage-namespace.md) (HNS) aktiviert wurde. Bedingungen werden bei Warteschlangen-, Tabellen- oder Dateiressourcen in Azure Storage zurzeit nicht unterstützt.

## <a name="supported-attributes-and-operations"></a>Unterstützte Attribute und Vorgänge

In dieser Vorschau können Sie integrierten Rollen oder benutzerdefinierten Rollen Bedingungen hinzufügen. Mithilfe von benutzerdefinierten Rollen können Sie Ihren Benutzern nur die wesentlichen Berechtigungen oder Datenaktionen gewähren. Die integrierten Rollen, die in dieser Vorschau unterstützt werden, umfassen [Leser von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader), [Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) und [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner).

Wenn Sie mit Bedingungen arbeiten, die auf [Blobindextags](../blobs/storage-manage-find-blobs.md) basieren, sollten Sie den *Besitzer von Speicherblobdaten* verwenden, da Berechtigungen für Tagvorgänge in dieser Rolle enthalten sind.

> [!NOTE]
> Blobindextags werden bei Data Lake Storage Gen2-Speicherkonten, die einen hierarchischen Namespace verwenden, nicht unterstützt. Sie sollten keine Rollenzuweisungsbedingungen mithilfe von Indextags für Speicherkonten autorisieren, bei denen HNS aktiviert wurde.

Das [Format für Azure-Rollenzuweisungsbedingungen](../../role-based-access-control/conditions-format.md) ermöglicht die Verwendung von `@Resource`- oder `@Request`-Attributen in den Bedingungen. Ein `@Resource`-Attribut bezieht sich auf ein vorhandenes Attribut einer Speicherressource, auf die zugegriffen wird, z. B. ein Speicherkonto, ein Container oder ein Blob. Ein `@Request`-Attribut bezieht sich auf ein Attribut, das in einer Speichervorgangsanforderung enthalten ist.

Die vollständige Liste der Attribute, die bei jeder „DataAction“ unterstützt werden, finden Sie unter [Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)](storage-auth-abac-attributes.md).

## <a name="see-also"></a>Siehe auch

- [Sicherheitsüberlegungen für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)](storage-auth-abac-security.md)
- [Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)](storage-auth-abac-attributes.md)
- [Was ist die attributbasierte Zugriffssteuerung in Azure (Azure Attribute-Based Access Control, Azure ABAC)? (Vorschau)](../../role-based-access-control/conditions-overview.md)