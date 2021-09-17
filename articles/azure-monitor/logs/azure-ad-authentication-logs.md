---
title: Azure AD-Authentifizierung für Protokolle
description: Erfahren Sie, wie Sie Azure Active Directory-Authentifizierung (Azure AD) für Log Analytics in Azure Monitor aktivieren.
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 526312df5c1bbf5e21a4017177752fb4f476ae5c
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825269"
---
# <a name="azure-ad-authentication-for-logs"></a>Azure AD-Authentifizierung für Protokolle

Azure Monitor kann [Daten in Protokollen aus verschiedenen Quellen erfassen](data-platform-logs.md#data-collection), beispielsweise von Agents auf virtuellen Computern, aus Application Insights, Diagnoseeinstellungen für Azure-Ressourcen und der Datensammler-API.

Log Analytics-Agents verwenden einen Arbeitsbereichsschlüssel als Registrierungsschlüssel, um den anfänglichen Zugriff zu verifizieren und ein Zertifikat bereitzustellen, mit dem anschließend eine sichere Verbindung zwischen dem Agent und Azure Monitor hergestellt wird. Weitere Informationen finden Sie unter [Senden von Daten von Agents](data-security.md#2-send-data-from-agents). Die Datensammler-API verwendet denselben Arbeitsbereichsschlüssel für die [Zugriffsautorisierung](data-collector-api.md#authorization).

Diese Optionen können umständlich sein und ein Risiko darstellen, da Anmeldeinformationen – insbesondere Arbeitsbereichsschlüssel – nur schwer in großem Umfang verwaltet werden können. Sie können die lokale Authentifizierung deaktivieren und sicherstellen, dass ausschließlich Telemetriedaten in Azure Monitor erfasst werden, die unter Verwendung von verwalteten Identitäten und Azure Active Directory authentifiziert wurden. Diese Funktion erhöht die Sicherheit und Zuverlässigkeit der Telemetrie, die für wichtige operative und geschäftliche Entscheidungen verwendet wird.

Führen Sie die folgenden Schritte aus, um die Azure Active Directory-Integration für Azure Monitor-Protokolle zu aktivieren und die Abhängigkeit von gemeinsam genutzten Geheimnissen zu beseitigen.

1. Der Azure Monitor-Agent benötigt zwar keine Schlüssel, dafür aber eine [systemseitig verwaltete Identität](../agents/azure-monitor-agent-overview.md#security). Führen Sie eine [Migration von den Log Analytics-Agents zum Azure Monitor-Agent](../agents/azure-monitor-agent-migration.md) durch.
2. [Deaktivieren Sie die lokale Authentifizierung für Log Analytics-Arbeitsbereiche](#disable-local-authentication-for-log-analytics).
3. Stellen Sie mithilfe von [Azure AD-Authentifizierung für Application Insights (Vorschau)](../app/azure-ad-authentication.md) sicher, dass nur authentifizierte Telemetriedaten in Ihren Application Insights-Ressourcen erfasst werden.

## <a name="disable-local-authentication-for-log-analytics"></a>Deaktivieren der lokalen Authentifizierung für Log Analytics

Nachdem Sie die Abhängigkeit vom Log Analytics-Agent beseitigt haben, können Sie die lokale Authentifizierung für Log Analytics-Arbeitsbereiche deaktivieren. Dadurch können Sie ausschließlich Telemetriedaten erfassen und abfragen, die über Azure AD authentifiziert wurden.

Die Deaktivierung der lokalen Authentifizierung kann einige Funktionen einschränken, insbesondere:

- Vorhandene Log Analytics-Agents funktionieren nicht mehr, es wird nur der Azure Monitor-Agent unterstützt. Der Azure Monitor-Agent umfasst nicht alle Funktionen, die über den Log Analytics-Agent verfügbar sind (z. B. benutzerdefinierte Protokollsammlung, IIS-Protokollsammlung).
- Die Datensammler-API (Vorschauversion) bietet keine Unterstützung für die Azure AD-Authentifizierung und steht für die Erfassung von Daten nicht zur Verfügung.

Sie können die lokale Authentifizierung mithilfe von Azure Policy oder programmgesteuert über eine Azure Resource Manager-Vorlage, PowerShell oder die CLI deaktivieren.

### <a name="azure-policy"></a>Azure Policy

Die Azure-Richtlinie für „DisableLocalAuth“ untersagt Benutzern das Erstellen eines neuen Log Analytics-Arbeitsbereichs, wenn diese Eigenschaft nicht auf TRUE festgelegt ist. Der Richtlinienname lautet `Log Analytics Workspaces should block non-Azure Active Directory based ingestion`. Um diese Richtliniendefinition auf Ihr Abonnement anzuwenden, [erstellen Sie eine neue Richtlinienzuweisung, und weisen Sie die Richtlinie dann zu](../../governance/policy/assign-policy-portal.md). 

Im Folgenden finden Sie die Richtlinienvorlagendefinition:

```json
{
  "properties": {
    "displayName": "Log Analytics Workspaces should block non-Azure Active Directory based ingestion.",
    "policyType": "BuiltIn",
    "mode": "Indexed",
    "description": "Enforcing log ingestion to require Azure Active Directory authentication prevents unauthenticated logs from an attacker which could lead to incorrect status, false alerts, and incorrect logs stored in the system.",
    "metadata": {
      "version": "1.0.0",
      "category": "Monitoring"
    },
    "parameters": {
      "effect": {
        "type": "String",
        "metadata": {
          "displayName": "Effect",
          "description": "Enable or disable the execution of the policy"
        },
        "allowedValues": [
          "Deny",
          "Audit",
          "Disabled"
        ],
        "defaultValue": "Audit"
      }
    },
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.OperationalInsights/workspaces"
          },
          {
            "field": "Microsoft.OperationalInsights/workspaces/features.disableLocalAuth",
            "notEquals": "true"
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
  },
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e15effd4-2278-4c65-a0da-4d6f6d1890e2",
  "type": "Microsoft.Authorization/policyDefinitions",
  "name": "e15effd4-2278-4c65-a0da-4d6f6d1890e2"
}
```

### <a name="azure-resource-manager"></a>Azure Resource Manager

Die Eigenschaft `DisableLocalAuth` wird verwendet, um jede lokale Authentifizierung für Ihren Log Analytics-Arbeitsbereich zu deaktivieren. Wenn diese Eigenschaft auf TRUE festgelegt ist, wird die Verwendung der Azure AD-Authentifizierung für sämtliche Zugriffe erzwungen. 

Nachfolgend sehen Sie ein Azure Resource Manager-Vorlage, die Sie zum Deaktivieren der lokalen Authentifizierung verwenden können:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaces_name": {
            "defaultValue": "workspace-name",
            "type": "string"
        },
        "workspace_location": {
          "defaultValue": "region-name",
          "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaces_name')]",
            "location": "[parameters('workspace_location')]",
            "properties": {
                 "sku": {
                    "name": "PerGB2018"
                },
                "retentionInDays": 30,
                "features": {
                    "disableLocalAuth": false,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
}

```


### <a name="cli"></a>CLI

Die Eigenschaft `DisableLocalAuth` wird verwendet, um jede lokale Authentifizierung für Ihren Log Analytics-Arbeitsbereich zu deaktivieren. Wenn diese Eigenschaft auf TRUE festgelegt ist, wird die Verwendung der Azure AD-Authentifizierung für sämtliche Zugriffe erzwungen. 

Das folgende Beispiel zeigt, wie Sie mithilfe von CLI-Befehlen die lokale Authentifizierung deaktivieren können:

```azurecli
    az resource update --ids "/subscriptions/[Your subscription ID]/resourcegroups/[Your resource group]/providers/microsoft.operationalinsights/workspaces/[Your workspace name]--api-version "2021-06-01" --set properties.features.disableLocalAuth=True
```

### <a name="powershell"></a>PowerShell

Die Eigenschaft `DisableLocalAuth` wird verwendet, um jede lokale Authentifizierung für Ihren Log Analytics-Arbeitsbereich zu deaktivieren. Wenn diese Eigenschaft auf TRUE festgelegt ist, wird die Verwendung der Azure AD-Authentifizierung für sämtliche Zugriffe erzwungen. 

Das folgende Beispiel zeigt, wie Sie mithilfe von PowerShell-Befehlen die lokale Authentifizierung deaktivieren können:

```powershell
    $workspaceSubscriptionId = "[You subscription ID]"
    $workspaceResourceGroup = "[You resource group]"
    $workspaceName = "[Your workspace name]"
    $disableLocalAuth = $false
    
    # login
    Connect-AzAccount
    
    # select subscription
    Select-AzSubscription -SubscriptionId $workspaceSubscriptionId
    
    # get private link workspace resource
    $workspace = Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ResourceGroupName $workspaceResourceGroup -ResourceName $workspaceName -ApiVersion "2021-06-01"
    
    # set DisableLocalAuth
    $workspace.Properties.Features | Add-Member -MemberType NoteProperty -Name DisableLocalAuth -Value $disableLocalAuth -Force
    $workspace | Set-AzResource -Force
```

## <a name="next-steps"></a>Nächste Schritte
* [Azure AD-Authentifizierung für Application Insights (Vorschau)](../app/azure-ad-authentication.md)