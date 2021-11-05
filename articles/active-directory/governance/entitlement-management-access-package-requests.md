---
title: Anzeigen der Anforderungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung – Azure Active Directory
description: Lernen Sie, wie Sie Anforderungen für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung anzeigen.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 9/20/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140d648ddde6d520fdb27c6df0152cb9eb61c1fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051799"
---
# <a name="view-and-remove-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Anzeigen und Entfernen von Anforderungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung

In der Azure AD-Berechtigungsverwaltung können Sie anzeigen, wer Zugriffspakete angefordert hat. Außerdem können Sie die jeweilige Richtlinie und den Status anzeigen. In diesem Artikel wird beschrieben, wie Sie Anforderungen für ein Zugriffspaket anzeigen und nicht mehr benötigte Anforderungen entfernen.

## <a name="view-requests"></a>Anzeigen von Anforderungen

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaket-Manager oder Zugriffspaketzuweisungs-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Anforderungen**.

1. Klicken Sie auf eine bestimmte Anforderung, wenn Sie zusätzliche Details anzeigen möchten.

    ![Anforderungsliste für ein Zugriffspaket](./media/entitlement-management-access-package-requests/requests-list.png)

1. Sie können auf Details des **Anforderungsverlaufs** klicken, um zu sehen, wer eine Anforderung genehmigt hat, welche Genehmigungsberechtigungen es gab und wann der Zugriff übermittelt wurde.

Wenn es eine Gruppe von Benutzern gibt, deren Anforderungen den Status „Teilweise übermittelt“ oder „Fehler“ haben, müssen Sie möglicherweise diese Anfragen mit Hilfe der [Wiederbearbeitungsfunktion](entitlement-management-reprocess-access-package-requests.md) erneut.

### <a name="view-assignments-with-microsoft-graph"></a>Anzeigen von Zuweisungen mit Microsoft Graph
Sie können auch mithilfe von Microsoft Graph Zuweisungen in einem Zugriffspaket abrufen.  Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte Berechtigung `EntitlementManagement.Read.All` oder `EntitlementManagement.ReadWrite.All` verfügt, kann die API aufrufen, um [accessPackageAssignments“aufzulisten](/graph/api/accesspackageassignmentrequest-list?view=graph-rest-beta&preserve-view=true). Sie können einen Filter anwenden, um ein bestimmtes Zugriffspaket anzugeben, z. B.:  `$expand=accessPackage&$filter=accessPackage/id eq '9bbe5f7d-f1e7-4eb1-a586-38cdf6f8b1ea'`. Für eine Anwendung, die über die Anwendungsberechtigung `EntitlementManagement.Read.All` oder `EntitlementManagement.ReadWrite.All` verfügt, kann diese API ebenfalls verwendet werden.

## <a name="remove-request-preview"></a>Anforderung entfernen (Vorschau)

Sie können auch eine abgeschlossene Anforderung entfernen, die nicht mehr benötigt wird. So entfernen Sie eine Anforderung:

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Anforderungen**.

1. Suchen Sie die Anforderung, die Sie aus dem Zugriffspaket entfernen möchten.

1. Wählen Sie die Schaltfläche Entfernen aus.

> [!NOTE]
> Wenn Sie eine abgeschlossene Anforderung aus einem Zugriffspaket entfernen, wird die aktive Zuweisung nicht entfernt, sondern nur die Daten der Anforderung. Der anfordernde Benutzer hat also weiterhin Zugriff. Wenn Sie auch eine Zuweisung und den daraus resultierenden Zugriff aus diesem Zugriffspaket entfernen müssen, klicken Sie im linken Menü auf **Zuweisungen**, suchen Sie die Zuweisung, und dann [entfernen Sie die Zuweisung](entitlement-management-access-package-assignments.md).

### <a name="remove-a-request-with-microsoft-graph"></a>Entfernen einer Anforderung mit Microsoft Graph

Sie können eine Anforderung auch mithilfe von Microsoft Graph entfernen.  Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte `EntitlementManagement.ReadWrite.All`Berechtigung oder eine Anwendung mit dieser Anwendungsberechtigung verfügt, kann die API aufrufen[, um ein accessPackageAssignmentRequest“ zu entfernen](/graph/api/accesspackageassignmentrequest-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Nächste Schritte

- [Nachbearbeitung von Anforderungen für ein Zugriffspaketpaket](entitlement-management-reprocess-access-package-requests.md)
- [Ändern der Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket](entitlement-management-access-package-request-policy.md)
- [Anzeigen, Hinzufügen und Entfernen von Zuweisungen für ein Zugriffspaket](entitlement-management-access-package-assignments.md)
- [Behandeln von Problemen bei Anforderungen](entitlement-management-troubleshoot.md#requests)
