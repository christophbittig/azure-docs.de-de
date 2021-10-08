---
title: Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal oder der REST-API die Zugriffsrechte für einen globalen Administrator zum Verwalten aller Abonnements und Verwaltungsgruppen in Azure Active Directory erhöhen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/10/2021
ms.author: rolyon
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 96e2f7176f85d5571ce73c4efdd592dd3964400d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124781516"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen

Als globaler Administrator in Azure Active Directory (Azure AD) haben Sie möglicherweise keinen Zugriff auf alle Abonnements und Verwaltungsgruppen in Ihrem Verzeichnis. In diesem Artikel erfahren Sie, wie Sie Ihren Zugriff auf alle Abonnements und Verwaltungsgruppen erhöhen.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>In welchen Fällen müssen Sie Ihren Zugriff erhöhen?

Wenn Sie globaler Administrator sind, müssen Sie unter Umständen folgende Aktionen ausführen:

- Wiedererlangen des Zugriffs auf ein Azure-Abonnement oder eine Verwaltungsgruppe, wenn ein Benutzer keinen Zugriff mehr hat
- Gewähren des Zugriffs auf ein Azure-Abonnement oder eine Verwaltungsgruppe für einen anderen Benutzer oder Sie selbst
- Anzeigen aller Azure-Abonnements oder Verwaltungsgruppen in einer Organisation
- Zulassen des Zugriffs auf alle Azure-Abonnements oder Verwaltungsgruppen für eine Automation-App (etwa eine App für die Rechnungsstellung oder Überwachung)

## <a name="how-does-elevated-access-work"></a>Wie erhöhe ich den Zugriff?

Azure AD und Azure-Ressourcen werden unabhängig voneinander geschützt. Das bedeutet, dass Azure AD-Rollenzuweisungen keinen Zugriff auf Azure-Ressourcen gewähren und Azure-Rollenzuweisungen keinen Zugriff auf Azure AD. Wenn Sie jedoch [globaler Administrator](../active-directory/roles/permissions-reference.md#global-administrator) in Azure AD sind, können Sie sich selbst Zugriff auf alle Azure-Abonnements und Verwaltungsgruppen in Ihrem Verzeichnis zuweisen. Verwenden Sie diese Funktion, wenn Sie keinen Zugriff auf Azure-Abonnementressourcen wie virtuelle Computer oder Speicherkonten haben und Sie Ihre globalen Administratorrechte verwenden möchten, um Zugriff auf diese Ressourcen zu erhalten.

Wenn Sie Ihre Zugriffsrechte erhöhen, wird Ihnen die Rolle [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) in Azure für den Stammbereich (`/`) zugewiesen. Auf diese Weise können Sie alle Ressourcen anzeigen und den Zugriff in jeder Abonnement- oder Verwaltungsgruppe im Verzeichnis zuweisen. Die Rollenzuweisung „Benutzerzugriffsadministrator“ kann mit Azure PowerShell, der Azure-Befehlszeilenschnittstelle oder der REST-API entfernt werden.

Sie sollten dieses erhöhte Zugriffsrecht entfernen, sobald Sie die Änderungen vorgenommen haben, die im Stammbereich erforderlich sind.

![Erhöhen von Zugriffsrechten](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure-Portal

### <a name="elevate-access-for-a-global-administrator"></a>Erhöhen der Zugriffsrechte für einen globalen Administrator

Führen Sie diese Schritte aus, um die Zugriffsrechte für einen globalen Administrator mit dem Azure-Portal zu erhöhen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure Active Directory Admin Center](https://aad.portal.azure.com) als globaler Administrator an.

    Wenn Sie Azure AD Privileged Identity Management verwenden, [aktivieren Sie Ihre Rollenzuweisung „Globaler Administrator“](../active-directory/privileged-identity-management/pim-how-to-activate-role.md).

1. Öffnen Sie **Azure Active Directory**.

1. Wählen Sie unter **Verwalten** die Option **Eigenschaften** aus.

   ![Auswählen des Azure Active Directory-Fensters „Eigenschaften“ (Screenshot)](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Wählen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** die Option **Ja**.

   ![Zugriffsverwaltung für Azure-Ressourcen – Screenshot](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Wenn Sie **Ja** auswählen, wird Ihnen in Azure RBAC im Stammbereich (/) die Rolle „Benutzerzugriffsadministrator“ zugewiesen. Dadurch erhalten Sie die Berechtigung, Rollen in allen Azure-Abonnements und Verwaltungsgruppen zuzuweisen, die diesem Azure AD-Verzeichnis zugeordnet sind. Diese Option ist nur für Benutzer verfügbar, denen in Azure AD die Rolle des globalen Administrators zugewiesen wurde.

   Wenn Sie **Nein** festlegen, wird die Rolle „Benutzerzugriffsadministrator“ in Azure RBAC aus Ihrem Benutzerkonto entfernt. Sie können dann keine Rollen mehr in allen Azure-Abonnements und Verwaltungsgruppen zuweisen, die diesem Azure AD-Verzeichnis zugeordnet sind. Sie können nur die Azure-Abonnements und Verwaltungsgruppen anzeigen und verwalten, für die Ihnen der Zugriff gewährt wurde.

    > [!NOTE]
    > Wenn Sie [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) verwenden, ändert die Deaktivierung Ihrer Rollenzuweisung den Umschalter **Zugriffsverwaltung für Azure-Ressourcen** nicht in **Nein**. Um den Zugriff mit den geringsten Rechten zu gewähren, empfehlen wir Ihnen das Festlegen dieses Umschalters auf **Nein**, bevor Sie Ihre Rollenzuweisung deaktivieren.
    
1. Klicken Sie auf **Speichern**, um Ihre Einstellung zu speichern.

   Diese Einstellung ist keine globale Eigenschaft und gilt nur für den aktuell angemeldeten Benutzer. Sie können den Zugriff nicht für alle Mitglieder der globalen Administratorrolle erhöhen.

1. Melden Sie sich ab und wieder an, um den Zugriff zu aktualisieren.

    Sie sollten jetzt auf alle Azure-Abonnements und Verwaltungsgruppen in Ihrem Verzeichnis zugreifen können. Wenn Sie den Bereich „Zugriffssteuerung (IAM)“ anzeigen, werden Sie feststellen, dass Ihnen die Rolle „Benutzerzugriffsadministrator“ im Stammbereich zugewiesen wurde.

   ![Screenshot: Zuweisen von Abonnementrollen im Stammbereich](./media/elevate-access-global-admin/iam-root.png)

1. Führen Sie die erforderlichen Änderungen für erhöhte Zugriffsrechte durch.

    Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Zuweisen von Azure-Rollen mit dem Azure-Portal](role-assignments-portal.md). Wenn Sie Privileged Identity Management verwenden, lesen Sie [Ermitteln von Azure-Ressourcen zur Verwaltung ](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) oder [Zuweisen von Azure-Ressourcenrollen](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Führen Sie die Schritte im folgenden Abschnitt aus, um die erhöhten Zugriffsrechte zu entfernen.

### <a name="remove-elevated-access"></a>Entfernen der erhöhten Zugriffsrechte

Führen Sie die folgenden Schritte aus, um die Zuweisung der Rolle „Benutzerzugriffsadministrator“ im Stammbereich (`/`) zu entfernen.

1. Melden Sie sich als derselbe Benutzer an, mit dem der Zugriff erhöht wurde.

1. Klicken Sie in der Navigationsliste auf **Azure Active Directory** und dann auf **Eigenschaften**.

1. Ändern Sie die Option **Zugriffsverwaltung für Azure-Ressourcen** wieder in **Nein**. Da es sich um eine benutzerspezifische Einstellung handelt, müssen Sie als der Benutzer angemeldet sein, der den Zugriff erhöht hat.

    Wenn Sie versuchen, die Rollenzuweisung „Benutzerzugriffsadministrator“ im Bereich „Zugriffssteuerung (IAM)“ zu entfernen, wird die folgende Meldung angezeigt. Um die Rollenzuweisung zu entfernen, müssen Sie den Umschalter wieder auf **Nein** festlegen oder Azure PowerShell, die Azure-Befehlszeilenschnittstelle oder die REST-API verwenden.

    ![Entfernen von Rollenzuweisungen im Stammbereich](./media/elevate-access-global-admin/iam-root-remove.png)

1. Melden Sie sich als „Globaler Administrator“ ab.

    Wenn Sie Privileged Identity Management verwenden, aktivieren Sie Ihre Rollenzuweisung „Globaler Administrator“.

    > [!NOTE]
    > Wenn Sie [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) verwenden, ändert die Deaktivierung Ihrer Rollenzuweisung den Umschalter **Zugriffsverwaltung für Azure-Ressourcen** nicht in **Nein**. Um den Zugriff mit den geringsten Rechten zu gewähren, empfehlen wir Ihnen das Festlegen dieses Umschalters auf **Nein**, bevor Sie Ihre Rollenzuweisung deaktivieren.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Auflisten der Rollenzuweisung im Stammbereich (/)

Verwenden Sie den Befehl [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment), um die Zuweisung der Rolle „Benutzerzugriffsadministrator“ für einen Benutzer im Stammbereich (`/`) aufzulisten.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Entfernen der erhöhten Zugriffsrechte

Führen Sie die folgenden Schritte aus, um die Zuweisung der Rolle „Benutzerzugriffsadministrator“ für Sie selbst oder einen anderen Benutzer im Stammbereich (`/`) zu entfernen.

1. Melden Sie sich als Benutzer an, der erhöhte Zugriffsrechte entfernen kann. Hierbei kann es sich um den Benutzer, der den Zugriff erhöht hat, oder einen anderen globalen Administrator handeln, der über erhöhte Zugriffsrechte im Stammbereich verfügt.

1. Verwenden Sie den Befehl [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment), um die Zuweisung der Rolle „Benutzerzugriffsadministrator“ zu entfernen.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="elevate-access-for-a-global-administrator"></a>Erhöhen der Zugriffsrechte für einen globalen Administrator

Führen Sie die folgenden grundlegenden Schritte aus, um die Zugriffsrechte für einen globalen Administrator über die Azure CLI zu erhöhen.

1. Rufen Sie mit dem Befehl [az rest](/cli/azure/reference-index#az_rest) den Endpunkt `elevateAccess` auf. Dadurch wird Ihnen die Rolle „Benutzerzugriffsadministrator“ im Stammbereich (`/`) zugewiesen.

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Führen Sie die erforderlichen Änderungen für erhöhte Zugriffsrechte durch.

    Informationen zum Zuweisen von Rollen finden Sie unter [Zuweisen von Azure-Rollen mithilfe der Azure CLI](role-assignments-cli.md).

1. Führen Sie die Schritte in einem späteren Abschnitt aus, um die erhöhten Zugriffsrechte zu entfernen.

### <a name="list-role-assignment-at-root-scope-"></a>Auflisten der Rollenzuweisung im Stammbereich (/)

Verwenden Sie den Befehl [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list), um die Zuweisung der Rolle „Benutzerzugriffsadministrator“ für einen Benutzer im Stammbereich (`/`) aufzulisten.

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Entfernen der erhöhten Zugriffsrechte

Führen Sie die folgenden Schritte aus, um die Zuweisung der Rolle „Benutzerzugriffsadministrator“ für Sie selbst oder einen anderen Benutzer im Stammbereich (`/`) zu entfernen.

1. Melden Sie sich als Benutzer an, der erhöhte Zugriffsrechte entfernen kann. Hierbei kann es sich um den Benutzer, der den Zugriff erhöht hat, oder einen anderen globalen Administrator handeln, der über erhöhte Zugriffsrechte im Stammbereich verfügt.

1. Verwenden Sie den Befehl [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete), um die Zuweisung der Rolle „Benutzerzugriffsadministrator“ zu entfernen.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST-API

### <a name="elevate-access-for-a-global-administrator"></a>Erhöhen der Zugriffsrechte für einen globalen Administrator

Führen Sie die folgenden grundlegenden Schritte aus, um mithilfe der REST-API die Zugriffsrechte für einen globalen Administrator zu erhöhen.

1. Rufen Sie mithilfe von REST `elevateAccess` auf. So erhalten Sie die Rolle „Benutzerzugriffsadministrator“ im Stammbereich (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Führen Sie die erforderlichen Änderungen für erhöhte Zugriffsrechte durch.

    Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Zuweisen von Azure-Rollen mit der REST-API](role-assignments-rest.md).

1. Führen Sie die Schritte in einem späteren Abschnitt aus, um die erhöhten Zugriffsrechte zu entfernen.

### <a name="list-role-assignments-at-root-scope-"></a>Auflisten der Rollenzuweisungen im Stammbereich (/)

Sie können alle Rollenzuweisungen für einen Benutzer im Stammbereich (`/`) auflisten.

- Rufen Sie [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) auf. Dabei entspricht `{objectIdOfUser}` der Objekt-ID des Benutzers, dessen Rollenzuweisungen Sie abrufen möchten.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Auflisten der Ablehnungszuweisungen im Stammbereich (/)

Sie können alle Ablehnungszuweisungen für einen Benutzer im Stammbereich (`/`) auflisten.

- Rufen Sie „GET denyAssignments“ auf. Hierbei entspricht `{objectIdOfUser}` der Objekt-ID des Benutzers, dessen Ablehnungszuweisungen Sie abrufen möchten.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Entfernen der erhöhten Zugriffsrechte

Beim Aufruf von `elevateAccess` erstellen Sie eine Rollenzuweisung für sich selbst. Um diese Berechtigungen zu widerrufen, müssen Sie die Rollenzuweisung „Benutzerzugriffsadministrator“ für sich selbst im Stammbereich (`/`) entfernen.

1. Rufen Sie [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) mit „User Access Administrator“ für `roleName` auf, um die Namens-ID der Rolle des Benutzerzugriffsadministrators abzurufen.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Speichern Sie die ID aus dem `name`-Parameter (in diesem Fall `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`).

1. Außerdem müssen Sie die Rollenzuweisung für den Verzeichnisadministrator im Verzeichnisbereich auflisten. Listen Sie alle Zuweisungen im Verzeichnisbereich für `principalId` des Verzeichnisadministrators auf, der den Aufruf mit erhöhten Zugriffsrechten vorgenommen hat. Dadurch werden alle Zuweisungen im Verzeichnis für „objectid“ aufgelistet.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Ein Verzeichnisadministrator sollte nicht über zu viele Zuweisungen verfügen. Wenn die obige Abfrage zu viele Zuweisungen zurückgibt, können Sie auch Abfragen für alle Zuweisungen auf der Verzeichnisbereichsebene durchführen und dann die Ergebnisse filtern: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Mit den oben aufgeführten Aufrufe wird eine Liste von Rollenzuweisungen zurückgegeben. Suchen Sie die Rollenzuweisung, bei der der Bereich `"/"` lautet und `roleDefinitionId` mit der Rollennamen-ID endet, die Sie in Schritt 1 ermittelt haben, und `principalId` der Objekt-ID des Verzeichnisadministrators entspricht. 
    
    Beispielrollenzuweisung:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Speichern Sie erneut die ID aus dem `name`-Parameter (in diesem Fall „11111111-1111-1111-1111-111111111111“).

1. Verwenden Sie abschließend die Rollenzuweisungs-ID, um die durch `elevateAccess` hinzugefügte Zuweisung zu entfernen:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="view-elevate-access-logs"></a>Anzeigen von Protokollen zu erhöhten Zugriffsrechten

Wenn das Zugriffsrecht erhöht wird, wird den Protokollen ein Eintrag hinzugefügt. Als globaler Administrator in Azure AD sollten Sie überprüfen, ob das Zugriffsrecht erhöht wurde und wer diesen Vorgang ausgeführt hat. Protokolleinträge zu erhöhten Zugriffsrechten werden nicht in den Standardaktivitätsprotokollen, sondern in den Aktivitätsprotokollen des Verzeichnisses angezeigt. In diesem Abschnitt werden verschiedene Möglichkeiten beschrieben, wie Sie die Protokolle zu erhöhten Zugriffsrechten anzeigen können.

### <a name="view-elevate-access-logs-using-the-azure-portal"></a>Anzeigen von Protokollen zu erhöhten Zugriffsrechten im Azure-Portal

1. Führen Sie die weiter oben in diesem Artikel beschriebenen Schritte aus, um Ihre Zugriffsrechte zu erhöhen.

1. Melden Sie sich als globaler Administrator am [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie **Monitor** > **Aktivitätsprotokoll**.

1. Ändern Sie die Liste **Aktivität** zu **Verzeichnisaktivität**.

1. Suchen Sie nach dem folgenden Vorgang, der auf eine Aktion zum Erhöhen von Zugriffsrechten hinweist.

    `Assigns the caller to User Access Administrator role`

    ![Screenshot: Verzeichnisaktivitätsprotokolle im Monitor.](./media/elevate-access-global-admin/monitor-directory-activity.png)

1. Führen Sie die weiter oben in diesem Artikel beschriebenen Schritte aus, um die erhöhten Zugriffsrechte wieder zu entfernen.

### <a name="view-elevate-access-logs-using-azure-cli"></a>Anzeigen von Protokollen zu erhöhten Zugriffsrechten mithilfe der Azure CLI

1. Führen Sie die weiter oben in diesem Artikel beschriebenen Schritte aus, um Ihre Zugriffsrechte zu erhöhen.

1. Verwenden Sie den Befehl [az login](/cli/azure/reference-index#az_login), um sich als globaler Administrator anzumelden.

1. Verwenden Sie den Befehl [az rest](/cli/azure/reference-index#az_rest), um folgenden Aufruf auszuführen. Dabei müssen Sie nach einem Datum filtern – wie im Beispielzeitstempel gezeigt – und einen Dateinamen angeben, unter dem die Protokolle gespeichert werden sollen.

    `url` ruft eine API auf, mit der die Protokolle in Microsoft.Insights abgerufen werden. Die Ausgabe wird in Ihrer Datei gespeichert.

    ```azurecli
    az rest --url "https://management.azure.com/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2021-09-10T20:00:00Z'" > output.txt
    ```

1.  Suchen Sie in der Ausgabedatei nach `elevateAccess`.

    Das Protokoll ähnelt der folgenden Ausgabe, in der Sie ablesen können, wann und durch wen die Aktion ausgeführt wurde.

    ```json
      "submissionTimestamp": "2021-08-27T15:42:00.1527942Z",
      "subscriptionId": "",
      "tenantId": "33333333-3333-3333-3333-333333333333"
    },
    {
      "authorization": {
        "action": "Microsoft.Authorization/elevateAccess/action",
        "scope": "/providers/Microsoft.Authorization"
      },
      "caller": "user@example.com",
      "category": {
        "localizedValue": "Administrative",
        "value": "Administrative"
      },
    ```

1. Führen Sie die weiter oben in diesem Artikel beschriebenen Schritte aus, um die erhöhten Zugriffsrechte wieder zu entfernen.

### <a name="delegate-access-to-a-group-to-view-elevate-access-logs-using-azure-cli"></a>Delegieren des Zugriffs an eine Gruppe zum Anzeigen von Protokollen zu erhöhten Zugriffsrechten mithilfe der Azure CLI

Wenn Sie die Protokolle zu erhöhten Zugriffsrechten regelmäßig abrufen möchten, können Sie den Zugriff an eine Gruppe delegieren und dann die Azure CLI verwenden.

1. Öffnen Sie **Azure Active Directory** > **Gruppen**.

1. Erstellen Sie eine neue Sicherheitsgruppe, und notieren Sie sich die Gruppenobjekt-ID.

1. Führen Sie die weiter oben in diesem Artikel beschriebenen Schritte aus, um Ihre Zugriffsrechte zu erhöhen.

1. Verwenden Sie den Befehl [az login](/cli/azure/reference-index#az_login), um sich als globaler Administrator anzumelden.

1. Verwenden Sie den Befehl [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), um der Gruppe die Rolle [Leser](built-in-roles.md#reader) zuzuweisen. Diese kann nur Protokolle auf Verzeichnisebene lesen, die sich unter `Microsoft/Insights` befinden.

    ```azurecli
    az role assignment create --assignee "{groupId}" --role "Reader" --scope "/providers/Microsoft.Insights"
    ```

1. Fügen Sie einen Benutzer hinzu, der Protokolle für die zuvor erstellte Gruppe liest.

1. Führen Sie die weiter oben in diesem Artikel beschriebenen Schritte aus, um die erhöhten Zugriffsrechte wieder zu entfernen.

Ein Benutzer in der Gruppe kann jetzt regelmäßig den Befehl [az rest](/cli/azure/reference-index#az_rest) ausführen, um Protokolle zu erhöhten Zugriffsrechten anzuzeigen.

```azurecli
az rest --url "https://management.azure.com/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2021-09-10T20:00:00Z'" > output.txt
```

## <a name="next-steps"></a>Nächste Schritte

- [Grundlegendes zu den verschiedenen Rollen](rbac-and-directory-admin-roles.md)
- [Zuweisen von Azure-Rollen mithilfe der REST-API](role-assignments-rest.md)
