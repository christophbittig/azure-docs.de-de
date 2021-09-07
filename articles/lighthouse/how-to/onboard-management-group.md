---
title: Durchführen eines Onboardings für alle Abonnements in einer Verwaltungsgruppe
description: Sie können eine Azure-Richtlinie bereitstellen, um alle Abonnements innerhalb einer Verwaltungsgruppe an einen Azure Lighthouse-Verwaltungsmandanten zu delegieren.
ms.date: 08/13/2021
ms.topic: how-to
ms.openlocfilehash: 07f2bb850120b1f27f090aec9d0d3f97d9643d05
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122342975"
---
# <a name="onboard-all-subscriptions-in-a-management-group"></a>Durchführen eines Onboardings für alle Abonnements in einer Verwaltungsgruppe

[Azure Lighthouse](../overview.md) ermöglicht die Delegierung von Abonnements und/oder Ressourcengruppen, aber nicht das Delegieren von [Verwaltungsgruppen](../../governance/management-groups/overview.md). Sie können jedoch eine [Azure-Richtlinie](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) bereitstellen, um alle Abonnements innerhalb einer Verwaltungsgruppe an einen Azure Lighthouse-Verwaltungsmandanten zu delegieren.

Die Richtlinie verwendet die Auswirkung [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists), um zu überprüfen, ob die einzelnen Abonnements in der Verwaltungsgruppe an den angegebenen Verwaltungsmandanten delegiert wurden. Wenn ein Abonnement noch nicht delegiert wurde, erstellt die Richtlinie die Azure Lighthouse-Zuweisung basierend auf den Werten, die Sie in den Parametern angeben. Sie können anschließend auf alle Abonnements in der Verwaltungsgruppe zugreifen, so als ob ein manuelles Onboarding durchgeführt worden wäre.

Beachten Sie bei Verwendung dieser Richtlinie Folgendes:

- Jedes Abonnement innerhalb der Verwaltungsgruppe erhält denselben Satz an Autorisierungen. Um verschiedenen Benutzern und Rollen Zugriff zu gewähren, müssen Sie ein manuelles Onboarding für das Abonnement durchführen.
- Obwohl für jedes Abonnement in der Verwaltungsgruppe ein Onboarding durchgeführt wird, können Sie über Azure Lighthouse keine Aktionen für die Ressource der Verwaltungsgruppe durchführen. Sie müssen Abonnements auswählen, an denen Sie arbeiten möchten, genau wie bei einem individuellen Onboarding.

Sofern unten nicht anders angegeben, müssen sämtliche dieser Schritte von einem Benutzer im Kundenmandanten ausgeführt werden, der über geeignete Berechtigungen verfügt.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch können [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), denselben Prozess verwenden.

## <a name="register-the-resource-provider-across-subscriptions"></a>Abonnementübergreifendes Registrieren des Ressourcenanbieters

Typischerweise wird im Rahmen des Onboardingprozesses für ein Abonnement der Ressourcenanbieter **Microsoft.ManagedServices** registriert. Wenn Sie mithilfe der Richtlinie ein Onboarding von Abonnements in einer Verwaltungsgruppe durchführen, muss der Ressourcenanbieter vorab registriert werden. Dies ist über einen Benutzer im Kundenmandanten möglich, dem die Rolle „Mitwirkender“ oder „Besitzer“ zugewiesen ist (oder mithilfe eines anderen Benutzers, der über Berechtigungen zum Ausführen des Vorgangs  `/register/action`  für den Ressourcenanbieter verfügt). Weitere Informationen finden Sie unter [Azure-Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md).

Sie können eine [Azure-Logik-App verwenden, um den Ressourcenanbieter automatisch abonnementübergreifend zu registrieren](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-customer). Diese Logik-App kann im Mandanten eines Kunden mit eingeschränkten Berechtigungen bereitgestellt werden, die es ihm ermöglichen, den Ressourcenanbieter in jedem Abonnement innerhalb einer Verwaltungsgruppe zu registrieren.

Wir stellen auch eine [Azure-Logik-App zur Verfügung, die im Mandanten des Dienstanbieters bereitgestellt werden kann](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-partner). Diese Logik-App kann den Ressourcenanbieter abonnementübergreifend in mehreren Mandanten zuweisen, indem der Logik-App eine [mandantenweite Administratoreinwilligung](../../active-directory/manage-apps/grant-admin-consent.md) erteilt wird. Zum Erteilen einer mandantenweiten Administratoreinwilligung müssen Sie sich als Benutzer anmelden, der zum Zustimmen im Namen der Organisation autorisiert ist. Beachten Sie, dass die Richtlinie auch dann einzeln für jede Verwaltungsgruppe bereitgestellt werden muss, wenn Sie diese Option verwenden, um den Anbieter mandantenübergreifend zu registrieren.

## <a name="create-your-parameters-file"></a>Erstellen einer Parameterdatei

Um die Richtlinie zu zuweisen, stellen Sie die Datei [deployLighthouseIfNotExistManagementGroup.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistManagementGroup.json) aus unserem Beispielrepository gemeinsam mit einer Parameterdatei [deployLighthouseIfNotExistsManagementGroup.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistsManagementGroup.parameters.json) zur Verfügung, die Sie mit Ihrem spezifischen Mandanten und den Zuweisungsdetails bearbeiten. Diese beiden Dateien enthalten die gleichen Details, die für das [Onboarding eines einzelnen Abonnements verwendet werden](onboard-customer.md).

Das folgende Beispiel zeigt eine Parameterdatei, die die Abonnements an den Mandanten „Relecloud Managed Services“ delegiert, mit Zugriff auf zwei principalIDs: eine für den Support auf Ebene 1 und eine für ein Automation-Konto, [das die delegateRoleDefinitionIds für verwaltete Identitäten im Kundenmandanten zuweisen kann](deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": { 
        "managedByName": { 
            "value": "Relecloud Managed Services" 
        }, 
        "managedByDescription": { 
            "value": "Relecloud provides managed services to its customers" 
        }, 
        "managedByTenantId": { 
            "value": "00000000-0000-0000-0000-000000000000" 
        }, 
        "managedByAuthorizations": { 
            "value": [ 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Tier 1 Support", 
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Automation Account - Full access", 
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9", 
                    "delegatedRoleDefinitionIds": [ 
                        "b24988ac-6180-42a0-ab88-20f7382dd24c", 
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293", 
                        "91c1777a-f3dc-4fae-b103-61d183457e46" 
                    ] 
                }                 
            ] 
        } 
    } 
} 
```

## <a name="assign-the-policy-to-a-management-group"></a>Zuweisen der Richtlinie zu einer Verwaltungsgruppe  

Nachdem Sie die Richtlinie bearbeitet haben, um Ihre Zuweisungen zu erstellen, können Sie sie auf Verwaltungsgruppenebene zuweisen. Weitere Informationen zum Zuweisen einer Richtlinie und zum Anzeigen der Ergebnisse in Bezug auf den Konformitätszustand finden Sie unter  [Schnellstart: Erstellen einer Richtlinienzuweisung](../../governance/policy/assign-policy-portal.md).

Das folgende PowerShell-Skript zeigt, wie Sie die Richtliniendefinition mithilfe der erstellten Vorlage und Parameterdatei unter der angegebenen Verwaltungsgruppe hinzufügen. Sie müssen die Zuweisungs- und Korrekturaufgabe für vorhandene Abonnements erstellen.

```azurepowershell-interactive
New-AzManagementGroupDeployment -Name <DeploymentName> -Location <location> -ManagementGroupId <ManagementGroupName> -TemplateFile <path to file> -TemplateParameterFile <path to parameter file> -verbose
```

## <a name="confirm-successful-onboarding"></a>Bestätigen des erfolgreichen Onboardings

Sie können auf verschiedene Weise bestätigen, dass das Onboarding der Abonnements erfolgreich war. Weitere Informationen finden Sie unter [Bestätigen des erfolgreichen Onboardings](onboard-customer.md#confirm-successful-onboarding).

Wenn Sie die Logik-App und die Richtlinie für Ihre Verwaltungsgruppe aktiv halten, werden auch alle neuen Abonnements, die der Verwaltungsgruppe hinzugefügt werden, integriert.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Onboarding von Kunden in Azure Lighthouse](onboard-customer.md).
- Informieren Sie sich über [Azure Policy](../../governance/policy/index.yml).
- Erfahren Sie mehr über [Azure Logic Apps](../../logic-apps/logic-apps-overview.md).
