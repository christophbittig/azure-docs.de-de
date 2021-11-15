---
title: Compliance des Azure Web PubSub-Diensts mit Azure Policy
description: Weisen Sie integrierte Richtlinien in Azure Policy zu, um die Compliance Ihrer Azure Web PubSub-Dienstressourcen zu überwachen.
author: JialinXin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/25/2021
ms.author: jixin
ms.openlocfilehash: a755b0bb9e01dff95c83bb2caf8d7773bb2d46d4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270679"
---
# <a name="audit-compliance-of-azure-web-pubsub-service-resources-using-azure-policy"></a>Überwachen der Compliance von Azure Web PubSub-Dienstressourcen mit Azure Policy

[Azure Policy](../governance/policy/overview.md) ist ein Dienst in Azure, mit dem Sie Richtlinien erstellen, zuweisen und verwalten können. Mit diesen Richtlinien werden unterschiedliche Regeln und Auswirkungen für Ihre Ressourcen erzwungen, damit diese stets mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel konform bleiben.

In diesem Artikel werden integrierte Richtlinien (Vorschau) für den Azure Web PubSub-Dienst vorgestellt. Mit diesen Richtlinien können Sie die Compliance neuer und vorhandener Web PubSub-Ressourcen überwachen.

Die Verwendung von Azure Policy ist gebührenfrei.

## <a name="built-in-policy-definitions"></a>Integrierte Richtliniendefinitionen

Die folgenden integrierten Richtlinien beschränken sich auf den Azure Web PubSub-Dienst:

[!INCLUDE [azure-policy-reference-policies-web-pubsub](../../includes/policy/reference/bycat/policies-web-pubsub.md)]

## <a name="assign-policy-definitions"></a>Zuweisen von Richtliniendefinitionen

* Weisen Sie Richtliniendefinitionen über das [Azure-Portal](../governance/policy/assign-policy-portal.md), die [Azure CLI](../governance/policy/assign-policy-azurecli.md), eine [Resource Manager-Vorlage](../governance/policy/assign-policy-template.md) oder die Azure Policy-SDKs zu.
* Beschränken Sie eine Richtlinienzuweisung auf eine Ressourcengruppe, ein Abonnement oder eine [Azure-Verwaltungsgruppe](../governance/management-groups/overview.md). Web PubSub-Richtlinienzuweisungen gelten für vorhandene und neue Web PubSub-Ressourcen innerhalb des Bereichs.
* Sie können die [Richtliniendurchsetzung](../governance/policy/concepts/assignment-structure.md#enforcement-mode) jederzeit aktivieren oder deaktivieren.

> [!NOTE]
> Nach dem Zuweisen oder Aktualisieren einer Richtlinie dauert es einen Moment, bis die Zuweisung auf die Ressourcen innerhalb des festgelegten Bereichs angewendet wird. Lesen Sie dazu auch die Informationen zu [Richtlinienauswertungsauslösern](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Überprüfen der Richtlinienkonformität

Greifen Sie über das Azure-Portal, die Azure-Befehlszeilentools oder die Azure Policy-SDKs auf die Informationen zur Konformität zu, die von Ihren Richtlinienzuweisungen generiert werden. Einzelheiten finden Sie unter [Abrufen von Compliancedaten von Azure-Ressourcen](../governance/policy/how-to/get-compliance-data.md).

Wenn eine Ressource nicht konform ist, kann das viele mögliche Ursachen haben. Wie Sie die Ursache bestimmen oder die verantwortliche Änderung finden, ist unter [Bestimmen der Nichtkonformität](../governance/policy/how-to/determine-non-compliance.md) beschrieben.

### <a name="policy-compliance-in-the-portal"></a>Richtlinienkonformität im Portal:

1. Wählen Sie **Alle Dienste** aus, und suchen Sie nach **Richtlinie**.
1. Wählen Sie **Compliance** aus.
1. Verwenden Sie die Filter, um die Konformitätszustände einzuschränken oder nach Richtlinien zu suchen.
   
    [ ![Richtlinienkonformität im Portal](./media/howto-monitor-azure-policy/azure-policy-compliance.png) ](./media/howto-monitor-azure-policy/azure-policy-compliance.png#lightbox)
2. Wählen Sie eine Richtlinie aus, um die Zusammenfassung der Konformitätsdetails und -ereignisse zu überprüfen. Wählen Sie anschließend bei Bedarf einen bestimmten Web PubSub-Dienst für die Ressourcencompliance aus.

### <a name="policy-compliance-in-the-azure-cli"></a>Richtlinienkonformität in der Azure CLI

Konformitätsdaten können auch über die Azure CLI abgerufen werden. Verwenden Sie zum Beispiel den Befehl [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) in der CLI, um die angewandten Richtlinien-IDs des Azure Web PubSub-Diensts abzurufen:

```azurecli
az policy assignment list --query "[?contains(displayName,'Web PubSub')].{name:displayName, ID:id}" --output table
```

Beispielausgabe:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure Web PubSub Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Führen Sie dann [az policy state list](/cli/azure/policy/state#az_policy_state_list) aus, um den Konformitätszustand aller Ressourcen einer bestimmten Ressourcengruppe im JSON-Format zurückzugeben:

```azurecli
az policy state list --g <resourceGroup>
```

Sie können auch [az policy state list](/cli/azure/policy/state#az_policy_state_list) ausführen, um den Compliancestatus einer bestimmten Web PubSub-Ressource im JSON-Format zurückzugeben:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/WebPubSub/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Definitionen](../governance/policy/concepts/definition-structure.md) und [Auswirkungen](../governance/policy/concepts/effects.md) in Azure Policy

* Erstellen einer [benutzerdefinierten Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md)

* Weitere Informationen zu [Governancefunktionen](../governance/index.yml) in Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/