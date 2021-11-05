---
title: Hinzufügen von Besitzern und Benutzern
description: Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs über das Azure-Portal oder PowerShell
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6edf548658d02ae3427fe5ac448dcc3e38a6b4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068758"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Der Zugriff in Azure DevTest Labs wird durch die [rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) gesteuert. Mithilfe von Azure RBAC können Sie Aufgaben in Ihrem Team in *Rollen* verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen. Drei dieser Azure-Rollen sind *Besitzer*, *DevTest Labs-Benutzer* und *Mitwirkender*. In diesem Artikel erfahren Sie, welche Aktionen von jeder der drei wichtigsten Azure-Rollen ausgeführt werden können. Sie erfahren, wie Sie Benutzer über das Portal oder über ein PowerShell-Skript zu einem Lab hinzufügen und Benutzer auf Abonnementebene hinzufügen.

## <a name="actions-that-can-be-performed-in-each-role"></a>Aktionen, die in jeder Rolle ausgeführt werden können
Es gibt drei wichtige Rollen, die Sie einem Benutzer zuweisen können:

* Besitzer
* DevTest Labs-Benutzer
* Mitwirkender

Die folgende Tabelle zeigt die Aktionen, die von Benutzern in jeder dieser Rollen ausgeführt werden können:

| **Aktionen, die Benutzer in dieser Rolle ausführen können** | **DevTest Labs-Benutzer** | **Besitzer** | **Mitwirkender** |
| --- | --- | --- | --- |
| **Lab-Aufgaben** | | | |
| Benutzer zu einem Lab hinzufügen |Nein |Ja |Nein |
| Kosteneinstellungen aktualisieren |Nein |Ja |Ja |
| **Grundlegende Aufgaben für virtuelle Computer** | | | |
| Benutzerdefinierte Images hinzufügen und entfernen |Nein |Ja |Ja |
| Formeln hinzufügen, aktualisieren und löschen |Ja |Ja |Ja |
| Aktivieren von Marketplace-Images |Nein |Ja |Ja |
| **Aufgaben für virtuelle Computer** | | | |
| Virtuelle Computer erstellen |Ja |Ja |Ja |
| Virtuelle Computer starten, beenden und löschen |Nur vom Benutzer erstellte virtuelle Computer |Ja |Ja |
| VM-Richtlinien aktualisieren |Nein |Ja |Ja |
| Datenträgern zu virtuellen Computern hinzufügen bzw. davon entfernen |Nur vom Benutzer erstellte virtuelle Computer |Ja |Ja |
| **Artefakt-Aufgaben** | | | |
| Artefaktrepositorys hinzufügen und entfernen |Nein |Ja |Ja |
| Artefakte anwenden |Ja |Ja |Ja |

> [!NOTE]
> Wenn ein Benutzer einen virtuellen Computer erstellt, wird diesem Benutzer automatisch die **Besitzer** -Rolle des virtuellen Computers zugewiesen.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Hinzufügen eines Besitzers oder Benutzers auf der Lab-Ebene
Besitzer und Benutzer können über das Azure-Portal auf der Lab-Ebene hinzugefügt werden. Bei einem Benutzer kann es sich um einen externen Benutzer mit gültigem [Microsoft-Konto (MSA)](./devtest-lab-faq.yml) handeln.
Die folgenden Schritte führen Sie durch den Prozess des Hinzufügens eines Besitzers oder Benutzers zu einem Lab in Azure DevTest Labs:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als [Benutzerzugangsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../role-based-access-control/built-in-roles.md#owner) an.

1. Öffnen Sie die gewünschte Ressourcengruppe und wählen Sie **DevTest Labs**.

1. Wählen Sie im Navigationsmenü **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen**.

    ![Seite „Zugriffssteuerung (IAM)“ mit geöffnetem Menü „Rollenzuweisung hinzufügen“](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Wählen Sie auf der Registerkarte **Rolle** die Rolle **Besitzer** oder **USER**.

    ![Seite „Rollenzuweisung hinzufügen“ mit ausgewählter Registerkarte „Rolle“](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** den Benutzer, dem Sie die gewünschte Rolle zuweisen möchten.

1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.


## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Hinzufügen eines externen Benutzers zu einem Lab mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können nicht nur Benutzer im Azure-Portal hinzufügen, sondern auch externe Benutzer über ein PowerShell-Skript zu Ihrem Lab hinzufügen. Ändern Sie im folgenden Beispiel die Werte der Parameter unter dem Kommentar **Values to change** .
Sie können die Werte `subscriptionId`, `labResourceGroup` und `labName` aus dem Labblatt im Azure-Portal abrufen.

> [!NOTE]
> Im Beispielskript wird davon ausgegangen, dass der angegebene Benutzer dem Active Directory als Gast hinzugefügt wurde. Wenn dies nicht der Fall ist, tritt ein Fehler auf. Um einem Lab einen Benutzer hinzuzufügen, der nicht in Active Directory ist, weisen Sie dem Benutzer im Azure-Portal wie im Abschnitt [Hinzufügen eines Besitzers oder Benutzers auf der Lab-Ebene](#add-an-owner-or-user-at-the-lab-level) beschrieben eine Rolle zu.   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Hinzufügen eines Besitzers oder Benutzers auf der Abonnementebene
Azure-Berechtigungen werden vom übergeordneten Bereich an den untergeordneten Bereich in Azure weitergegeben. Daher sind Besitzer eines Azure-Abonnements, das Labs enthält, automatisch Besitzer dieser Labs. Sie besitzen auch die virtuellen Computer und andere Ressourcen, die von den Lab-Benutzern und dem Azure DevTest Labs-Dienst erstellt werden. 

Sie können einem Lab über das entsprechende Blatt im [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)zusätzliche Besitzer hinzufügen. Der Verwaltungsbereich des hinzugefügten Besitzers ist jedoch weniger umfangreich als der Bereich des Abonnementbesitzers. Beispielsweise erhalten die hinzugefügten Besitzer keinen vollständigen Zugriff auf einige der Ressourcen, die vom DevTest Labs-Dienst im Abonnement erstellt werden. 

Um einen Besitzer zu einem Azure-Abonnement hinzuzufügen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als [Benutzerzugangsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../role-based-access-control/built-in-roles.md#owner) an.

1. Öffnen Sie die gewünschte Abonnementgruppe.

1. Wählen Sie im Navigationsmenü **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen**.

    ![Seite „Zugriffssteuerung (IAM)“ mit geöffnetem Menü „Rollenzuweisung hinzufügen“](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Wählen Sie auf der Registerkarte **Rolle** die Rolle **Besitzer**.

    ![Seite „Rollenzuweisung hinzufügen“ mit ausgewählter Registerkarte „Rolle“](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** den Benutzer, dem Sie die Eigentümerrolle zuweisen möchten.

1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
