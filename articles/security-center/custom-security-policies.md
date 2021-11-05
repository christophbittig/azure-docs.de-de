---
title: Erstellen benutzerdefinierter Sicherheitsrichtlinien in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Benutzerdefinierte Azure-Richtliniendefinitionen, die von Microsoft Defender für Cloud überwacht werden.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.custom: ignite-fall-2021
ms.openlocfilehash: e596312807e2f6d5292c4910a7e2c3e9e7d75b3f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009983"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Erstellen von benutzerdefinierten Sicherheitsinitiativen und -richtlinien

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Cloud generiert Sicherheitsempfehlungen, um zum Schutz Ihrer Systeme und Ihrer Umgebung beizutragen. Diese Empfehlungen basieren auf bewährten Methoden der Branche, die in die generische Standardsicherheitsrichtlinie integriert sind, die für alle Kunden bereitgestellt wird. Sie können auch aus den Informationen stammen, die in Defender für Cloud zu Branchen- und gesetzlichen Standards enthalten sind.

Mit diesem Feature können Sie eigene *benutzerdefinierte* Initiativen hinzufügen. Sie erhalten dann Empfehlungen, wenn Ihre Umgebung die von Ihnen erstellten Richtlinien nicht einhält. Alle von Ihnen erstellten benutzerdefinierten Initiativen werden neben den integrierten Initiativen im Compliance-Dashboard angezeigt, wie im Tutorial [Verbessern der Einhaltung gesetzlicher Vorschriften](regulatory-compliance-dashboard.md) beschrieben wird.

Wie in der [Azure Policy-Dokumentation](../governance/policy/concepts/definition-structure.md#definition-location) erläutert, müsse Sie als Speicherort für Ihre benutzerdefinierte Initiative eine Verwaltungsgruppe oder ein Abonnement angeben. 

> [!TIP]
> Eine Übersicht über die wichtigsten Konzepte auf dieser Seite finden Sie unter [Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?](security-policy-concept.md).

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>So fügen Sie eine benutzerdefinierte Initiative zu Ihrem Abonnement hinzu 

1. Wählen Sie im Menü von Defender für Cloud die Option **Sicherheitsrichtlinie** aus.

1. Wählen Sie ein Abonnement oder eine Verwaltungsgruppe aus, dem bzw. der Sie eine benutzerdefinierte Initiative hinzufügen möchten.

    [![Auswählen eines Abonnements, für das Sie die benutzerdefinierte Richtlinie erstellen](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Damit Ihre benutzerdefinierten Initiativen in Defender für Cloud ausgewertet und angezeigt werden können, müssen Sie diese auf Abonnementebene (oder höher) hinzufügen. Es wird empfohlen, den größtmöglichen verfügbaren Bereich auszuwählen.

1. Klicken Sie auf der Seite „Sicherheitsrichtlinie“ unter „Ihre benutzerdefinierten Initiativen“ auf **Benutzerdefinierte Initiative hinzufügen**.

    [![Klicken auf „Benutzerdefinierte Initiative hinzufügen“](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Die folgende Seite wird angezeigt:

    ![Erstellen oder Hinzufügen einer Richtlinie](media/custom-security-policies/create-or-add-custom-policy.png)

1. Überprüfen Sie auf der Seite „Benutzerdefinierte Initiativen hinzufügen“ die Liste der benutzerdefinierten Richtlinien aus, die in Ihrer Organisation bereits erstellt wurden. Sehen Sie eine, die Sie Ihrem Abonnement zuweisen möchten, klicken Sie auf **Hinzufügen**. Wenn in der Liste keine Initiative enthalten ist, die Ihren Anforderungen entspricht, überspringen Sie diesen Schritt.

1. So erstellen Sie eine neue benutzerdefinierte Initiative

    1. Klicken Sie auf **Neu erstellen**.
    1. Geben Sie den Speicherort und den Namen der Definition ein.
    1. Wählen Sie die einzuschließenden Richtlinien aus, und klicken Sie auf **Hinzufügen**.
    1. Geben Sie die gewünschten Parameter ein.
    1. Klicken Sie auf **Speichern**.
    1. Klicken Sie auf der Seite „Benutzerdefinierte Initiativen hinzufügen“ auf „Aktualisieren“. Ihre neue Initiative wird als verfügbar angezeigt.
    1. Klicken Sie auf **Hinzufügen**, und weisen Sie sie Ihrem Abonnement zu.

    > [!NOTE]
    > Zum Erstellen neuer Initiativen sind Abonnementbesitzer-Anmeldeinformationen erforderlich. Weitere Informationen zu Azure-Rollen finden Sie unter [Berechtigungen in Microsoft Defender für Cloud](permissions.md).

    Ihre neue Initiative tritt in Kraft, und Sie können die Auswirkung auf die folgenden beiden Arten anzeigen:

    * Wählen Sie im Defender für Cloud-Menü die Option **Einhaltung gesetzlicher Bestimmungen** aus. Das Compliance-Dashboard wird geöffnet und zeigt Ihre neue benutzerdefinierte Initiative neben den integrierten Initiativen an.
    
    * Sie erhalten dann Empfehlungen, wenn Ihre Umgebung die von Ihnen definierten Richtlinien nicht einhält.

1. Um die resultierenden Empfehlungen für Ihre Richtlinie anzuzeigen, klicken Sie in der Randleiste auf **Empfehlungen**, um die Seite „Empfehlungen“ zu öffnen. Die Empfehlungen werden mit der Bezeichnung „Benutzerdefiniert“ angezeigt und sind innerhalb einer Stunde verfügbar.

    [![Benutzerdefinierte Empfehlungen](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurieren einer Sicherheitsrichtlinie in Azure Policy über die REST-API

Als Teil der nativen Integration in Azure Policy ermöglicht Ihnen Microsoft Defender für Cloud die Nutzung der REST-API von Azure Policy, um Richtlinienzuweisungen zu erstellen. Die folgenden Anweisungen führen Sie durch das Erstellen von Richtlinienzuweisungen sowie durch das Anpassen vorhandener Zuweisungen. 

Wichtige Konzepte in Azure Policy: 

- Eine **Richtliniendefinition** ist eine Regel. 

- Eine **Initiative** ist eine Sammlung von Richtliniendefinitionen (Regeln). 

- Eine **Zuweisung** ist eine Anwendung einer Initiative oder Richtlinie für einen bestimmten Bereich (Verwaltungsgruppe, Abonnement usw.). 

Defender für Cloud verfügt über die integrierte Initiative [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction), die alle zugehörigen Sicherheitsrichtlinien enthält. Um die Richtlinien von Defender für Cloud für Ihre Azure-Ressourcen zu bewerten, sollten Sie eine Zuweisung zur Verwaltungsgruppe oder zum Abonnement erstellen, die bzw. das Sie bewerten möchten.

In der integrierten Initiative sind alle Richtlinien von Defender für Cloud standardmäßig aktiviert. Sie können bestimmte Richtlinien aus der integrierten Initiative deaktivieren. Wenn Sie beispielsweise alle Defender für Cloud-Richtlinien mit Ausnahme von **Web Application Firewall** anwenden möchten, ändern Sie den Wert des Auswirkungsparameters der Richtlinie in **Deaktiviert**.

## <a name="api-examples"></a>API-Beispiele

Ersetzen Sie diese Variablen in den folgenden Beispielen:

- Geben Sie für **{scope}** den Namen der Verwaltungsgruppe oder des Abonnements ein, auf die oder das Sie die Richtlinie anwenden.
- Geben Sie für **{policyAssignmentName}** den Namen der relevanten Richtlinienzuweisung ein.
- Geben Sie für **{name}** Ihren Namen oder den Namen des Administrators ein, der die Richtlinienänderung genehmigt hat.

In diesem Beispiel wird veranschaulicht, wie Sie die integrierte Defender für Cloud-Initiative einem Abonnement oder einer Verwaltungsgruppe zuweisen:
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Microsoft Defender for Cloud", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

In diesem Beispiel wird veranschaulicht, wie Sie die integrierte Defender für Cloud-Initiative einem Abonnement zuweisen, wobei die folgenden Richtlinien deaktiviert sind: 

- Systemupdates („systemUpdatesMonitoringEffect“) 

- Sicherheitskonfigurationen („systemConfigurationsMonitoringEffect“) 

- Endpunktschutz (Endpoint Protection) („endpointProtectionMonitoringEffect“) 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Microsoft Defender for Cloud", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
In diesem Beispiel wird gezeigt, wie Sie eine Zuweisung entfernt wird:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Verbessern der benutzerdefinierten Empfehlungen mit ausführlichen Informationen

Die integrierten Empfehlungen, die mit Microsoft Defender für Cloud bereitgestellt werden, enthalten Details wie Schweregrade und Korrekturanweisungen. Wenn Sie Ihren benutzerdefinierten Empfehlungen diese Art von Informationen hinzufügen möchten, damit Sie im Azure-Portal oder wenn Sie auf Ihre Empfehlungen zugreifen angezeigt werden, müssen Sie die REST-API verwenden. 

Folgende zwei Arten von Informationen können Sie hinzufügen:

- **RemediationDescription**: Zeichenfolge
- **Severity**: Enumeration [Niedrig, Mittel, Hoch]

Die Metadaten sollten der Richtliniendefinition für eine Richtlinie hinzugefügt werden, die Teil der benutzerdefinierten Initiative ist. Sie sollten in der Eigenschaft „securityCenter“ vorliegen, wie hier gezeigt:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Im Folgenden finden Sie ein Beispiel für eine benutzerdefinierte Richtlinie mit der Eigenschaft metadata/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Ein weiteres Beispiel für die Verwendung der securityCenter-Eigenschaft finden Sie in [diesem Abschnitt der REST-API-Dokumentation](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie benutzerdefinierte Sicherheitsrichtlinien erstellt werden. 

Weitere verwandte Informationen finden Sie in den folgenden Artikeln: 

- [Übersicht über Sicherheitsrichtlinien](tutorial-security-policy.md)
- [Liste der integrierten Sicherheitsrichtlinien](./policy-reference.md)
