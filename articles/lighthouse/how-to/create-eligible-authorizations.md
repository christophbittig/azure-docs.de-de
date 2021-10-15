---
title: Erstellen von berechtigten Autorisierungen
description: Beim Onboarding von Kunden in Azure Lighthouse können Sie es Benutzern in Ihrem Verwaltungsmandanten ermöglichen, ihre Rolle JIT-basiert (Just-In-Time) zu erhöhen.
ms.date: 09/08/2021
ms.topic: how-to
ms.openlocfilehash: b36c3a91c780c2d374ceafe14a671aaa135a2d3c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777828"
---
# <a name="create-eligible-authorizations"></a>Erstellen von berechtigten Autorisierungen

Beim Onboarding von Kunden in Azure Lighthouse erstellen Sie Autorisierungen, um Benutzern in Ihrem Verwaltungsmandanten integrierte Azure-Rollen zuzuweisen. Sie können auch berechtigte Autorisierungen erstellen, die [Azure Active Directory (Azure AD) Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) nutzen, damit Benutzer in Ihrem Verwaltungsmandanten ihre Rolle vorübergehend erhöhen können. So können Sie zusätzliche Berechtigungen auf JIT-Basis gewähren, damit Benutzer nur für einen festgelegten Zeitraum über diese Berechtigungen verfügen.

Durch das Erstellen von berechtigten Autorisierungen können Sie die Anzahl von permanenten Benutzerzuweisungen zu privilegierten Rollen verringern. Dadurch werden Sicherheitsrisiken im Zusammenhang mit privilegiertem Zugriff durch Benutzer in Ihrem Mandanten minimiert.

In diesem Thema wird die Funktionsweise von berechtigten Autorisierungen erläutert, und Sie erfahren, wie Sie diese Autorisierungen beim [Onboarding eines Kunden in Azure Lighthouse](onboard-customer.md) erstellen.

> [!IMPORTANT]
> Berechtigte Autorisierungen befinden sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="license-requirements"></a>Lizenzanforderungen

Zum Erstellen berechtigter Autorisierungen ist eine Enterprise Mobility + Security E5-Lizenz (EMS E5) oder Azure AD Premium P2-Lizenz erforderlich. Um die richtige Lizenz für Ihre Anforderungen zu ermitteln, lesen Sie [Vergleich: Allgemein verfügbare Features der Editionen Free, Basic und Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Nicht der Kundenmandant, sondern der Verwaltungsmandant muss über die EMS E5- oder Azure AD Premium P2-Lizenz verfügen.

Zusätzliche Kosten, die mit einer berechtigten Rolle verbunden sind, fallen nur während des Zeitraums an, in dem der Benutzer seinen Zugriff auf diese Rolle erhöht.

Weitere Informationen zu Lizenzen für Benutzer finden Sie unter [Lizenzanforderungen für die Verwendung von PIM](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="how-eligible-authorizations-work"></a>Funktionsweise von berechtigten Autorisierungen

Eine berechtigte Autorisierung definiert eine Rollenzuweisung, bei der der Benutzer die Rolle aktivieren muss, um privilegierte Aufgaben auszuführen. Wenn der Benutzer die berechtigte Rolle aktiviert, erhält er für den festgelegten Zeitraum den vollständigen Zugriff, der durch diese Rolle erteilt wird.

Benutzer im Kundenmandanten können vor dem Onboarding alle Rollenzuweisungen überprüfen. Dies umfasst auch die Rollenzuweisungen in berechtigten Autorisierungen.

Sobald ein Benutzer eine berechtigte Rolle erfolgreich aktiviert hat, verfügt er für einen vorkonfigurierten Zeitraum zusätzlich zu seinen dauerhaften Rollenzuweisungen für diesen Bereich über diese erhöhte Rolle im delegierten Bereich.

Administratoren im Verwaltungsmandanten können alle Privileged Identity Management-Aktivitäten im Überwachungsprotokoll im Verwaltungsmandanten überprüfen. Kunden können diese Aktionen im Azure-Aktivitätsprotokoll für das delegierte Abonnement anzeigen.

## <a name="eligible-authorization-elements"></a>Berechtigte Autorisierungselemente

Sie können eine berechtigte Autorisierung erstellen, wenn Sie Kunden mit Azure Resource Manager-Vorlagen integrieren, oder indem Sie ein Angebot für verwaltete Dienste im Azure Marketplace veröffentlichen. Jede berechtigte Autorisierung muss drei Elemente enthalten: den Benutzer, die Rolle und die Zugriffsrichtlinie.

### <a name="user"></a>Benutzer

Für jede berechtigte Autorisierung geben Sie die Prinzipal-ID für einen einzelnen Benutzer oder eine Azure AD-Gruppe im verwaltenden Mandanten an. Zusammen mit der Prinzipal-ID müssen Sie für jede Autorisierung einen Anzeigenamen Ihrer Wahl angeben.

Wenn eine Gruppe in einer berechtigten Autorisierung angegeben wird, kann jedes Mitglied dieser Gruppe seine individuellen Rechte für den Zugriff auf die Rolle gemäß der Zugriffsrichtlinie erhöhen.

Sie können berechtigte Autorisierungen nicht mit Dienstprinzipalen verwenden, da ein Dienstprinzipalkonto derzeit nicht seine Zugriffsrechte erhöhen und eine berechtigte Rolle verwenden kann. Sie können auch keine Berechtigungen mit `delegatedRoleDefinitionIds` verwenden, die ein Benutzerzugriffsadministrator [verwalteten Identitäten zuweisen](deploy-policy-remediation.md) kann.

> [!NOTE]
> Stellen Sie für jede berechtigte Autorisierung sicher, dass Sie auch eine permanente (aktive) Autorisierung für dieselbe Prinzipal-ID mit einer anderen Rolle erstellen, z. B. Leser (oder eine andere integrierte Azure-Rolle, die über Lesezugriff verfügt). Wenn Sie keine permanente Berechtigung für den Lesezugriff einbeziehen, kann der Benutzer seine Rolle nicht im Azure-Portal erhöhen.

### <a name="role"></a>Rolle

Jede berechtigte Autorisierung muss eine [integrierte Azure-Rolle](../../role-based-access-control/built-in-roles.md) enthalten, die der Benutzer auf Just-In-Time-Basis nutzen kann.

Die Rolle kann eine beliebige integrierte Azure-Rolle sein, die [für die delegierte Azure-Ressourcenverwaltung unterstützt](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) wird (mit Ausnahme des Benutzerzugriffsadministrators).

> [!IMPORTANT]
> Wenn Sie mehrere berechtigte Autorisierungen einbeziehen, die dieselbe Rolle verwenden, müssen für jede der berechtigten Autorisierungen die gleichen Zugriffsrichtlinieneinstellungen gelten.

### <a name="access-policy"></a>Zugriffsrichtlinie

Die Zugriffsrichtlinie definiert die Anforderungen der mehrstufigen Authentifizierung und die Dauer, für die die Rolle für einen Benutzer aktiviert wird, und ob genehmigende Personen erforderlich sind.

#### <a name="multifactor-authentication"></a>Mehrstufige Authentifizierung

Geben Sie an, ob eine [mehrstufige Azure AD-Authentifizierung](../../active-directory/authentication/concept-mfa-howitworks.md) erforderlich sein soll, damit eine berechtigte Rolle aktiviert werden kann.

#### <a name="maximum-duration"></a>Maximale Dauer

Definiert die Dauer, für die der Benutzer über die berechtigte Rolle verfügt. Der Mindestwert beträgt 30 Minuten und der Höchstwert 8 Stunden.

#### <a name="approvers"></a>Genehmigende Personen

Das Element der genehmigenden Personen ist optional. Wenn Sie es angeben, können Sie bis zu zehn Benutzer oder Benutzergruppen im verwaltenden Mandanten angeben, der Anforderungen eines Benutzers zum Aktivieren der berechtigten Rolle genehmigen oder verweigern kann.

Sie können kein Dienstprinzipalkonto als genehmigende Person verwenden. Außerdem können genehmigende Personen ihren eigenen Zugriff nicht genehmigen. Wenn eine genehmigende Person auch als Benutzer in einer berechtigten Autorisierung enthalten ist, muss eine andere genehmigende Person Zugriff gewähren, damit sie die Berechtigungen ihrer Rolle erhöhen kann.

Wenn Sie keine genehmigenden Personen einschließen, kann der Benutzer die berechtigte Rolle jederzeit aktivieren.

## <a name="create-eligible-authorizations-using-managed-services-offers"></a>Erstellen berechtigter Autorisierungen mithilfe von Angeboten für verwaltete Dienste

Um Ihre Kunden in Azure Lighthouse zu integrieren, können Sie Angebote für verwaltete Dienste in Azure Marketplace veröffentlichen. Beim [Erstellen Ihrer Angebote in Partner Center](publish-managed-services-offers.md) können Sie nun angeben, ob der **Zugriffstyp** für die jeweilige [Autorisierung](../../marketplace/create-managed-service-offer-plans.md#authorizations) **Aktiv** oder **Berechtigt** sein soll.

Wenn Sie **Berechtigt** auswählen, kann der Benutzer in Ihrer Autorisierung die Rolle gemäß der von Ihnen konfigurierten Zugriffsrichtlinie aktivieren. Sie müssen eine maximale Dauer zwischen 30 Minuten und 8 Stunden festlegen und angeben, ob Sie die mehrstufige Azure-Authentifizierung fordern. Sie können auch bis zu 10 genehmigende Personen hinzufügen, wenn Sie sie verwenden möchten, indem Sie einen Anzeigenamen und eine Prinzipal-ID für jede genehmigende Personen angeben.

Überprüfen Sie unbedingt die Details im Abschnitt [Berechtigte Autorisierungselemente](#eligible-authorization-elements), wenn Sie Ihre berechtigten Autorisierungen in Partner Center konfigurieren.

## <a name="create-eligible-authorizations-using-azure-resource-manager-templates"></a>Erstellen von berechtigten Autorisierungen mithilfe von Azure Resource Manager-Vorlagen

Für das Onboarding Ihres Kunden in Azure Lighthouse verwenden Sie eine [Azure Resource Manager-Vorlage mit einer entsprechenden Parameterdatei](onboard-customer.md#create-an-azure-resource-manager-template), die Sie anpassen. Welche Vorlage Sie auswählen, hängt davon ab, ob Sie das Onboarding für ein vollständiges Abonnement, eine Ressourcengruppe oder mehrere Ressourcengruppen innerhalb eines Abonnements durchführen.

Um beim Onboarding eines Kunden berechtigte Autorisierungen hinzuzufügen, verwenden Sie eine Vorlage aus dem [delegated-resource-management-eligible-authorizations-Abschnitt unseres Beispiel-Repositorys](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations). Wir stellen Vorlagen mit und ohne enthaltene genehmigende Personen bereit, damit Sie die bestgeeignete Vorlage für Ihr Szenario verwenden können.

|So führen Sie das Onboarding durch (mit berechtigten Autorisierungen)  |Verwenden Sie diese Azure Resource Manager-Vorlage:  |Ändern Sie außerdem diese Parameterdatei: |
|---------|---------|---------|
|Subscription   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.parameters.json)    |
|Abonnement (mit genehmigenden Personen)  |[subscription-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.json)  |[subscription-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.parameters.json)    |
|Resource group   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.parameters.json)    |
|Ressourcengruppe (mit genehmigenden Personen)  |[rg-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg-managing-tenant-approvers.json)  |[rg-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg-managing-tenant-approvers.parameters.json)    |
|Mehrere Ressourcengruppen in einem Abonnement   |[multiple-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.parameters.json)    |
|Mehrere Ressourcengruppen in einem Abonnement (mit genehmigenden Personen)  |[multiple-rg-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg-managing-tenant-approvers.json)  |[multiple-rg-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg-managing-tenant-approvers.parameters.json)    |

Nachfolgend ist die Vorlage **subscription-managing-tenant-approvers.json** gezeigt, die für das Onboarding eines Abonnements mit berechtigten Autorisierungen (inklusive genehmigender Personen) verwendet werden kann.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "type": "string",
            "metadata": {
                "description": "Specify a unique name for your offer"
            }
        },
        "mspOfferDescription": {
            "type": "string",
            "metadata": {
                "description": "Name of the Managed Service Provider offering"
            }
        },
        "managedByTenantId": {
            "type": "string",
            "metadata": {
                "description": "Specify the tenant id of the Managed Service Provider"
            }
        },
        "authorizations": {
            "type": "array",
            "metadata": {
                "description": "Specify an array of objects, containing tuples of Azure Active Directory principalId, a Azure roleDefinitionId, and an optional principalIdDisplayName. The roleDefinition specified is granted to the principalId in the provider's Active Directory and the principalIdDisplayName is visible to customers."
            }
        },
        "eligibleAuthorizations": {
            "type": "array",
            "metadata": {
                "description": "Provide the authorizations that will have just-in-time role assignments on customer environments with support for approvals from the managing tenant"
            }
        }
    },
        "variables": {
            "mspRegistrationName": "[guid(parameters('mspOfferName'))]",
            "mspAssignmentName": "[guid(parameters('mspOfferName'))]"
        },
        "resources": [
            {
                "type": "Microsoft.ManagedServices/registrationDefinitions",
                "apiVersion": "2020-02-01-preview",
                "name": "[variables('mspRegistrationName')]",
                "properties": {
                    "registrationDefinitionName": "[parameters('mspOfferName')]",
                    "description": "[parameters('mspOfferDescription')]",
                    "managedByTenantId": "[parameters('managedByTenantId')]",
                    "authorizations": "[parameters('authorizations')]",
                    "eligibleAuthorizations": "[parameters('eligibleAuthorizations')]"
                }
            },
            {
                "type": "Microsoft.ManagedServices/registrationAssignments",
                "apiVersion": "2020-02-01-preview",
                "name": "[variables('mspAssignmentName')]",
                "dependsOn": [
                    "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
                ],
                "properties": {
                    "registrationDefinitionId": "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
                }
            }
        ],
        "outputs": {
            "mspOfferName": {
                "type": "string",
                "value": "[concat('Managed by', ' ', parameters('mspOfferName'))]"
            },
            "authorizations": {
                "type": "array",
                "value": "[parameters('authorizations')]"
            },
            "eligibleAuthorizations": {
                "type": "array",
                "value": "[parameters('eligibleAuthorizations')]"
            }
        }
    }
```

### <a name="define-eligible-authorizations-in-your-parameters-file"></a>Definieren von berechtigten Autorisierungen in der Parameterdatei

Die [Beispielvorlage subscription-managing-tenant-approvers.Parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.parameters.json) kann verwendet werden, um beim Onboarding eines Abonnements Autorisierungen (einschließlich berechtigter Autorisierungen) zu definieren.

Alle berechtigten Autorisierungen müssen im `eligibleAuthorizations`-Parameter definiert werden. In diesem Beispiel wird eine berechtigte Autorisierung hinzugefügt.

Diese Vorlage enthält auch das `managedbyTenantApprovers`-Element, das eine `principalId` hinzufügt, die alle Versuche genehmigen muss, die berechtigten Rollen zu aktivieren, die im `eligibleAuthorizations`-Element definiert sind.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Relecloud Managed Services"
        },
        "mspOfferDescription": {
            "value": "Relecloud Managed Services"
        },
        "managedByTenantId": {
            "value": "<insert the managing tenant id>"
        },
        "authorizations": {
            "value": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM group"
                }
            ]
        }, 
        "eligibleAuthorizations":{
            "value": [
                {
                        "justInTimeAccessPolicy": {
                            "multiFactorAuthProvider": "Azure",
                            "maximumActivationDuration": "PT8H",
                            "managedByTenantApprovers": [ 
                                { 
                                    "principalId": "00000000-0000-0000-0000-000000000000", 
                                    "principalIdDisplayName": "PIM-Approvers" 
                                } 
                            ]
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "Tier 2 Support",
                        "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"

                }
            ]
        }
    }
}
```

Jeder Eintrag im `eligibleAuthorizations`-Parameter enthält [drei Elemente](#eligible-authorization-elements), die eine berechtigte Autorisierung definieren: `principalId`, `roleDefinitionId` und `justInTimeAccessPolicy`.

`principalId` gibt die ID für den Azure AD-Benutzer oder die Azure AD-Gruppe an, für den bzw. die diese berechtigte Autorisierung gilt.

`roleDefinitionId` enthält die Rollendefinitions-ID für eine [integrierte Azure-Rolle](../../role-based-access-control/built-in-roles.md), die der Benutzer auf Just-In-Time-Basis nutzen kann. Wenn Sie mehrere berechtigte Autorisierungen mit derselben `roleDefinitionId` verwenden, müssen diese Autorisierungen identische Einstellungen für `justInTimeAccessPolicy` haben.

`justInTimeAccessPolicy` gibt drei Elemente an:

- `multiFactorAuthProvider` kann entweder auf **Azure** festgelegt werden, um die mehrstufige Authentifizierung in Azure AD zu verwenden, oder auf **Keine**, wenn keine mehrstufige Authentifizierung erforderlich ist.
- `maximumActivationDuration` legt die Dauer fest, für die der Benutzer über die berechtigte Rolle verfügt. Dieser Wert muss im ISO 8601-Format angegeben werden. Der Mindestwert ist PT30M (30 Minuten), der Höchstwert lautet PT8H (8 Stunden). Der Einfachheit halber sollten Sie Werte nur in Inkrementen von einer halben Stunde verwenden (z. B. PT6H für 6 Stunden oder PT6H30M für 6,5 Stunden).
- `managedByTenantApprovers` ist optional. Wenn Sie sie einschließen, muss sie eine oder mehrere Kombinationen aus principalId und principalIdDisplayName enthalten, die eine Aktivierung der berechtigten Rolle genehmigen müssen.

Weitere Informationen zu diesen Elementen finden Sie oben im Abschnitt [Berechtigte Autorisierungselemente](#eligible-authorization-elements).

## <a name="elevation-process-for-users"></a>Rechteerweiterung für Benutzer

Nachdem Sie das Onboarding für einen Kunden in Azure Lighthouse durchgeführt haben, sind alle berechtigten Rollen, die Sie hinzugefügt haben, für den angegebenen Benutzer (oder für Benutzer in bestimmten Gruppen) verfügbar.

Benutzer können ihre Zugriffsrechte jederzeit erhöhen, indem sie im Azure-Portal auf der Seite **Meine Kunden** eine Delegierung auswählen und dann auf **Berechtigte Rollen verwalten** klicken. Anschließend führen sie die [Schritte zum Aktivieren der Rolle](../../active-directory/privileged-identity-management/pim-resource-roles-activate-your-roles.md) in Azure AD Privileged Identity Management aus.

:::image type="content" source="../media/manage-eligible-roles.png" alt-text="Screenshot der Schaltfläche „Berechtigte Rollen verwalten“ im Azure-Portal":::

Wenn genehmigende Personen angegeben wurden, hat der Benutzer erst dann Zugriff auf die Rolle, wenn die Genehmigung von einer bestimmten genehmigenden Person erteilt wurde. Alle genehmigenden Personen werden benachrichtigt, wenn eine Genehmigung angefordert wird, und der Benutzer kann die berechtigte Rolle erst verwenden, nachdem die Genehmigung erteilt wurde. Genehmigende Personen werden auch benachrichtigt, wenn dies geschieht. Weitere Informationen zum Genehmigungsprozess finden Sie unter [Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in Privileged Identity Management](../../active-directory/privileged-identity-management/pim-resource-roles-approval-workflow.md).

Nachdem die berechtigte Rolle aktiviert wurde, verfügt der Benutzer für die gesamte Dauer, die in der berechtigten Autorisierung festgelegt ist, über diese Rolle. Nach Ablauf dieses Zeitraums kann die Rolle erst wieder verwendet werden, wenn der Benutzer die Schritte zur Rechteerweiterung erneut ausführt, um seine Zugriffsrechte zu erhöhen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Onboarding von Kunden in Azure Lighthouse mithilfe von ARM-Vorlagen](onboard-customer.md).
- Erfahren Sie, wie Sie [Kunden mit Angeboten für verwaltete Dienste integrieren](publish-managed-services-offers.md).
- Erfahren Sie mehr über [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).
- Erfahren Sie mehr über [Mandanten, Benutzer und Rollen in Azure Lighthouse](../concepts/tenants-users-roles.md).
