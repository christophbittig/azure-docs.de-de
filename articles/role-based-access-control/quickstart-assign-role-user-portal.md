---
title: 'Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer mit dem Azure-Portal – Azure RBAC'
description: In diesem Tutorial erfahren Sie, wie Sie einem Benutzer mit dem Azure-Portal und der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) Zugriff auf Azure-Ressourcen gewähren.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/15/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: 96064811bf0d82ab37a338adf3ed2ac4568c57f1
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069165"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer mit dem Azure-Portal

Der Zugriff auf Azure-Ressourcen wird mithilfe der [rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](overview.md) verwaltet. In diesem Tutorial gewähren Sie einem Benutzer Zugriff, um virtuelle Computer in einer Ressourcengruppe zu erstellen und zu verwalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Gewähren des Zugriffs für einen Benutzer im Ressourcengruppenbereich
> * Zugriff entfernen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

1. Klicken Sie in der Navigationsliste auf **Ressourcengruppen**.

1. Klicken Sie auf **Neu**, um die Seite **Erstellen einer Ressourcengruppe** zu öffnen.

   ![Erstellen Sie eine neue Ressourcengruppe.](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Wählen Sie ein Abonnement aus.

1. Geben Sie für den **Ressourcengruppenname** den Namen **Beispielgruppe** oder einen anderen Namen ein.

1. Klicken Sie auf **Überprüfen + Erstellen** und anschließend auf **Erstellen**, um die Ressourcengruppe zu erstellen.

1. Klicken Sie auf **Aktualisieren**, um die Liste der Ressourcengruppen zu aktualisieren.

   Die neue Ressourcengruppe wird in der Liste Ihrer Ressourcengruppen angezeigt.

## <a name="grant-access"></a>Gewähren von Zugriff

In Azure RBAC weisen Sie zum Gewähren des Zugriffs eine Azure-Rolle zu.

1. Öffnen Sie in der Liste der **Ressourcengruppen** die neue Ressourcengruppe **Beispielgruppe**.

1. Klicken Sie in dem Navigationsmenü auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die aktuelle Liste mit den Rollenzuweisungen anzuzeigen.

   ![Wählen Sie auf der Seite „Zugriffssteuerung (IAM)“ eine Ressourcengruppe aus.](./media/shared/rg-role-assignments.png)


1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen**.

   Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „Rollenzuweisung hinzufügen“ deaktiviert.

   ![Wählen Sie auf der Seite „Zugriffssteuerung (IAM)“ auf der Registerkarte „Add“ die Option „Hinzufügen von Rollenzuweisung“ aus.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Wählen Sie auf der Registerkarte **Rollen** die Rolle **Mitwirkender für virtuelle Computer** aus.

    ![Wählen Sie auf der Seite „Hinzufügen von Rollenzuweisung“ die Registerkarte „Rolle“ aus.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** sich selbst oder einen anderen Benutzer aus.

1. Überprüfen Sie auf der Registerkarte **Überprüfen und zuweisen** die Einstellungen für die Rollenzuweisung.

1. Klicken Sie auf **Überprüfen und zuweisen**, um die Rolle zuzuweisen.

   Nach einigen Augenblicken wird dem Benutzer die Rolle „Mitwirkender für virtuelle Computer“ im Ressourcengruppenbereich „Beispielgruppe“ zugewiesen.

   ![Rollenzuweisung: Mitwirkender von virtuellen Computern.](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Entfernen des Zugriffs

In Azure RBAC entfernen Sie eine Rollenzuweisung, um den Zugriff zu entfernen.

1. Fügen Sie in der Liste der Rollenzuweisungen ein Häkchen neben dem Benutzer mit der Rolle „Mitwirkender für virtuelle Computer“ hinzu.

1. Klicken Sie auf **Entfernen**.

   ![Meldung zum Entfernen der Rollenzuweisung.](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Klicken Sie in der angezeigten Meldung zum Entfernen der Rollenzuweisung auf **Ja**.

## <a name="clean-up"></a>Bereinigung

1. Klicken Sie in der Navigationsliste auf **Ressourcengruppen**.

1. Klicken Sie auf **Beispielgruppe**, um die Ressourcengruppe zu öffnen.

1. Klicken Sie auf **Ressourcengruppe löschen**, um die Ressourcengruppe zu löschen.

1. Geben Sie auf dem Blatt **Wollen Sie wirklich löschen** den Namen der Ressourcengruppe ein und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer mithilfe von Azure PowerShell](tutorial-role-assignments-user-powershell.md)
