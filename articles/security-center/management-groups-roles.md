---
title: Organisieren von Abonnements in Verwaltungsgruppen und Zuweisen von Rollen zu Benutzern für Microsoft Defender für Cloud
description: Hier erfahren Sie, wie Sie Ihre Azure-Abonnements in Verwaltungsgruppen in Microsoft Defender für Cloud organisieren und Benutzern bzw. Benutzerinnen in Ihrer Organisation Rollen zuweisen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/11/2021
ms.custom: subject-rbac-steps
ms.author: memildin
ms.openlocfilehash: ffb6e78ffd0b4214f0751686dba695e8d35e3160
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425119"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Organisieren von Abonnements in Verwaltungsgruppen und Zuweisen von Rollen zu Benutzern

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Auf dieser Seite wird erläutert, wie Sie den Sicherheitsstatus Ihrer Organisation bedarfsorientiert verwalten, indem Sie Sicherheitsrichtlinien auf alle Azure-Abonnements anwenden, die mit Ihrem Azure Active Directory-Mandanten verknüpft sind.

Für eine Sichtbarkeit des Sicherheitsstatus aller Abonnements, die mit einem Azure AD-Mandanten verknüpft sind, benötigen Sie eine Azure-Rolle mit ausreichenden Leseberechtigungen, die in der Stammverwaltungsgruppe zugewiesen wurde.

## <a name="organize-your-subscriptions-into-management-groups"></a>Organisieren von Abonnements in Verwaltungsgruppen

### <a name="introduction-to-management-groups"></a>Einführung in Verwaltungsgruppen

Verwenden Sie Verwaltungsgruppen für das effiziente Verwalten der Bereiche Zugriff, Richtlinien und Berichterstellung für **Gruppen von Abonnements** sowie das effektive Verwalten der gesamten Azure-Umgebung, indem Aktionen für die Stammverwaltungsgruppe durchgeführt werden. Sie können Abonnements in Verwaltungsgruppen organisieren und Ihre Governancerichtlinien auf die Verwaltungsgruppen anwenden. Alle Abonnements in einer Verwaltungsgruppe erben automatisch die auf die Verwaltungsgruppe angewendeten Richtlinien. 

Jeder Azure AD-Mandant wird einer einzelnen Verwaltungsgruppe der obersten Ebene zugewiesen, die als **Stammverwaltungsgruppe** bezeichnet wird. Die Stammverwaltungsgruppe ist in die Hierarchie integriert, sodass ihr alle Verwaltungsgruppen und Abonnements untergeordnet sind. Diese Gruppe ermöglicht das Anwenden von globalen Richtlinien und Azure-Rollenzuweisungen auf Verzeichnisebene. 

Die Stammverwaltungsgruppe wird automatisch erstellt, wenn Sie die folgenden Aktionen durchführen: 
- Öffnen Sie **Verwaltungsgruppen** im [Azure-Portal](https://portal.azure.com).
- Erstellen Sie per API-Aufruf eine Verwaltungsgruppe.
- Erstellen Sie eine Verwaltungsgruppe mit PowerShell. Eine Anleitung für PowerShell finden Sie unter [Erstellen von Verwaltungsgruppen zum Organisieren und Verwalten von Ressourcen](../governance/management-groups/create-management-group-portal.md).

Für das Defender für Cloud-Onboarding sind Verwaltungsgruppen zwar nicht erforderlich, aber es wird empfohlen, mindestens eine Verwaltungsgruppe zu erstellen, damit die Stammverwaltungsgruppe erstellt wird. Nachdem die Gruppe erstellt wurde, werden alle Abonnements unter Ihrem Azure AD-Mandanten damit verknüpft. 

Eine ausführliche Übersicht über Verwaltungsgruppen finden Sie im Artikel [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md).

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Anzeigen und Erstellen von Verwaltungsgruppen im Azure-Portal

1. Verwenden Sie im [Azure-Portal](https://portal.azure.com) das Suchfeld in der oberen Leiste, um **Verwaltungsgruppen** zu suchen und zu öffnen.

    :::image type="content" source="./media/management-groups-roles/open-management-groups-service.png" alt-text="Zugreifen auf Ihre Verwaltungsgruppen":::

    Die Liste Ihrer Verwaltungsgruppen wird angezeigt.

1. Zum Erstellen einer Verwaltungsgruppe klicken Sie auf **Verwaltungsgruppe hinzufügen**, geben die relevanten Details ein und klicken auf **Speichern**.

    :::image type="content" source="media/management-groups-roles/add-management-group.png" alt-text="Hinzufügen einer Verwaltungsgruppe zu Azure":::

    - Die **ID der Verwaltungsgruppe** ist der eindeutige Bezeichner des Verzeichnisses, der zum Übermitteln von Befehlen für diese Verwaltungsgruppe verwendet wird. Dieser Bezeichner kann nach der Erstellung nicht bearbeitet werden, da er im gesamten Azure-System zum Identifizieren dieser Gruppe verwendet wird. 
    - Im Feld für den Anzeigenamen wird der Name angegeben, der im Azure-Portal angezeigt wird. Beim Erstellen der Verwaltungsgruppe gibt es ein optionales Feld für einen separaten Anzeigenamen, der jederzeit geändert werden kann.  


### <a name="add-subscriptions-to-a-management-group"></a>Hinzufügen von Abonnements zu einer Verwaltungsgruppe
Sie können Abonnements der von Ihnen erstellten Verwaltungsgruppe hinzufügen.

1. Öffnen Sie im Azure-Portal **Verwaltungsgruppen** und wählen Sie die Verwaltungsgruppe für Ihr Abonnement aus.

    :::image type="content" source="./media/management-groups-roles/management-group-subscriptions.png" alt-text="Auswählen einer Verwaltungsgruppe für Ihr Abonnement":::

1. Wenn die Seite der Gruppe geöffnet wird, wählen Sie **Abonnements** aus.

1. Wählen Sie auf der Abonnementseite die Option **Hinzufügen** aus, wählen Sie Ihre Abonnements aus und wählen Sie dann **Speichern** aus. Wiederholen Sie diese Schritte, bis Sie alle betreffenden Abonnements hinzugefügt haben.

    :::image type="content" source="./media/management-groups-roles/management-group-add-subscriptions.png" alt-text="Hinzufügen eines Abonnements zu einer Verwaltungsgruppe":::

   > [!IMPORTANT]
   > Verwaltungsgruppen können sowohl Abonnements als auch untergeordnete Verwaltungsgruppen enthalten. Wenn Sie einem Benutzer eine Azure-Rolle für die übergeordnete Verwaltungsgruppe zuweisen, wird der Zugriff von den Abonnements der untergeordneten Verwaltungsgruppe geerbt. Richtlinien, die für die übergeordnete Verwaltungsgruppe festgelegt werden, werden ebenfalls von den untergeordneten Elementen geerbt. 



## <a name="assign-azure-roles-to-other-users"></a>Zuweisen von Azure-Rollen zu anderen Benutzern

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Weisen Sie Benutzern Azure-Rollen über das Azure-Portal zu: 

1. Verwenden Sie im [Azure-Portal](https://portal.azure.com) das Suchfeld in der oberen Leiste, um **Verwaltungsgruppen** zu suchen und zu öffnen.

    :::image type="content" source="./media/management-groups-roles/open-management-groups-service.png" alt-text="Zugreifen auf Ihre Verwaltungsgruppen":::

    Die Liste Ihrer Verwaltungsgruppen wird angezeigt.

1.  Wählen Sie die entsprechende Verwaltungsgruppe aus.

1. Wählen Sie **Zugriffssteuerung (IAM)** aus, öffnen Sie die Registerkarte **Rollenzuweisungen** und wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus.

    :::image type="content" source="./media/management-groups-roles/add-user.png" alt-text="Hinzufügen eines Benutzers zu einer Verwaltungsgruppe":::

1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** die entsprechende Rolle aus.

    :::image type="content" source="./media/management-groups-roles/add-role-assignment-page.png" alt-text="Seite „Rollenzuweisung hinzufügen“":::

1. Wählen Sie auf der Registerkarte **Mitglieder** die Option **+ Mitglieder auswählen** aus und weisen Sie die Rolle den entsprechenden Mitgliedern zu.

1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.


### <a name="assign-azure-roles-to-users-with-powershell"></a>Weisen Sie Benutzern Azure-Rollen mit PowerShell zu: 

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps).
2. Führen Sie die folgenden Befehle aus: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Melden Sie sich mit den Anmeldeinformationen des globalen Administrators an, wenn Sie dazu aufgefordert werden. 

    ![Screenshot: Aufforderung zur Anmeldung](./media/management-groups-roles/azurerm-sign-in.PNG)

4. Gewähren Sie die Berechtigungen der Rolle „Leser“, indem Sie den folgenden Befehl ausführen:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Verwenden Sie zum Entfernen der Rolle den folgenden Befehl: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Entfernen der erhöhten Zugriffsrechte 

Nachdem den Benutzern die Azure-Rollen zugewiesen wurden, sollte der Mandantenadministrator für sich selbst die Rolle „Benutzerzugriffsadministrator“ entfernen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure Active Directory Admin Center](https://aad.portal.azure.com) an.

2. Wählen Sie in der Navigationsliste **Azure Active Directory** aus, und klicken Sie dann auf **Eigenschaften**.

3. Legen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** den Schalter auf **Nein** fest.

4. Wählen Sie **Speichern** aus, um Ihre Einstellungen zu speichern.



## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite haben Sie gelernt, wie Sie Abonnements in Verwaltungsgruppen organisieren und Benutzern Rollen zuweisen. Verwandte Informationen

- [Berechtigungen in Microsoft Defender für Cloud](permissions.md)