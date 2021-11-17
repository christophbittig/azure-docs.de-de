---
title: Festlegen einer in Privileged Identity Management zu verwaltenden Gruppe – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Gruppen mit Rollenzuweisung, die als Gruppen mit privilegiertem Zugriff verwaltet werden sollen, in Privileged Identity Management (PIM) integrieren können.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/09/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72f848dae3eda447edee40b0da18f09fed50462c
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157049"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Verwalten von Gruppen mit privilegiertem Zugriff (Vorschau) in Privileged Identity Management

In Azure Active Directory (Azure AD) können Sie Cloudgruppen integrierte Azure AD-Rollen zuweisen, um die Verwaltung von Rollenzuweisungen zu vereinfachen. Zum Schutz von Azure AD-Rollen und für einen sicheren Zugriff können Sie nun Privileged Identity Management (PIM) verwenden, um den Just-in-Time-Zugriff für Mitglieder oder Besitzer dieser Gruppen zu verwalten. Um eine Azure AD-Gruppe, der Rollen zugewiesen werden können, als eine Gruppe mit privilegiertem Zugriff in Privileged Identity Management verwalten zu können, müssen Sie diese der Verwaltung in PIM unterstellen.

## <a name="identify-groups-to-manage"></a>Festlegen zu verwaltender Gruppen

Sie können in Azure AD eine Gruppe mit Rollenzuweisung erstellen, wie es unter [Erstellen einer Gruppe in Azure Active Directory für das Zuweisen von Rollen](../roles/groups-create-eligible.md) beschrieben ist. Sie müssen ein Besitzer der Gruppe sein, um Sie der Verwaltung mit Privileged Identity Management zu unterstellen.

1. [Melden Sie sich bei Azure AD](https://aad.portal.azure.com) mit den Berechtigungen der Rolle „Administrator für privilegierte Rollen“ an.

1. Wählen Sie **Gruppen** aus, und wählen Sie dann die Gruppe mit Rollenzuweisung aus, die Sie in PIM verwalten möchten. Sie können die Liste durchsuchen und filtern.

    ![Suchen einer Gruppe mit Rollenzuweisung für die Verwaltung in PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Öffnen Sie die Gruppe, und wählen Sie **Privilegierter Zugriff (Vorschau)** aus.

    ![Öffnen der Privileged Identity Management-Benutzeroberfläche](./media/groups-discover-groups/groups-discover-groups.png)

1. Beginnen Sie mit der Verwaltung von Zuweisungen in PIM.

    ![Verwalten von Zuweisungen in Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Sobald eine Gruppe mit privilegiertem Zugriff verwaltet wird, kann Sie nicht mehr aus der Verwaltung entfernt werden. Dadurch wird verhindert, dass andere Ressourcenadministratoren Privileged Identity Management-Einstellungen entfernen.

> [!IMPORTANT]
> Wird eine Gruppe mit privilegiertem Zugriff aus Azure Active Directory gelöscht, kann es bis zu 24 Stunden dauern, bis die Gruppe auf dem Blatt „Gruppen mit privilegiertem Zugriff (Vorschau)“ entfernt wird.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Zuweisungen für Gruppen mit privilegiertem Zugriff in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Zuweisen von Gruppen mit privilegiertem Zugriff in Privileged Identity Management](pim-resource-roles-assign-roles.md)
