---
title: Autorisieren des Zugriffs auf Warteschlangen mithilfe von Active Directory
titleSuffix: Azure Storage
description: Autorisieren Sie den Zugriff auf Azure-Warteschlangen mithilfe von Azure Active Directory (Azure AD). Weisen Sie Azure-Rollen für Zugriffsrechte zu. Greifen Sie mit einem Azure AD-Konto auf Daten zu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: bd9224537be7559a325b30a71fb72eab07a71bd0
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855363"
---
# <a name="authorize-access-to-queues-using-azure-active-directory"></a>Autorisieren des Zugriffs auf Warteschlangen mithilfe von Azure Active Directory

Azure Storage unterstützt mithilfe von Azure Active Directory (Azure AD) das Autorisieren von Anforderungen an Warteschlangendaten. Mit Azure AD können Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zum Gewähren von Berechtigungen für einen Sicherheitsprinzipal verwenden, bei dem es sich um einen Benutzer, eine Gruppe oder einen Anwendungsdienstprinzipal handeln kann. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, um ein OAuth 2.0-Token zurückzugeben. Das Token kann anschließend zum Autorisieren einer Anforderung an den Warteschlangendienst verwendet werden.

Die Autorisierung von Anforderungen für Azure Storage mit Azure AD bietet über die Autorisierung mit einem gemeinsam verwalteten Schlüssel überlegene Sicherheit und Benutzerfreundlichkeit. Microsoft empfiehlt, nach Möglichkeit die Azure AD-Autorisierung mit Ihren Warteschlangenanwendungen zu verwenden, um den Zugriff mit minimal erforderlichen Berechtigungen sicherzustellen.

Die Autorisierung mit Azure AD ist für alle universellen Speicherkonten sowie in allen öffentlichen Regionen und nationalen Clouds verfügbar. Die Azure AD-Autorisierung wird nur für Speicherkonten unterstützt, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden.

## <a name="overview-of-azure-ad-for-queues"></a>Übersicht über Azure AD für Warteschlangen

Wenn ein Sicherheitsprinzipal (ein Benutzer, eine Gruppe oder eine Anwendung) versucht, auf eine Warteschlangenressource zuzugreifen, muss die Anforderung autorisiert werden. Mit Azure AD ist der Zugriff auf eine Ressource ein zweistufiger Prozess. Zunächst wird die Identität des Sicherheitsprinzipals authentifiziert, und ein OAuth 2.0-Token wird zurückgegeben. Anschließend wird das Token als Teil einer Anforderung an den Warteschlangendienst übergeben und vom Dienst verwendet, um den Zugriff auf die angegebene Ressource zu autorisieren.

Für den Authentifizierungsschritt ist es erforderlich, dass eine Anwendung zur Laufzeit ein OAuth 2.0-Zugriffstoken anfordert. Wenn eine Anwendung in einer Azure-Entität, z. B. auf einem virtuellen Azure-Computer, in einer VM-Skalierungsgruppe oder einer Azure Functions-App, ausgeführt wird, kann der Zugriff auf Warteschlangen über eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) erfolgen. Informationen zur Autorisierung von Anforderungen über eine verwaltete Identität finden Sie unter [Autorisieren des Zugriffs auf Warteschlangendaten mit verwalteten Identitäten für Azure-Ressourcen](authorize-managed-identity.md).

Der Autorisierungsschritt erfordert, dass dem Sicherheitsprinzipal mindestens eine Azure-Rolle zugewiesen wird. Azure Storage umfasst Azure-Rollen mit gängigen Gruppen von Berechtigungen für Warteschlangendaten. Die Berechtigungen dieses Sicherheitsprinzipals sind durch die Rollen vorgegeben, die dem Prinzipal zugewiesen sind. Weitere Informationen zum Zuweisen von Azure-Rollen für Warteschlangenzugriff finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Warteschlangendaten](../queues/assign-azure-role-data-access.md).

Für native Anwendungen und Webanwendungen, die Anforderungen an den Azure-Warteschlangendienst senden, kann die Autorisierung auch mit Azure AD erfolgen. Informationen zum Anfordern eines Zugriffstokens und seiner Verwendung zum Autorisieren von Anforderungen finden Sie unter [Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung](../common/storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Zuweisen von Azure-Rollen für Zugriffsrechte

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definiert eine Reihe von in Azure integrierten Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Warteschlangendaten. Außerdem können Sie benutzerdefinierte Rollen für den Zugriff auf Warteschlangendaten definieren.

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

### <a name="resource-scope"></a>Ressourcenumfang

Bevor Sie einem Sicherheitsprinzipal eine Azure RBAC-Rolle zuweisen, legen Sie den Zugriffsbereich fest, den der Sicherheitsprinzipal haben soll. Es hat sich als am besten bewährt, stets nur den kleinstmöglichen Umfang an Zugriffsrechten zu gewähren. Azure RBAC-Rollen, die in einem umfassenderen Bereich definiert sind, werden von den darunterliegenden Ressourcen geerbt.

Sie können den Zugriff auf Azure-Warteschlangenressourcen auf den folgenden Ebenen einschränken, beginnend mit dem kleinstmöglichen Bereich:

- **Eine einzelne Warteschlange**. Bei diesem Umfang gilt eine Rollenzuweisung für Nachrichten in der Warteschlange sowie für Warteschlangeneigenschaften und Metadaten.
- **Das Speicherkonto**. Bei diesem Bereich gilt eine Rollenzuweisung für alle Warteschlangen und deren Nachrichten.
- **Die Ressourcengruppe**. Bei diesem Bereich gilt eine Rollenzuweisung für alle Warteschlangen in allen Speicherkonten der Ressourcengruppe.
- **Das Abonnement**. Bei diesem Bereich gilt eine Rollenzuweisung für alle Warteschlangen in allen Speicherkonten in allen Ressourcengruppen des Abonnements.
- **Eine Verwaltungsgruppe**. Bei diesem Bereich gilt eine Rollenzuweisung für alle Warteschlangen in allen Speicherkonten in allen Ressourcengruppen in allen Abonnements in der Ressourcengruppe.

Weitere Informationen zum Bereich für Azure RBAC-Rollenzuweisungen finden Sie unter [Grundlegendes zum Bereich für Azure RBAC](../../role-based-access-control/scope-overview.md).

### <a name="azure-built-in-roles-for-queues"></a>In Azure integrierte Rollen für Warteschlangen

Azure RBAC stellt eine Reihe von integrierten Rollen zum Autorisieren des Zugriffs auf Warteschlangendaten mit Azure AD und OAuth bereit. Beispiele für Rollen, die Berechtigungen für Datenressourcen in Azure Storage bereitstellen:

- [Mitwirkender an Storage-Warteschlangendaten](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Dient zum Gewähren von Lese-/Schreib-/Löschberechtigungen für Azure-Warteschlangen.
- [Storage-Warteschlangendatenleser](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Dient zum Gewähren von Leseberechtigungen für Azure-Warteschlangen.
- [Verarbeiter von Speicherwarteschlangen-Datennachrichten](../../role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Dient zum Gewähren von Berechtigungen zum Einsehen, Abrufen und Löschen für Nachrichten in Azure Storage-Warteschlangen.
- [Absender der Speicherwarteschlangen-Datennachricht](../../role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Dient zum Gewähren von Berechtigungen zum Hinzufügen für Nachrichten in Azure Storage-Warteschlangen.

Informationen zum Zuweisen einer integrierten Azure-Rolle zu einem Sicherheitsprinzipal finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Warteschlangendaten](../queues/assign-azure-role-data-access.md). Informationen zum Auflisten von Azure RBAC-Rollen und deren Berechtigungen finden Sie unter [Auflisten von Azure-Rollendefinitionen](../../role-based-access-control/role-definitions-list.md).

Weitere Informationen dazu, wie integrierte Rollen für Azure Storage definiert sind, finden Sie unter [Grundlegendes zu Rollendefinitionen](../../role-based-access-control/role-definitions.md#control-and-data-actions). Informationen zum Erstellen von benutzerdefinierten Azure-Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md).

Nur Rollen, die explizit für den Datenzugriff definiert sind, ermöglichen einem Sicherheitsprinzipal den Zugriff auf Warteschlangendaten. Integrierte Rollen wie **Besitzer**, **Mitwirkender** und **Speicherkontomitwirkender** gestatten einem Sicherheitsprinzipal die Verwaltung eines Speicherkontos, gewähren aber keinen Zugriff auf die Warteschlangendaten in diesem Konto über Azure AD. Wenn eine Rolle jedoch **Microsoft.Storage/storageAccounts/listKeys/action** enthält, kann ein Benutzer, dem diese Rolle zugewiesen ist, über die Autorisierung mit gemeinsam verwendetem Schlüssel mit den Kontozugriffsschlüsseln auf Daten im Speicherkonto zugreifen. Weitere Informationen finden Sie unter [Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal](../../storage/queues/authorize-data-operations-portal.md).

Ausführliche Informationen zu integrierten Azure-Rollen für Azure Storage für die Datendienste und den Verwaltungsdienst finden Sie im Artikel [In Azure integrierte Rollen für Azure RBAC](../../role-based-access-control/built-in-roles.md#storage) im Abschnitt **Storage**. Informationen zu den verschiedenen Typen von Rollen, die Berechtigungen in Azure bereitstellen, finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Die Verteilung von Azure-Rollenzuweisungen kann bis zu 30 Minuten dauern.

### <a name="access-permissions-for-data-operations"></a>Zugriffsberechtigungen für Datenvorgänge

Einzelheiten zu den Berechtigungen, die für spezifische Vorgänge des Warteschlangendiensts erforderlich sind, finden Sie unter [Berechtigungen für das Aufrufen von Datenvorgängen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations).

## <a name="access-data-with-an-azure-ad-account"></a>Zugreifen auf Daten über ein Azure AD-Konto

Der Zugriff auf Warteschlangendaten über das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle kann über das Azure AD-Konto eines Benutzers oder über die Kontozugriffsschlüssel (Autorisierung mit gemeinsam verwendetem Schlüssel) autorisiert werden.

### <a name="data-access-from-the-azure-portal"></a>Datenzugriff über das Azure-Portal

Im Azure-Portal können Sie über Ihr Azure AD-Konto oder die Kontozugriffsschlüssel auf Warteschlangendaten in einem Azure-Speicherkonto zugreifen. Welches Autorisierungsschema im Azure-Portal verwendet wird, hängt von den Ihnen zugewiesenen Azure-Rollen ab.

Wenn Sie versuchen, auf Warteschlangendaten zuzugreifen, wird im Azure-Portal zunächst überprüft, ob Ihnen eine Azure-Rolle mit **Microsoft.Storage/storageAccounts/listkeys/action** zugewiesen ist. Wenn Ihnen eine Rolle mit dieser Aktion zugewiesen wurde, wird im Azure-Portal der Kontoschlüssel für den Zugriff auf Warteschlangendaten per Authentifizierung mit gemeinsam verwendetem Schlüssel verwendet. Wenn Ihnen keine Rolle mit dieser Aktion zugewiesen wurde, wird im Azure-Portal versucht, über Ihr Azure AD-Konto auf die Daten zuzugreifen.

Für den Zugriff auf Warteschlangendaten über das Azure-Portal mithilfe Ihres Azure AD-Kontos benötigen Sie Berechtigungen für den Zugriff auf Warteschlangendaten sowie das Navigieren durch die Ressourcen des Speicherkontos im Azure-Portal. Die integrierten Rollen, die von Azure Storage bereitgestellt werden, gewähren Zugriff auf Warteschlangenressourcen, aber nicht auf die Speicherkontoressourcen. Aus diesem Grund erfordert der Zugriff auf das Portal außerdem die Zuweisung einer Azure Resource Manager-Rolle (z. B. [Leser](../../role-based-access-control/built-in-roles.md#reader)) mindestens auf Ebene des Speicherkontos. Die Rolle **Leser** erteilt die am stärksten eingeschränkten Berechtigungen. Eine andere Azure Resource Manager-Rolle, die den Zugriff auf Ressourcen zur Verwaltung von Speicherkonten gewährt, ist jedoch ebenfalls akzeptabel. Weitere Informationen zum Zuweisen von Berechtigungen zu Benutzern für den Datenzugriff im Azure-Portal über ein Azure AD-Konto finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Warteschlangendaten](../queues/assign-azure-role-data-access.md).

Wenn Sie zu einer Warteschlange navigieren, wird im Azure-Portal angegeben, welches Autorisierungsschema verwendet wird. Weitere Informationen zum Datenzugriff im Portal finden Sie unter [Auswählen der Autorisierung des Zugriffs auf Warteschlangendaten im Azure-Portal](../queues/authorize-data-operations-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Datenzugriff über PowerShell oder die Azure-Befehlszeilenschnittstelle

In der Azure-Befehlszeilenschnittstelle und PowerShell ist die Anmeldung mit Azure AD-Anmeldeinformationen möglich. Nach der Anmeldung wird Ihre Sitzung unter diesen Anmeldeinformationen ausgeführt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Auswählen der Autorisierung des Zugriffs auf Warteschlangendaten mit der Azure CLI](authorize-data-operations-cli.md)
- [Ausführen von PowerShell-Befehlen mit Azure AD-Anmeldeinformationen für den Zugriff auf Warteschlangendaten](authorize-data-operations-powershell.md)

## <a name="next-steps"></a>Nächste Schritte

- [Autorisieren des Zugriffs auf Daten in Azure Storage](../common/authorize-data-access.md)
- [Zuweisen einer Azure-Rolle für den Zugriff auf Warteschlangendaten](assign-azure-role-data-access.md)