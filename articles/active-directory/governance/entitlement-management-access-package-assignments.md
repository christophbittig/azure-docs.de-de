---
title: Anzeigen, Hinzufügen und Entfernen von Zuweisungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung – Azure Active Directory
description: Erfahren Sie, wie Sie Zuweisungen für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung anzeigen, hinzufügen und entfernen.
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
ms.date: 10/05/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f944caecae6d35e680f5c5beb1a6e23fc422e698
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618086"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Anzeigen, Hinzufügen und Entfernen von Zuweisungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung

In der Azure AD-Berechtigungsverwaltung können Sie anzeigen, welche Benutzer Zugriffspaketen zugewiesen sind. Außerdem können Sie die jeweiligen Richtlinien und den Status anzeigen. Wenn ein Zugriffspaket eine geeignete Richtlinie enthält, können Sie dem Zugriffspaket auch direkt Benutzer zuweisen. In diesem Artikel wird beschrieben, wie Zuweisungen für Zugriffspakete angezeigt, hinzugefügt und entfernt werden.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Berechtigungsverwaltung zu verwenden und Benutzer zu Zugriffspaketen zuzuweisen, benötigen Sie eine der folgenden Lizenzen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-Lizenz

## <a name="view-who-has-an-assignment"></a>Anzeigen von Benutzern mit Zuweisung

**Erforderliche Rolle:** Unternehmensadministrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaket-Manager oder Zugriffspaketzuweisungs-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Zuweisungen**, um eine Liste der aktiven Zuweisungen anzuzeigen.

    ![Liste der Zuweisungen für ein Zugriffspaket](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Klicken Sie auf eine bestimmte Zuweisung, wenn Sie zusätzliche Details anzeigen möchten.

1. Wenn Sie die Liste der Zuweisungen mit nicht ordnungsgemäß bereitgestellten Ressourcenrollen anzeigen möchten, klicken Sie auf den Filterstatus, und wählen Sie **Übermitteln** aus.

    Zusätzliche Details zu Übermittlungsfehlern werden auf der Seite **Anforderungen** unter der Anforderung des jeweiligen Benutzers angezeigt.

1. Klicken Sie zum Anzeigen von abgelaufenen Zuweisungen auf den Filterstatus, und wählen Sie **Abgelaufen** aus.

1. Wenn Sie eine CSV-Datei aus der gefilterten Liste herunterladen möchten, klicken Sie auf **Herunterladen**.

## <a name="view-assignments-programmatically"></a>Programmgesteuertes Anzeigen von Zuweisungen
### <a name="view-assignments-with-microsoft-graph"></a>Anzeigen von Zuweisungen mit Microsoft Graph
Sie können auch mithilfe von Microsoft Graph Zuweisungen in einem Zugriffspaket abrufen.  Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte Berechtigung `EntitlementManagement.Read.All` oder `EntitlementManagement.ReadWrite.All` verfügt, kann die API aufrufen, um [„accessPackageAssignments“ aufzulisten](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true). Ein Identity Governance-Administrator kann Zugriffspakete aus mehreren Katalogen abrufen. Wenn einem Benutzer aber nur katalogspezifische delegierte Administratorrollen zugewiesen sind, muss mit der Anforderung ein Filter bereitgestellt werden, um ein bestimmtes Zugriffspaket anzugeben, z. B. `$filter=accessPackage/id eq 'a914b616-e04e-476b-aa37-91038f0b165b'`. Für eine Anwendung, die über die Anwendungsberechtigung `EntitlementManagement.Read.All` oder `EntitlementManagement.ReadWrite.All` verfügt, kann diese API ebenfalls verwendet werden.

### <a name="view-assignments-with-powershell"></a>Anzeigen von Zuweisungen mit PowerShell

Sie können diese Abfrage in PowerShell mit dem Cmdlet `Get-MgEntitlementManagementAccessPackageAssignment` durchführen, das in den [PowerShell-Cmdlets für Identity Governance von Microsoft Graph](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) (Modulversion 1.6.0 oder höher) enthalten ist. Für dieses Cmdlet wird ein Parameter als Zugriffspaket-ID verwendet, der in der Antwort des Cmdlets `Get-MgEntitlementManagementAccessPackage` enthalten ist.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.Read.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -ExpandProperty target -All -ErrorAction Stop
$assignments | ft Id,AssignmentState,TargetId,{$_.Target.DisplayName}
```

## <a name="directly-assign-a-user"></a>Direktes Zuweisen eines Benutzers 

In einigen Fällen möchten Sie bestimmte Benutzer möglicherweise einem Zugriffspaket direkt zuweisen, damit diese das Zugriffspaket nicht extra anfordern müssen. Damit dies möglich ist, muss das Zugriffspaket eine Richtlinie enthalten, die direkte Zuweisungen eines Administrator erlaubt.

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaketmanager oder Zugriffspaketzuweisungsmanager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie im Menü auf der linken Seite auf **Zuweisungen**.

1. Klicken Sie auf **Neue Zuweisung**, um die Seite „Hinzufügen von Benutzern zu einem Zugriffspaket“ zu öffnen.

    ![Zuweisungen: Benutzer einem Zugriffspaket hinzufügen](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1.  Wählen Sie in der Liste **Richtlinie auswählen** eine Richtlinie aus, von der künftige Anforderungen des Benutzers und der Lebenszyklus geregelt und verfolgt werden. Wenn für die ausgewählten Benutzer andere Richtlinieneinstellungen gelten sollen, können Sie auf **Neue Richtlinie erstellen** klicken, um eine neue Richtlinie hinzuzufügen.

1.  Nach dem Auswählen einer Richtlinie können Sie Benutzer hinzufügen und dann die Benutzer auswählen, denen Sie dieses Zugriffspaket unter der ausgewählten Richtlinie zuweisen möchten.

    > [!NOTE]
    > Wenn Sie eine Richtlinie mit Fragen auswählen, können Sie nur jeweils einen Benutzer zuweisen.

1. Legen Sie Datum und Uhrzeit fest, wann die Zuweisung der ausgewählten Benutzer beginnen und enden soll. Wenn Sie kein Enddatum angeben, werden die Lebenszykluseinstellungen der Richtlinie verwendet.

1.  Geben Sie zur Datenbankpflege optional eine Begründung für Ihre direkte Zuweisung an.

1.  Wenn die ausgewählte Richtlinie zusätzliche Informationen zur anfordernden Person enthält, klicken Sie auf **Fragen anzeigen**, um sie im Namen des Benutzers zu beantworten, und dann auf **Speichern**.  

     ![Zuweisungen: Klicken auf „Fragen anzeigen“](./media/entitlement-management-access-package-assignments/assignments-view-questions.png)

    ![Zuweisungen: Bereich mit Fragen](./media/entitlement-management-access-package-assignments/assignments-questions-pane.png)

1. Klicken Sie auf **Hinzufügen**, um die ausgewählten Benutzer dem Zugriffspaket direkt zuzuweisen.

    Klicken Sie nach kurzer Zeit auf **Aktualisieren**, um die Benutzer in der Liste „Zuweisungen“ anzuzeigen.
    
> [!NOTE]
> Beim Zuweisen von Benutzern zu einem Zugriffspaket müssen Administratoren überprüfen, ob die Benutzer basierend auf den vorhandenen Richtlinienanforderungen für dieses Zugriffspaket berechtigt sind. Andernfalls werden die Benutzer dem Zugriffspaket nicht erfolgreich zugewiesen. Wenn das Zugriffspaket eine Richtlinie enthält, die erfordert, dass Benutzeranforderungen genehmigt werden, können Benutzer dem Paket nicht direkt zugewiesen werden, ohne dass diese Zuweisung von den festgelegten genehmigenden Benutzern genehmigt wurde.

## <a name="directly-assign-any-user-preview"></a>Direktes Zuweisen eines Benutzers (Vorschau)
Mit der Azure AD-Berechtigungsverwaltung können Sie auch externe Benutzer direkt einem Zugriffspaket zuweisen, um die Zusammenarbeit mit Partnern zu vereinfachen. Hierzu muss das Zugriffspaket über eine Richtlinie verfügen, mit der Benutzer, die sich noch nicht in Ihrem Verzeichnis befinden, den Zugriff anfordern können.

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaketmanager oder Zugriffspaketzuweisungsmanager

1.  Wählen Sie im Azure-Portal die Option **Azure Active Directory** und dann **Identity Governance** aus.

1.  Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie dann das Zugriffspaket, dem Sie einen Benutzer hinzufügen möchten.

1.  Klicken Sie im Menü auf der linken Seite auf **Zuweisungen**.

1.  Wählen Sie **Neue Zuweisung** aus, um die Seite **Hinzufügen von Benutzern zu einem Zugriffspaket** zu öffnen.

1.  Wählen Sie in der Liste **Richtlinie auswählen** eine Richtlinie aus, welche die Einstellung **Für Benutzer, die sich nicht in Ihrem Verzeichnis befinden** zulässt.

1. Wählen Sie **Beliebiger Benutzer** aus. Sie können angeben, welche Benutzer diesem Zugriffspaket zugewiesen werden sollen.
    ![Zuweisungen: Benutzer einem Zugriffspaket hinzufügen](./media/entitlement-management-access-package-assignments/assignments-add-any-user.png)

1. Geben Sie den **Namen des Benutzers** (optional) und seine **E-Mail-Adresse** (erforderlich) ein.

    > [!NOTE]
    > - Der Benutzer, den Sie hinzufügen möchten, muss sich innerhalb des Gültigkeitsbereichs der Richtlinie befinden. Wenn Ihre Richtlinie beispielsweise auf **Bestimmte verbundene Organisationen** festgelegt ist, muss die E-Mail-Adresse des Benutzers aus einer der Domänen der ausgewählten Organisationen stammen. Wenn der Benutzer, den Sie hinzufügen möchten, die E-Mail-Adresse jen@*foo.com* hat, die Domäne der ausgewählten Organisation jedoch *bar.com* lautet, können Sie diesen Benutzer dem Zugriffspaket nicht hinzufügen.
    > - Ähnliches gilt auch, wenn Sie Ihre Richtlinie auf **Alle konfigurierten verbundenen Organisationen** festlegen. Dann muss die E-Mail-Adresse des Benutzers aus einer der konfigurierten verbundenen Organisationen stammen. Sonst kann der Benutzer dem Zugriffspaket nicht hinzugefügt werden.
    > - Wenn Sie dem Zugriffspaket einen Benutzer hinzufügen möchten, müssen Sie sicherstellen, dass Sie beim Konfigurieren Ihrer Richtlinie die Option **Alle Benutzer (alle verbundenen Organisationen und alle externen Benutzer)** auswählen.

1.  Legen Sie Datum und Uhrzeit fest, wann die Zuweisung der ausgewählten Benutzer beginnen und enden soll. Wenn Sie kein Enddatum angeben, werden die Lebenszykluseinstellungen der Richtlinie verwendet.
1.  Klicken Sie auf **Hinzufügen**, um die ausgewählten Benutzer dem Zugriffspaket direkt zuzuweisen.
1.  Klicken Sie nach kurzer Zeit auf **Aktualisieren**, um die Benutzer in der Liste „Zuweisungen“ anzuzeigen.

## <a name="directly-assigning-users-programmatically"></a>Programmgesteuertes direktes Zuweisen von Benutzern
### <a name="assign-a-user-to-an-access-package-with-microsoft-graph"></a>Zuweisen eines Benutzers zu einem Zugriffspaket mit Microsoft Graph
Sie können einen Benutzer auch direkt mithilfe von Microsoft Graph einem Zugriffspaket zuweisen.  Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte Berechtigung `EntitlementManagement.ReadWrite.All` oder eine Anwendung mit dieser Anwendungsberechtigung verfügt, kann die API aufrufen, um die [Anforderung „accessPackageAssignmentRequest“ zu erstellen](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true). Bei dieser Anforderung sollte der Wert der `requestType`-Eigenschaft `AdminAdd` lauten, und die `accessPackageAssignment`-Eigenschaft ist hierbei eine Struktur, die die `targetId` des zugewiesenen Benutzers enthält.

### <a name="assign-a-user-to-an-access-package-with-powershell"></a>Zuweisen eines Benutzers zu einem Zugriffspaket mit PowerShell

Sie können einem Benutzer ein Zugriffspaket in PowerShell mit dem Cmdlet `New-MgEntitlementManagementAccessPackageAssignmentRequest` zuweisen, das in den [PowerShell-Cmdlets für Identity Governance von Microsoft Graph](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) (Modulversion 1.6.0 oder höher) enthalten ist. Für dieses Cmdlet werden die folgenden Parameter verwendet:
* Zugriffspaket-ID, die in der Antwort des Cmdlets `Get-MgEntitlementManagementAccessPackage` enthalten ist
* Zuweisungsrichtlinien-ID des Zugriffspakets, die in der Antwort des Cmdlets `Get-MgEntitlementManagementAccessPackageAssignmentPolicy` enthalten ist
* Objekt-ID des Zielbenutzers

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -TargetId "a43ee6df-3cc5-491a-ad9d-ea964ef8e464"
```

Sie können einem Zugriffspaket in PowerShell auch mehrere Benutzer zuweisen, indem Sie das Cmdlet `New-MgEntitlementManagementAccessPackageAssignment` verwenden, das in den [PowerShell-Cmdlets für Identity Governance von Microsoft Graph](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) (Modulversion 1.6.1 oder höher) enthalten ist. Für dieses Cmdlet werden die folgenden Parameter verwendet:
* Zugriffspaket-ID, die in der Antwort des Cmdlets `Get-MgEntitlementManagementAccessPackage` enthalten ist
* Zuweisungsrichtlinien-ID des Zugriffspakets, die in der Antwort des Cmdlets `Get-MgEntitlementManagementAccessPackageAssignmentPolicy` enthalten ist
* Objekt-IDs der Zielbenutzer, entweder als Array mit Zeichenfolgen oder als Liste mit Benutzermitgliedern, die vom Cmdlet `Get-MgGroupMember` zurückgegeben wird

Wenn Sie beispielsweise sicherstellen möchten, dass alle Benutzer, die derzeit Mitglied einer Gruppe sind, auch über die Zuweisung eines Zugriffspakets verfügen, können Sie dieses Cmdlet zum Erstellen von Anforderungen für die Benutzer verwenden, die derzeit keine Zuweisungen haben.  Beachten Sie, dass mit diesem Cmdlet nur Zuweisungen erstellt werden. Hiermit werden keine Zuweisungen entfernt.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All,Directory.Read.All"
Select-MgProfile -Name "beta"
$members = Get-MgGroupMember -GroupId "a34abd69-6bf8-4abd-ab6b-78218b77dc15"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -RequiredGroupMember $members
```

## <a name="remove-an-assignment"></a>Entfernen einer Zuweisung

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaketmanager oder Zugriffspaketzuweisungsmanager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie im Menü auf der linken Seite auf **Zuweisungen**.
 
1. Aktivieren Sie das Kontrollkästchen neben dem Benutzer, dessen Zuweisung Sie aus dem Zugriffspaket entfernen möchten. 

1. Klicken Sie oben im linken Bereich auf die Schaltfläche **Entfernen**. 
 
    ![Zuweisungen: Entfernen des Benutzers aus dem Zugriffspaket](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Eine Benachrichtigung wird angezeigt, in der Sie darüber informiert werden, dass die Zuweisung entfernt wurde. 

## <a name="remove-an-assignment-programmatically"></a>Programmgesteuertes Entfernen einer Zuweisung
### <a name="remove-an-assignment-with-microsoft-graph"></a>Entfernen einer Zuweisung mit Microsoft Graph
Sie können die Zuweisung eines Benutzers zu einem Zugriffspaket auch entfernen, indem Sie Microsoft Graph verwenden.  Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte Berechtigung `EntitlementManagement.ReadWrite.All` oder eine Anwendung mit dieser Anwendungsberechtigung verfügt, kann die API aufrufen, um die [Anforderung „accessPackageAssignmentRequest“ zu erstellen](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true).  Bei dieser Anforderung sollte der Wert der `requestType`-Eigenschaft `AdminRemove` lauten, und die `accessPackageAssignment`-Eigenschaft ist hierbei eine Struktur, die die `id`-Eigenschaft für die Identifizierung des zu entfernenden `accessPackageAssignment`-Elements enthält.

### <a name="remove-an-assignment-with-powershell"></a>Entfernen einer Zuweisung mit PowerShell

Sie können die Zuweisung eines Benutzers mit dem Cmdlet `New-MgEntitlementManagementAccessPackageAssignmentRequest` entfernen, das in den [PowerShell-Cmdlets für Identity Governance von Microsoft Graph](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) (Modulversion 1.6.0 oder höher) enthalten ist.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -Filter "accessPackageId eq '9f573551-f8e2-48f4-bf48-06efbb37c7b8' and assignmentState eq 'Delivered'" -All -ErrorAction Stop
$toRemove = $assignments | Where-Object {$_.targetId -eq '76fd6e6a-c390-42f0-879e-93ca093321e7'}
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageAssignmentId $toRemove.Id -RequestType "AdminRemove"
```

## <a name="next-steps"></a>Nächste Schritte

- [Ändern der Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket](entitlement-management-access-package-request-policy.md)
- [Anzeigen von Berichten und Protokollen](entitlement-management-reports.md)
