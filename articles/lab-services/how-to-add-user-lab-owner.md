---
title: Hinzufügen zusätzlicher Benutzer zu einem Lab in Azure Lab Services
description: In diesem Artikel erfahren Sie, wie ein Administrator einen Benutzer als Besitzer eines Labs in Azure Lab Services hinzufügen kann.
ms.topic: article
ms.date: 08/03/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: e8dd21a140e1c519f09d661c776e5c84c47da925
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635011"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Hinzufügen zusätzlicher Benutzer zu einem vorhandenen Lab in Azure Lab Services
In diesem Artikel erfahren Sie, wie ein Administrator zusätzliche Besitzer zu einem vorhandenen Lab hinzufügen kann.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Hinzufügen von Benutzern zur Leserrolle für das Labkonto
1. Kehren Sie zurück zur Seite **Labkonto**, und klicken Sie im linken Menü auf **Alle Labs**.
2. Wählen Sie das **Lab** aus, zu dem Sie einen Benutzer als Besitzer hinzufügen möchten. 

    ![Auswählen des Labs ](./media/how-to-add-user-lab-owner/select-lab.png)  
1. Wählen Sie im Navigationsmenü **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen (Vorschau)** aus.

    ![Seite „Zugriffssteuerung (IAM)“ mit geöffnetem Menü „Rollenzuweisung hinzufügen“](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Wählen Sie auf der Registerkarte **Rollen** die Rolle **Leser** aus.

    ![Seite „Rollenzuweisung hinzufügen“ mit ausgewählter Registerkarte „Rolle“](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** den Benutzer aus, den Sie der Rolle „Leser“ hinzufügen möchten.

1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.
## <a name="add-user-to-the-owner-role-for-the-lab"></a>Hinzufügen von Benutzern zur Benutzerrolle für das Lab

> [!NOTE]
> Wenn der Benutzer nur über Lesezugriff für ein Lab verfügt, wird das Lab in labs.azure.com nicht angezeigt. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).


1. Wählen Sie auf der Seite **Lab-Konto** die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen (Vorschau)** aus.

    ![Seite „Zugriffssteuerung (IAM)“ mit geöffnetem Menü „Rollenzuweisung hinzufügen“](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Wählen Sie auf der Registerkarte **Rollen** die Rolle **Besitzer** aus.

    ![Seite „Rollenzuweisung hinzufügen“ mit ausgewählter Registerkarte „Rolle“](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** den Benutzer aus, den Sie der Rolle „Besitzer“ hinzufügen möchten.

1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.


## <a name="next-steps"></a>Nächste Schritte
Bestätigen Sie, dass das Lab dem Benutzer angezeigt wird, sobald er sich beim [Lab Services-Portal](https://labs.azure.com) anmeldet.
