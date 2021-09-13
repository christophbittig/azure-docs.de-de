---
title: Gewähren von rollenbasiertem Zugriff für Benutzer in Microsoft Azure Maps
titleSuffix: Azure Maps
description: Verwenden der rollenbasierten Zugriffssteuerung, um Benutzern Autorisierung für Azure Maps zu gewähren
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: 22b3eaaff0989718a781b29549b4fab102673273
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802346"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Gewähren von rollenbasierter Zugriffssteuerung für Benutzer in Azure Maps

Sie können die *rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)* verwenden, indem Sie mindestens einer Azure Maps-Rollendefinition eine Azure AD-Gruppe oder einen Sicherheitsprinzipal zuweisen.

Die verfügbaren Azure-Rollendefinitionen für Azure Maps finden Sie unter [Anzeigen der integrierten Azure Maps-Rollendefinitionen](../how-to-manage-authentication.md#view-built-in-azure-maps-role-definitions).

Ausführliche Schritte zum Zuweisen einer verfügbaren Azure Maps-Rolle zur erstellten verwalteten Identität oder zum Dienstprinzipal finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

Informationen zum effizienten Verwalten des Zugriffs auf Azure Maps-Apps und -Ressourcen für eine Vielzahl von Benutzern finden Sie unter [Verwalten des Zugriffs auf Apps und Ressourcen mithilfe von Azure Active Directory-Gruppen](../../active-directory/fundamentals/active-directory-manage-groups.md).

>[!IMPORTANT]
>Damit Benutzer sich bei einer Anwendung authentifizieren können, müssen sie zuerst in Azure AD erstellt werden. Weitere Informationen finden Sie unter [Hinzufügen oder Löschen von Benutzern in Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Informationen zur effektiven Verwaltung eines großen Verzeichnisses für Benutzer finden Sie in der [Dokumentation mit grundlegenden Informationen zu Azure Active Directory](../../active-directory/fundamentals/index.yml).

> [!WARNING]
> Integrierte Azure Maps-Rollendefinitionen bieten einen sehr umfangreichen Autorisierungszugriff auf viele Azure Maps-REST-APIs. Informationen, wie Sie den Zugriff auf APIs auf ein Mindestmaß beschränken, finden Sie unter [Erstellen einer benutzerdefinierten Rollendefinition und Zuweisen der systemseitig zugewiesenen Identität](../../role-based-access-control/custom-roles.md) zur benutzerdefinierten Rollendefinition. Dadurch wird die geringste Berechtigung aktiviert, die für den Zugriff der Anwendung auf Azure Maps erforderlich ist.
