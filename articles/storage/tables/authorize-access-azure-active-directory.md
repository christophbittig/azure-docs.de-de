---
title: Autorisieren des Tabellenzugriffs mithilfe von Active Directory (Vorschauversion)
titleSuffix: Azure Storage
description: Autorisieren Sie den Zugriff auf Azure-Tabellen mithilfe von Azure Active Directory (Azure AD, Vorschauversion). Weisen Sie Azure-Rollen für Zugriffsrechte zu. Greifen Sie mit einem Azure AD-Konto auf Daten zu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 71ce588ea617a0322da4ffc7cf9f2626e4abb609
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733998"
---
# <a name="authorize-access-to-tables-using-azure-active-directory-preview"></a>Autorisieren des Tabellenzugriffs mithilfe von Azure Active Directory (Vorschauversion)

Azure Storage unterstützt mithilfe von Azure Active Directory (Azure AD) das Autorisieren von Anforderungen für Tabellendaten (Vorschauversion). Mit Azure AD können Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zum Gewähren von Berechtigungen für einen Sicherheitsprinzipal verwenden, bei dem es sich um einen Benutzer, eine Gruppe oder einen Anwendungsdienstprinzipal handeln kann. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, um ein OAuth 2.0-Token zurückzugeben. Das Token kann anschließend zum Autorisieren einer Anforderung an den Tabellendienst verwendet werden.

Die Autorisierung von Anforderungen für Azure Storage mit Azure AD bietet über die Autorisierung mit einem gemeinsam verwalteten Schlüssel überlegene Sicherheit und Benutzerfreundlichkeit. Microsoft empfiehlt, nach Möglichkeit die Azure AD-Autorisierung mit Ihren Tabellenanwendungen zu verwenden, um den Zugriff mit minimal erforderlichen Berechtigungen sicherzustellen.

Die Autorisierung mit Azure AD ist für alle universellen Speicherkonten sowie in allen öffentlichen Regionen und nationalen Clouds verfügbar. Die Azure AD-Autorisierung wird nur für Speicherkonten unterstützt, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden.

> [!IMPORTANT]
> Die Autorisierung mit Azure AD für Tabellen befindet sich derzeit in der **Vorschauphase**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="overview-of-azure-ad-for-tables"></a>Übersicht über Azure AD für Tabellen

Wenn ein Sicherheitsprinzipal (ein Benutzer, eine Gruppe oder eine Anwendung) versucht, auf eine Tabellenressource zuzugreifen, muss die Anforderung autorisiert werden. Mit Azure AD ist der Zugriff auf eine Ressource ein zweistufiger Prozess. Zunächst wird die Identität des Sicherheitsprinzipals authentifiziert, und ein OAuth 2.0-Token wird zurückgegeben. Anschließend wird das Token als Teil einer Anforderung an den Tabellendienst übergeben und von diesem verwendet, um den Zugriff auf die angegebene Ressource zu autorisieren.

Für den Authentifizierungsschritt ist es erforderlich, dass eine Anwendung zur Laufzeit ein OAuth 2.0-Zugriffstoken anfordert. Wenn eine Anwendung in einer Azure-Entität, z. B. einer Azure-VM, einer VM-Skalierungsgruppe oder einer Azure Functions-App, ausgeführt wird, kann der Zugriff auf Tabellen über eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) erfolgen. Informationen zur Autorisierung von Anforderungen über eine verwaltete Identität finden Sie unter [Autorisieren des Zugriffs auf Tabellen mit verwalteten Identitäten für Azure-Ressourcen](../common/storage-auth-aad-msi.md).

Der Autorisierungsschritt erfordert, dass dem Sicherheitsprinzipal mindestens eine Azure-Rolle zugewiesen wird. Azure Storage umfasst Azure-Rollen mit gängigen Gruppen von Berechtigungen für Tabellendaten. Die Berechtigungen dieses Sicherheitsprinzipals sind durch die Rollen vorgegeben, die dem Prinzipal zugewiesen sind. Weitere Informationen zum Zuweisen von Azure-Rollen für den Tabellenzugriff finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Tabellendaten](assign-azure-role-data-access.md).

Für native Anwendungen und Webanwendungen, die Anforderungen an den Azure Table Storage-Dienst senden, kann die Autorisierung auch mit Azure AD erfolgen. Informationen zum Anfordern eines Zugriffstokens und seiner Verwendung zum Autorisieren von Anforderungen finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Zuweisen von Azure-Rollen für Zugriffsrechte

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definiert eine Reihe von in Azure integrierten Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Tabellendaten. Außerdem können Sie benutzerdefinierte Rollen für den Zugriff auf Tabellendaten definieren.

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

### <a name="resource-scope"></a>Ressourcenumfang

Bevor Sie einem Sicherheitsprinzipal eine Azure RBAC-Rolle zuweisen, legen Sie den Zugriffsbereich fest, den der Sicherheitsprinzipal haben soll. Es hat sich als am besten bewährt, stets nur den kleinstmöglichen Umfang an Zugriffsrechten zu gewähren. Azure RBAC-Rollen, die in einem umfassenderen Bereich definiert sind, werden von den darunterliegenden Ressourcen geerbt.

Sie können den Zugriff auf Azure-Tabellenressourcen auf den folgenden Ebenen einschränken, beginnend mit dem kleinstmöglichen Bereich:

- **Individuelle Tabelle:** In diesem Bereich gilt eine Rollenzuweisung nur für die angegebene Tabelle.
- **Das Speicherkonto**. In diesem Bereich gilt eine Rollenzuweisung für alle Tabellen im Konto.
- **Die Ressourcengruppe**. Bei diesem Bereich gilt eine Rollenzuweisung für alle Tabellen in allen Speicherkonten der Ressourcengruppe.
- **Das Abonnement**. Bei diesem Bereich gilt eine Rollenzuweisung für alle Tabellen in allen Speicherkonten in allen Ressourcengruppen des Abonnements.
- **Eine Verwaltungsgruppe**. Bei diesem Bereich gilt eine Rollenzuweisung für alle Tabellen in allen Speicherkonten in allen Ressourcengruppen in allen Abonnements in der Ressourcengruppe.

Weitere Informationen zum Bereich für Azure RBAC-Rollenzuweisungen finden Sie unter [Grundlegendes zum Bereich für Azure RBAC](../../role-based-access-control/scope-overview.md).

### <a name="azure-built-in-roles-for-tables"></a>In Azure integrierte Rollen für Tabellen

Azure RBAC beinhaltet Rollen zum Autorisieren des Zugriffs auf Tabellendaten mit Azure AD und OAuth. Die folgenden integrierten Rollen besitzen Berechtigungen für Tabellen in Azure Storage:

- [Mitwirkender an Storage-Tabellendaten](../../role-based-access-control/built-in-roles.md#storage-table-data-contributor): Diese Rolle erteilt Lese-, Schreib- und Löschberechtigungen für Table Storage-Ressourcen.
- [Storage-Tabellendatenleser](../../role-based-access-control/built-in-roles.md#storage-table-data-reader): Diese Rolle kann schreibgeschützt auf Table Storage-Ressourcen zugreifen.

Informationen zum Zuweisen einer integrierten Azure-Rolle zu einem Sicherheitsprinzipal finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Tabellendaten](assign-azure-role-data-access.md). Informationen zum Auflisten von Azure RBAC-Rollen und deren Berechtigungen finden Sie unter [Auflisten von Azure-Rollendefinitionen](../../role-based-access-control/role-definitions-list.md).

Weitere Informationen dazu, wie integrierte Rollen für Azure Storage definiert sind, finden Sie unter [Grundlegendes zu Rollendefinitionen](../../role-based-access-control/role-definitions.md#management-and-data-operations). Informationen zum Erstellen von benutzerdefinierten Azure-Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md).

Nur Rollen, die explizit für den Datenzugriff vorgesehen sind, ermöglichen einem Sicherheitsprinzipal den Zugriff auf Tabellendaten. Integrierte Rollen wie **Besitzer**, **Mitwirkender** und **Speicherkontomitwirkender** gestatten einem Sicherheitsprinzipal die Verwaltung eines Speicherkontos, gewähren aber keinen Zugriff auf die Tabellendaten in diesem Konto über Azure AD. Wenn eine Rolle jedoch **Microsoft.Storage/storageAccounts/listKeys/action** enthält, kann ein Benutzer, dem diese Rolle zugewiesen ist, über die Autorisierung mit gemeinsam verwendetem Schlüssel mit den Kontozugriffsschlüsseln auf Daten im Speicherkonto zugreifen.

Ausführliche Informationen zu integrierten Azure-Rollen für Azure Storage für die Datendienste und den Verwaltungsdienst finden Sie im Artikel [In Azure integrierte Rollen für Azure RBAC](../../role-based-access-control/built-in-roles.md#storage) im Abschnitt **Storage**. Informationen zu den verschiedenen Typen von Rollen, die Berechtigungen in Azure bereitstellen, finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Die Verteilung von Azure-Rollenzuweisungen kann bis zu 30 Minuten dauern.

### <a name="access-permissions-for-data-operations"></a>Zugriffsberechtigungen für Datenvorgänge

Einzelheiten zu den Berechtigungen, die für spezifische Vorgänge des Table Storage-Diensts erforderlich sind, finden Sie unter [Berechtigungen für das Aufrufen von Datenvorgängen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations).

## <a name="next-steps"></a>Nächste Schritte

- [Autorisieren des Zugriffs auf Daten in Azure Storage](../common/authorize-data-access.md)
- [Zuweisen einer Azure-Rolle für den Zugriff auf Tabellendaten](assign-azure-role-data-access.md)