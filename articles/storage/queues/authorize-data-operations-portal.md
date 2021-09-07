---
title: Auswählen der Autorisierung des Zugriffs auf Warteschlangendaten im Azure-Portal
titleSuffix: Azure Storage
description: Wenn Sie über das Azure-Portal auf Warteschlangendaten zugreifen, werden vom Portal im Hintergrund Anforderungen an Azure Storage gesendet. Diese Anforderungen an Azure Storage können entweder mit Ihrem Azure AD-Konto oder Zugriffsschlüssel für das Speicherkonto authentifiziert und autorisiert werden.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 06/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: da6dfb018885ce55323e4a05e738501692c68a58
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727974"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Auswählen der Autorisierung des Zugriffs auf Warteschlangendaten im Azure-Portal

Wenn Sie über das [Azure-Portal](https://portal.azure.com) auf Warteschlangendaten zugreifen, werden vom Portal im Hintergrund Anforderungen an Azure Storage gesendet. Eine Anforderung an Azure Storage kann entweder mit Ihrem Azure AD-Konto oder Zugriffsschlüssel für das Speicherkonto autorisiert werden. Das Portal zeigt an, welche Methode Sie verwenden, und ermöglicht Ihnen, zwischen den beiden zu wechseln, sofern Sie die entsprechenden Berechtigungen haben.

## <a name="permissions-needed-to-access-queue-data"></a>Für den Zugriff auf Warteschlangendaten benötigte Berechtigungen

Je nachdem, wie Sie den Zugriff Warteschlangendaten im Azure-Portal autorisieren möchten, benötigen Sie spezielle Berechtigungen. Meist werden diese Berechtigungen über die rollenbasierte Zugriffssteuerung (Azure RBAC) bereitgestellt. Weitere Informationen zu Azure RBAC finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Verwenden des Kontozugriffsschlüssels

Für den Zugriff auf Warteschlangendaten mit dem Kontozugriffsschlüssel muss Ihnen eine Azure-Rolle zugewiesen sein, die die Azure RBAC-Aktion **Microsoft.Storage/storageAccounts/listkeys/action** umfasst. Bei dieser Azure-Rolle kann es sich um eine integrierte oder benutzerdefinierte Rolle handeln. Es folgen Beispiele für integrierte Rollen, die **Microsoft.Storage/storageAccounts/listkeys/action** unterstützen, nach dem Ausmaß ihrer Berechtigungen von niedrig bis hoch sortiert:

- Die Rolle [Lese- und Datenzugriff](../../role-based-access-control/built-in-roles.md#reader-and-data-access)
- Die Rolle [Speicherkontomitwirkender](../../role-based-access-control/built-in-roles.md#storage-account-contributor)
- Die Azure Resource Manager-Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor)
- Die Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner)

Wenn Sie versuchen, im Azure-Portal auf Warteschlangendaten zuzugreifen, wird vom Portal zunächst überprüft, ob Ihnen eine Rolle mit **Microsoft.Storage/storageAccounts/listkeys/action** zugewiesen ist. Wenn Ihnen eine Rolle mit dieser Aktion zugewiesen wurde, wird im Portal der Kontoschlüssel für den Zugriff auf Warteschlangendaten verwendet. Wenn Ihnen keine Rolle mit dieser Aktion zugewiesen wurde, versucht das Portal, über Ihr Azure AD-Konto auf Daten zuzugreifen.

> [!IMPORTANT]
> Wenn ein Speicherkonto mit **ReadOnly** in Azure Resource Manager gesperrt ist, ist der Vorgang [Schlüssel auflisten](/rest/api/storagerp/storageaccounts/listkeys) für dieses Speicherkonto nicht zulässig. **Schlüssel auflisten** ist ein POST-Vorgang, und alle POST-Vorgänge werden verhindert, wenn die Sperre **ReadOnly** für das Konto festgelegt wurde. Wenn das Konto mit einer **ReadOnly**-Sperre gesperrt ist, müssen die Benutzer aus diesem Grund Anmeldeinformationen von Azure AD verwenden, um auf Warteschlangendaten im Portal zuzugreifen. Informationen zum Zugreifen auf Warteschlangendaten im Portal mit Azure AD finden Sie unter [Verwenden des Azure AD-Kontos](#use-your-azure-ad-account).

> [!NOTE]
> Die klassischen Abonnement-Administratorrollen **Dienstadministrator** und **Co-Administrator** schließen die Entsprechung der Azure Resource Manager-Rolle [`Owner`](../../role-based-access-control/built-in-roles.md#owner) ein. Die Rolle **Besitzer** schließt alle Aktionen einschließlich **Microsoft.Storage/storageAccounts/listkeys/action** ein, sodass ein Benutzer mit einer dieser Administratorrollen auch mit dem Kontoschlüssel auf Warteschlangendaten zugreifen kann. Weitere Informationen finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Verwenden des Azure AD-Kontos

Für den Zugriff auf Warteschlangendaten im Azure-Portal über Ihr Azure AD-Konto müssen Sie die beiden folgenden Voraussetzungen erfüllen:

- Ihnen wurde entweder eine integrierte oder benutzerdefinierte Rolle zugewiesen, die den Zugriff auf Warteschlangendaten ermöglicht.
- Ihnen wurde die Azure Resource Manager-Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) mindestens bis zur Ebene des Speicherkontos oder höher zugewiesen. Die Rolle **Leser** erteilt die am stärksten eingeschränkten Berechtigungen. Eine andere Azure Resource Manager-Rolle, die den Zugriff auf Ressourcen zur Verwaltung von Speicherkonten gewährt, ist jedoch ebenfalls akzeptabel.

Die Azure Resource Manager-Rolle **Leser** ermöglicht es Benutzern, Ressourcen im Speicherkonto anzuzeigen, ohne sie zu ändern. Sie bietet keine Leseberechtigungen für Daten in Azure Storage, sondern nur für Ressourcen zur Kontoverwaltung. Die Rolle **Leser** ist erforderlich, damit Benutzer im Azure-Portal zu Warteschlangen navigieren können.

Informationen zu den integrierten Rollen, die den Zugriff auf Warteschlangendaten unterstützen, finden Sie unter [Autorisieren des Zugriffs auf Warteschlangen mithilfe von Azure Active Directory](authorize-access-azure-active-directory.md).

Benutzerdefinierte Rollen können verschiedene Kombinationen der von den integrierten Rollen gebotenen Berechtigungen unterstützen. Weitere Informationen zum Erstellen benutzerdefinierter Azure-Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md) und [Grundlegendes zu Rollendefinitionen für Azure-Ressourcen](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Die Vorschauversion des Storage-Explorers im Azure-Portal unterstützt nicht die Verwendung von Azure AD-Anmeldeinformationen zum Anzeigen und Ändern von Warteschlangendaten. Storage-Explorer im Azure-Portal verwendet immer die Kontoschlüssel für den Zugriff auf Daten. Um Storage-Explorer im Azure-Portal verwenden zu können, muss Ihnen eine Rolle zugewiesen werden, in der **Microsoft.Storage/storageAccounts/listkeys/action** enthalten ist.

## <a name="navigate-to-queues-in-the-azure-portal"></a>Navigieren zu Warteschlangen im Azure-Portal

Um Warteschlangendaten im Portal anzuzeigen, navigieren Sie zur **Übersicht** Ihres Speicherkontos und klicken auf die Links für **Warteschlangen**. Alternativ dazu können Sie im Menü zum Abschnitt **Warteschlangendienst** navigieren.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Screenshot, der zeigt, wie Sie im Azure-Portal zu Warteschlangendaten navigieren":::

## <a name="determine-the-current-authentication-method"></a>Bestimmen der aktuellen Authentifizierungsmethode

Wenn Sie zu einer Warteschlange navigieren, wird im Azure-Portal angezeigt, ob Sie zur Authentifizierung derzeit den Kontozugriffsschlüssel oder Ihr Azure AD-Konto verwenden.

### <a name="authenticate-with-the-account-access-key"></a>Authentifizierung mit dem Kontozugriffsschlüssel

Wenn Sie sich mit dem Kontozugriffsschlüssel authentifizieren, wird im Portal **Zugriffsschlüssel** als Authentifizierungsmethode angegeben:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Screenshot eines Benutzers, der gegenwärtig mit dem Kontoschlüssel auf Warteschlangen zugreift":::

Um zur Verwendung des Azure AD-Kontos zu wechseln, klicken Sie auf den in der Abbildung hervorgehobenen Link. Wenn Sie über die Ihnen zugewiesenen Azure-Rollen die entsprechenden Berechtigungen haben, können Sie fortfahren. Wenn Ihnen jedoch die benötigten Berechtigungen fehlen, erhalten Sie eine Fehlermeldung wie die folgende:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Angezeigter Fehler, wenn das Azure AD-Konto den Zugriff nicht unterstützt":::

Beachten Sie, dass in der Liste keine Warteschlangen enthalten sind, wenn Ihrem Azure AD-Konto die Anzeigeberechtigung fehlt. Klicken Sie auf den Link **Zum Zugriffsschlüssel wechseln**, um erneut den Zugriffsschlüssel für die Authentifizierung zu nutzen.

### <a name="authenticate-with-your-azure-ad-account"></a>Authentifizierung mit dem Azure AD-Konto

Wenn Sie sich mit Ihrem Azure AD-Konto authentifizieren, wird im Portal **Azure AD-Benutzerkonto** als Authentifizierungsmethode angegeben:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Screenshot eines Benutzers, der gegenwärtig mit dem Azure AD-Konto auf Warteschlangen zugreift":::

Um zur Verwendung des Zugriffsschlüssels zu wechseln, klicken Sie auf den in der Abbildung hervorgehobenen Link. Wenn Sie Zugriff auf den Kontoschlüssel haben, können Sie fortfahren. Wenn Sie jedoch keinen Zugriff auf den Kontoschlüssel haben, wird im Azure-Portal eine Fehlermeldung angezeigt.

Warteschlangen werden nicht im Portal aufgeführt, wenn Sie keinen Zugriff auf die Kontoschlüssel haben. Klicken Sie auf den Link **Zum Azure AD-Benutzerkonto wechseln**, um erneut das Azure AD-Konto für die Authentifizierung zu nutzen.

## <a name="next-steps"></a>Nächste Schritte

- [Autorisieren des Zugriffs auf Daten in Azure Storage](../common/authorize-data-access.md)
- [Zuweisen einer Azure-Rolle für den Zugriff auf Warteschlangendaten](assign-azure-role-data-access.md)
