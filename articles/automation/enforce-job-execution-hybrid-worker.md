---
title: Erzwingen der Auftragsausführung auf einem Azure Automation Hybrid Runbook Worker
description: In diesem Artikel erfahren Sie, wie Sie eine benutzerdefinierte Azure Policy-Definition verwenden, um die Auftragsausführung auf einem Azure Automation Hybrid Runbook Worker zu erzwingen.
services: automation
ms.subservice: process-automation
ms.date: 05/24/2021
ms.topic: conceptual
ms.openlocfilehash: ced119e7835f6c7fd7bf6aea34a10ebea4af3fc5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026277"
---
# <a name="use-azure-policy-to-enforce-job-execution-on-hybrid-runbook-worker"></a>Verwenden von Azure Policy zum Erzwingen der Auftragsausführung auf Hybrid Runbook Worker

Beim Starten eines Runbooks auf einem Hybrid Runbook Worker wird die Option **Ausführen auf** verwendet, mit der Sie den Namen einer Hybrid Runbook Worker-Gruppe beim Initiieren aus dem Azure-Portal mit dem Azure PowerShell oder der REST-API angeben können. Wenn eine Gruppe angegeben wird, ruft einer der Worker in dieser Gruppe das Runbook ab und führt es aus. Wenn Ihr Runbook diese Option nicht angibt, führt Azure Automation das Runbook in der Azure-Sandbox aus. 

Jeder Benutzer in Ihrer Organisation, der Mitglied des [Automation-Auftragsoperator](automation-role-based-access-control.md#automation-job-operator) oder höher ist, kann Runbookaufträge erstellen. Zum Verwalten der Runbookausführung für eine Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto können Sie [Azure Policy](../governance/policy/overview.md) verwenden. Dies trägt dazu bei, Organisationsstandards zu erzwingen und sicherzustellen, dass Ihre Automatisierungsaufträge von den dafür vorgesehenen Personen gesteuert und verwaltet werden und dass niemand ein Runbook in einer Azure-Sandbox ausführen kann, sondern nur auf Hybrid Runbook Workers. 

Eine benutzerdefinierte Azure Policy-Definition ist in diesem Artikel enthalten, um Sie bei der Steuerung dieser Aktivitäten mithilfe der folgenden Automation-REST-API-Vorgänge zu unterstützen. Dies gilt insbesondere in folgenden Fällen:

* [Auftrag erstellen](/rest/api/automation/job/create)
* [Auftragszeitplan erstellen](/rest/api/automation/jobschedule/create)
* [Webhook erstellen](/rest/api/automation/webhook/createorupdate)

Diese Richtlinie basiert auf der `runOn`-Eigenschaft. Die Richtlinie überprüft den Wert der Eigenschaft, die den Namen einer vorhandenen Hybrid Runbook Worker-Gruppe enthalten sollte. Wenn der Wert NULL ist, wird er als Erstellungsanforderung für den Auftrag, Auftragszeitplan oder Webhook für die Azure-Sandbox interpretiert, und die Anforderung wird abgelehnt.

## <a name="permissions-required"></a>Erforderliche Berechtigungen

Sie müssen ein Mitglied der Rolle [Besitzer](../role-based-access-control/built-in-roles.md#owner) auf Abonnementebene sein, um die Berechtigung für die Azure Policy-Ressourcen zu erhalten.

## <a name="create-and-assign-the-policy-definition"></a>Erstellen und Zuweisen der Richtliniendefinition

Hier erstellen wir die Richtlinienregel und weisen sie dann entweder einer Verwaltungsgruppe oder einem Abonnement zu und geben optional eine Ressourcengruppe im Abonnement an. Falls Sie mit der Richtliniensprache noch nicht vertraut sind, helfen Ihnen die Informationen zur Strukturierung der Richtliniendefinition weiter, die unter [Struktur von Richtliniendefinitionen](../governance/policy/concepts/definition-structure.md) angegeben sind.

1. Verwenden Sie den folgenden JSON-Codeausschnitt, um eine JSON-Datei mit dem Namen „AuditAutomationHRWJobExecution.json“ zu erstellen.

    ```json
    {
        "properties": {
            "displayName": "Enforce job execution on Automation Hybrid Runbook Worker",
            "description": "Enforce job execution on Hybrid Runbook Workers in your Automation account.",
            "mode": "all",
            "parameters": {
                "effectType": {
                    "type": "string",
                    "defaultValue": "Deny",
                    "allowedValues": [
                       "Deny",
                       "Disabled"
                    ],
                    "metadata": {
                        "displayName": "Effect",
                        "description": "Enable or disable execution of the policy"
                    }
                }
            },
            "policyRule": {
          "if": {
            "anyOf": [
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobs"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/webhooks"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/webhooks/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobSchedules"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobSchedules/runOn'))]",
                    "less": 1
                  }
                ]
              }
            ]
          },
          "then": {
            "effect": "[parameters('effectType')]"
          }
        }
      }
    }
    ```

2. Führen Sie den folgenden Azure PowerShell- oder Azure CLI-Befehl aus, um eine Richtliniendefinition mithilfe der Datei AuditAutomationHRWJobExecution.json zu erstellen.

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az policy definition create --name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' --display-name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' --description 'This policy enforces job execution on Automation account user Hybrid Runbook Workers.' --rules 'AuditAutomationHRWJobExecution.json' --mode All
    ```

    Der Befehl erstellt eine Richtliniendefinition mit dem Namen **Audit Enforce Jobs on Automation Hybrid Runbook Workers**. Weitere Informationen zu anderen Parametern, die Sie verwenden können, finden Sie unter [az policy definition create](/cli/azure/policy/definition#az_policy_definition_create).

    `az policy definition create` speichert die Richtliniendefinition standardmäßig im ausgewählten Abonnement des Sitzungskontexts bei Aufruf ohne Speicherortparameter. Um die Definition an einem anderen Speicherort zu speichern, verwenden Sie die folgenden Parameter:

    * **subscription** zum Speichern in einem anderen Abonnement. Dieser Parameter erfordert einen *GUID-Wert* für die Abonnement-ID oder einen *Zeichenfolgenwert* für den Namen des Abonnements.
    * **management-group** zum Speichern in einer Verwaltungsgruppe. Dies erfordert einen *Zeichenfolgenwert*.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -DisplayName 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' -Policy 'AuditAutomationHRWJobExecution.json'
    ```

    Der Befehl erstellt eine Richtliniendefinition mit dem Namen **Audit Enforce Jobs on Automation Hybrid Runbook Workers**. Weitere Informationen zu anderen Parametern, die Sie verwenden können, finden Sie unter [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

    `New-AzPolicyDefinition` speichert die Richtliniendefinition standardmäßig im ausgewählten Abonnement des Sitzungskontexts bei Aufruf ohne Speicherortparameter. Um die Definition an einem anderen Speicherort zu speichern, verwenden Sie die folgenden Parameter:

    * **SubscriptionId** zum Speichern in einem anderen Abonnement. Dies erfordert einen *GUID*-Wert.
    * **ManagementGroupName** zum Speichern in einer Verwaltungsgruppe. Dies erfordert einen *Zeichenfolgenwert*.

    ---

3. Nach dem Erstellen der Richtliniendefinition können Sie eine Richtlinienzuweisung erstellen. Führen Sie dazu die folgenden Befehle aus:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
    ```

    Der Parameter **scope** für `az policy assignment create` kann mit einer Verwaltungsgruppe, einem Abonnement, einer Ressourcengruppe oder einer einzelnen Ressource verwendet werden. Der Parameter verwendet einen vollständigen Ressourcenpfad. Das Muster für **scope** für die einzelnen Container ist unten aufgeführt. Ersetzen Sie `{rName}`, `{rgName}`, `{subId}` und `{mgName}` durch Ihre(n) Ressourcennamen, Ressourcengruppennamen, Abonnement-ID bzw. Namen der Verwaltungsgruppe. `{rType}` wird durch den **Ressourcentyp** der Ressource ersetzt, z. B. `Microsoft.Compute/virtualMachines` für eine VM.

    - Ressource: `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - Ressourcengruppe: `/subscriptions/{subID}/resourceGroups/{rgName}`
    - Abonnement: `/subscriptions/{subID}`
    - Verwaltungsgruppe: `/providers/Microsoft.Management/managementGroups/{mgName}`

    Sie können die ID der Azure Policy-Definition abrufen, indem Sie PowerShell mit dem folgenden Befehl verwenden:
    
    ```azurecli
    az policy definition show --name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers'
    ```
    
    Die ID für die Richtliniendefinition, die Sie erstellt haben, sollte in etwa wie im folgenden Beispiel aussehen:
    
    ```output
    "/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Enforce Jobs on Automation Hybrid Runbook Workers"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $rgName = Get-AzResourceGroup -Name 'ContosoRG'
    $Policy = Get-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers'
    New-AzPolicyAssignment -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -PolicyDefinition $Policy -Scope $rg.ResourceId
    ```

    Ersetzen Sie _ContosoRG_ durch den Namen Ihrer gewünschten Ressourcengruppe.

    Der Parameter **Scope** für `New-AzPolicyAssignment` kann mit einer Verwaltungsgruppe, einem Abonnement, einer Ressourcengruppe oder einer einzelnen Ressource verwendet werden. Der Parameter verwendet einen vollständigen Ressourcenpfad, den die Eigenschaft **ResourceId** in `Get-AzResourceGroup` zurückgibt. Das Muster für **Scope** sieht für jeden Container wie folgt aus. Ersetzen Sie `{rName}`, `{rgName}`, `{subId}` und `{mgName}` durch Ihre(n) Ressourcennamen, Ressourcengruppennamen, Abonnement-ID bzw. Namen der Verwaltungsgruppe.
    `{rType}` wird durch den **Ressourcentyp** der Ressource ersetzt, z. B. `Microsoft.Compute/virtualMachines` für eine VM.

    - Ressource: `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - Ressourcengruppe: `/subscriptions/{subId}/resourceGroups/{rgName}`
    - Abonnement: `/subscriptions/{subId}`
    - Verwaltungsgruppe: `/providers/Microsoft.Management/managementGroups/{mgName}`

    ---

4. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
5. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie die Option **Alle Dienste** auswählen und dann nach **Policy** suchen und die entsprechende Option auswählen.
6. Wählen Sie links auf der Seite die Option **Konformität** aus. Suchen Sie dann die von Ihnen erstellte Richtlinienzuweisung.

   :::image type="content" source="./media/enforce-job-execution-hybrid-worker/azure-policy-dashboard-policy-status.png" alt-text="Screenshot: Azure Policy-Dashboard":::

Wenn einer der Automation-REST-Vorgänge ohne Verweis auf einen Hybrid Runbook Worker im Anforderungstext ausgeführt wird, wird ein 403-Antwortcode mit einem Fehler zurückgegeben, der dem folgenden Beispiel ähnelt und angibt, dass der Vorgang die Ausführung in einer Azure-Sandbox versucht hat:

```rest
{
  "error": {
    "code": "RequestDisallowedByPolicy",
    "target": "Start_VMS",
    "message": "Resource 'Start_VMS' was disallowed by policy. Policy identifiers: '[{\"policyAssignment\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917\"},\"policyDefinition\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc\"}}]'.",
    "additionalInfo": [
      {
        "type": "PolicyViolation",
        "info": {
          "policyDefinitionDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "evaluationDetails": {
            "evaluatedExpressions": [
              {
                "result": "True",
                "expressionKind": "Field",
                "expression": "type",
                "path": "type",
                "expressionValue": "Microsoft.Automation/automationAccounts/jobs",
                "targetValue": "Microsoft.Automation/automationAccounts/jobs",
                "operator": "Equals"
              },
              {
                "result": "True",
                "expressionKind": "Value",
                "expression": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                "expressionValue": 0,
                "targetValue": 1,
                "operator": "Less"
              }
            ]
          },
          "policyDefinitionId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionName": "4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionEffect": "Deny",
          "policyAssignmentId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917",
          "policyAssignmentName": "fd5e2cb3842d4eefbc857917",
          "policyAssignmentDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "policyAssignmentScope": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG",
          "policyAssignmentParameters": {}
        }
      }
    ]
  }
}
```

Der versuchte Vorgang wird auch im Aktivitätsprotokoll des Automation-Kontos protokolliert, ähnlich wie im folgenden Beispiel.

:::image type="content" source="./media/enforce-job-execution-hybrid-worker/failed-job-activity-log-example.png" alt-text="Beispiel für ein Aktivitätsprotokoll für die fehlgeschlagene Auftragsausführung.":::

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Arbeit mit Runbooks finden Sie unter [Verwalten eines Runbooks in Azure Automation](manage-runbooks.md).