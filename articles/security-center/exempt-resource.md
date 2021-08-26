---
title: Ausschließen einer Ressource, eines Abonnements, einer Verwaltungsgruppe und einer Sicherheitsbewertung aus einer Azure Security Center-Empfehlung
description: Erfahren Sie, wie Sie Regeln erstellen, um Abonnements oder Verwaltungsgruppen aus Sicherheitsempfehlungen auszuschließen, und wie Sie verhindern, dass sie sich auf Ihre Sicherheitsbewertung auswirken.
author: memildin
ms.author: memildin
ms.date: 05/12/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: f69b7174ec37a38fd972f53daaaf09776a279cea
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339960"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Ausschließen von Ressource und Empfehlungen aus der Sicherheitsbewertung 

Für jedes Sicherheitsteam hat die Sicherstellung, dass sich die Analysten auf die für die Organisation wichtigen Aufgaben und Incidents konzentrieren können, eine sehr hohe Priorität. Security Center verfügt über viele Features zum Anpassen der Informationen, und um sicherzustellen, dass Ihre Sicherheitsbewertung die Sicherheitsprioritäten Ihrer Organisation angemessen widerspiegelt. Die Option **Ausnahme** ist ein solches Feature.

Wenn Sie in Azure Security Center Ihre Sicherheitsempfehlungen untersuchen, überprüfen Sie unter anderem zunächst die Liste mit den betroffenen Ressourcen.

Es kann gelegentlich vorkommen, dass eine Ressource aufgeführt ist, die Ihrer Meinung nach nicht enthalten sein sollte. Auch kann es bisweilen passieren, dass eine Empfehlung in einem Bereich angezeigt wird, zu dem sie Ihrer Meinung nach nicht gehört. Die Ressource könnte mit einem Prozess korrigiert worden sein, der von Security Center nicht nachverfolgt wird. Möglicherweise ist die Empfehlung für ein bestimmtes Abonnement nicht geeignet. Oder vielleicht hat Ihr Unternehmen einfach beschlossen, die Risiken im Zusammenhang mit der jeweiligen Ressource oder Empfehlung zu akzeptieren.

In solchen Fällen können Sie eine Ausnahme für eine Empfehlung erstellen:

- Sie können **eine Ressource ausnehmen**, um sicherzustellen, dass sie in Zukunft nicht mehr als fehlerhafte Ressource aufgeführt wird und keine Auswirkung auf Ihre Sicherheitsbewertung hat. Die Ressource wird als nicht anwendbar aufgeführt, und als Grund wird „Ausgenommen“ mit der spezifischen, von Ihnen ausgewählten Begründung angezeigt.

- Sie können **ein Abonnement oder eine Verwaltungsgruppe ausnehmen**, um sicherzustellen, dass die Empfehlung keine Auswirkung auf Ihre Sicherheitsbewertung hat und in Zukunft nicht mehr für das Abonnement oder die Verwaltungsgruppe angezeigt wird. Das gilt sowohl für bereits vorhandene Ressourcen als auch für alle zukünftig erstellten Ressourcen. Die Empfehlung wird mit der spezifischen Begründung gekennzeichnet, die Sie für den ausgewählten Bereich auswählen.

## <a name="availability"></a>Verfügbarkeit

| Aspekt                          | Details                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status des Release:                  | Vorschau<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Preise:                        | Dies ist eine Premium-Richtlinienfunktion von Azure, die für Azure Defender-Kunden kostenlos zur Verfügung steht. Für andere Benutzer fallen hierfür in Zukunft unter Umständen Gebühren an.                                                                                                                                                                 |
| Erforderliche Rollen und Berechtigungen: | **Besitzer** oder **Mitwirkender bei Ressourcenrichtlinien** zum Erstellen einer Ausnahme<br>Zum Erstellen einer Regel benötigen Sie Berechtigungen zum Bearbeiten von Richtlinien in Azure Policy.<br>Weitere Informationen finden Sie unter [Azure RBAC-Berechtigungen in Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Einschränkungen:                    | Ausnahmen können nur für Empfehlungen erstellt werden, die in der Standardinitiative von Security Center, im [Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction) oder in einer der bereitgestellten Initiativen für gesetzliche Standards enthalten sind. Für Empfehlungen, die über benutzerdefinierte Initiativen generiert werden, können keine Ausnahmen gemacht werden. Weitere Informationen über die Beziehungen zwischen Richtlinien, Initiativen und Empfehlungen finden Sie [unter diesem Link](security-policy-concept.md). |
| Clouds:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National/Sovereign (Azure Government, Azure China 21Vianet)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definieren einer Ausnahme

Zur Feinabstimmung der Sicherheitsempfehlungen, die Security Center für Ihre Abonnements, Verwaltungsgruppe oder Ressourcen bereitstellt, können Sie eine Ausnahmeregel erstellen:

- Markieren Sie eine bestimmte **Empfehlung** als „Gemindert“ oder „Risiko akzeptiert“. Sie können Empfehlungsausnahmen für ein Abonnement, mehrere Abonnements oder eine gesamte Verwaltungsgruppe erstellen.
- Markieren Sie **eine oder mehrere Ressourcen** für eine bestimmte Empfehlung als „Gemindert“ oder „Risiko akzeptiert“.

> [!NOTE]
> Ausnahmen können nur für Empfehlungen erstellt werden, die in der Standardinitiative von Security Center, im Azure-Sicherheitsvergleichstest oder in einer der bereitgestellten Initiativen für gesetzliche Standards enthalten sind. Für Empfehlungen, die von benutzerdefinierten Initiativen generiert werden, die Ihren Abonnements zugewiesen sind, können keine Ausnahmen gemacht werden. Weitere Informationen über die Beziehungen zwischen Richtlinien, Initiativen und Empfehlungen finden Sie [unter diesem Link](security-policy-concept.md).

> [!TIP]
> Sie können auch Ausnahmen mithilfe der API erstellen. Ein JSON-Beispiel und eine Erläuterung der relevanten Strukturen finden Sie unter [Azure Policy-Ausnahmenstruktur](../governance/policy/concepts/exemption-structure.md).

So erstellen Sie eine Ausnahmeregel:

1. Öffnen Sie die Seite mit den Empfehlungsdetails für die jeweilige Empfehlung.

1. Wählen Sie auf der Symbolleiste am oberen Rand der Seite **Ausnahme** aus.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Erstellen Sie eine Ausnahmeregel für eine Empfehlung, von der ein Abonnement oder eine Verwaltungsgruppe ausgenommen werden soll.":::

1. Im Bereich **Ausnahme**:
    1. Wählen Sie den Bereich für diese Ausnahmeregel aus:
        - Wenn Sie eine Verwaltungsgruppe auswählen, werden alle Abonnements innerhalb dieser Gruppe von der Empfehlung ausgenommen.
        - Wenn Sie diese Regel erstellen, um eine oder mehrere Ressourcen von der Empfehlung auszuschließen, wählen Sie „Ausgewählte Ressourcen“ und die relevanten Ressourcen in der Liste aus.

    1. Geben Sie einen Namen für diese Ausnahmeregel ein.
    1. Legen Sie optional ein Ablaufdatum fest.
    1. Wählen Sie die Kategorie für die Ausnahme aus:
        - **Gelöst durch Drittanbieter (Gemindert)** : Wenn Sie einen Drittanbieterdienst verwenden, den Security Center nicht identifiziert hat. 

            > [!NOTE]
            > Wenn Sie eine Empfehlung als gemindert ausnehmen, erhalten Sie keine Punkte für Ihre Sicherheitsbewertung. Da jedoch für die fehlerhaften Ressourcen keine Punkte *entfernt* werden, ist das Ergebnis, dass Ihre Bewertung steigt.

        - **Risiko akzeptiert (Verzicht)** : Wenn Sie sich entschieden haben, das Risiko zu akzeptieren, diese Empfehlung nicht zu mindern.
    1. Geben Sie optional eine Beschreibung ein.
    1. Wählen Sie **Erstellen** aus.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Schritte zum Erstellen einer Ausnahmeregel, um Ihr Abonnement oder Ihre Verwaltungsgruppe von einer Empfehlung auszuschließen.":::

    Wenn die Ausnahme wirksam wird (dies kann bis zu 30 Minuten dauern):
    - Die Empfehlung oder Ressourcen haben keine Auswirkungen auf Ihre Sicherheitsbewertung.
    - Wenn Sie bestimmte Ressourcen ausgenommen haben, werden sie auf der Registerkarte **Nicht anwendbar** der Seite mit den Empfehlungsdetails aufgelistet.
    - Wenn Sie eine Empfehlung ausgenommen haben, wird sie standardmäßig auf der Empfehlungenseite in Security Center ausgeblendet. Dies liegt daran, dass die Standardoptionen des Filters **Empfehlungsstatus** auf dieser Seite **Nicht anwendbar**-Empfehlungen ausschließen sollen. Dasselbe gilt, wenn Sie alle Empfehlungen in einer Sicherheitssteuerung ausschließen.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Standardfilter auf der Azure Security Center-Empfehlungenseite blenden die nicht anwendbaren Empfehlungen und Sicherheitssteuerungen aus.":::

    - In der Informationsleiste am oberen Rand der Seite mit den Empfehlungsdetails wird die Anzahl von ausgeschlossenen Ressourcen aktualisiert:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Anzahl von ausgeschlossenen Ressourcen.":::

1. Öffnen Sie die Registerkarte **Nicht anwendbar**, um Ihre ausgeschlossenen Ressourcen anzuzeigen:

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Ändern einer Ausnahme.":::

    Der Grund für die einzelnen Ausnahmen ist jeweils in der Tabelle angegeben (1).

    Wählen Sie über die Auslassungszeichen („...“) das Menü aus (wie unter Punkt 2 dargestellt), um eine Ausnahme zu ändern oder zu löschen.

1. Wenn Sie alle Ausnahmeregeln für Ihr Abonnement überprüfen möchten, wählen Sie in der Informationsleiste **Ausnahmen anzeigen** aus:

    > [!IMPORTANT]
    > Um die spezifischen Ausnahmen anzuzeigen, die für eine Empfehlung relevant sind, filtern Sie die Liste nach dem relevanten Bereich und dem Namen der Empfehlung.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy-Seite „Ausnahme“":::

    > [!TIP]
    > Alternativ [können Sie mit Azure Resource Graph Empfehlungen mit Ausnahmen](#find-recommendations-with-exemptions-using-azure-resource-graph) finden.

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Überwachen von in Ihren Abonnements erstellten Ausnahmen

Wie bereits auf dieser Seite erläutert, sind Ausnahmeregeln ein leistungsfähiges Tool, das eine präzise Kontrolle über die Empfehlungen bietet, die sich auf Ressourcen in Ihren Abonnements und Verwaltungsgruppen auswirken. 

Damit Sie nachverfolgen können, wie Ihre Benutzer diese Funktion nutzen, haben wir eine Azure Resource Manager-Vorlage (ARM) erstellt, die ein Logik-App-Playbook und alle notwendigen API-Verbindungen bereitstellt, um Sie zu benachrichtigen, wenn eine Ausnahme erstellt wurde.

- Weitere Informationen zum Playbook finden Sie im Tech Community-Blogbeitrag [How to keep track of Resource Exemptions in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580) (Nachverfolgen von Ressourcenausnahmen in Azure Security Center).
- Sie finden die ARM-Vorlage im [Azure Security Center-GitHub-Repository](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption).
- [Verwenden Sie diesen automatisierten Prozess](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json), um alle erforderlichen Komponenten bereitzustellen.

## <a name="use-the-inventory-to-find-resources-that-have-exemptions-applied"></a>Verwenden Sie den Bestand, um Ressourcen zu suchen, für die Ausnahmen angewendet wurden.

Auf der Seite „Ressourcenbestand“ von Azure Security Center können Sie auf einer Seite den gesamten Sicherheitsstatus der Ressourcen anzeigen, die Sie mit Azure Security Center verbunden haben. Weitere Informationen finden Sie unter [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md).

Die Bestandsseite enthält viele Filter, mit denen Sie die Liste der Ressourcen auf diejenigen eingrenzen können, die für ein bestimmtes Szenario am wichtigsten sind. Ein solcher Filter ist die **Contains-Ausnahme**. Verwenden Sie diesen Filter, um alle Ressourcen zu suchen, die von einer oder mehrere Empfehlungen ausgenommen wurden.

:::image type="content" source="media/exempt-resource/inventory-filter-exemptions.png" alt-text="Seite für den Ressourcenbestand in Security Center und Filter zum Suchen von Ressourcen mit Ausnahmen":::


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Suchen von Empfehlungen mit Ausnahmen mit Azure Resource Graph

Azure Resource Graph (ARG) bietet mit zuverlässigen Funktionen zum Filtern, Gruppieren und Sortieren sofortigen Zugriff auf Ressourceninformationen in Ihren Cloudumgebungen. Es ist eine schnelle und effiziente Möglichkeit, Informationen über Azure-Abonnements programmgesteuert oder aus dem Azure-Portal heraus abzufragen.

So zeigen Sie alle Empfehlungen mit Ausnahmeregeln an:

1. Öffnen Sie den **Azure Resource Graph-Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Die Empfehlungsseite des Azure Resource Graph-Explorer** wird gestartet." :::

1. Geben Sie die folgende Abfrage ein, und wählen Sie **Abfrage ausführen** aus.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Weitere Informationen finden Sie auf den folgenden Seiten:
- [Erfahren Sie mehr über Azure Resource Graph](../governance/resource-graph/index.yml).
- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)
- [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>FAQ: Ausnahmeregeln

- [Was geschieht, wenn eine Empfehlung in mehreren Richtlinieninitiativen vorliegt?](#what-happens-when-one-recommendation-is-in-multiple-policy-initiatives)
- [Gibt es Empfehlungen, die keine Ausnahme unterstützen?](#are-there-any-recommendations-that-dont-support-exemption)

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Was geschieht, wenn eine Empfehlung in mehreren Richtlinieninitiativen vorliegt?

Manchmal liegt eine Sicherheitsempfehlung in mehreren Richtlinieninitiativen vor. Wenn mehrere Instanzen derselben Empfehlung demselben Abonnement zugewiesen sind, und Sie eine Ausnahme für die Empfehlung erstellen, wirkt sich dies auf alle Initiativen aus, für die Sie die Berechtigung zum Bearbeiten haben. 

Beispielsweise ist die Empfehlung **** Teil der Standardrichtlinieninitiative, die allen Azure-Abonnements von Azure Security Center zugewiesen wird. Sie liegt also auch in xxxxx vor.

Wenn Sie versuchen, eine Ausnahme für diese Empfehlung zu erstellen, wird eine der beiden folgenden Meldungen angezeigt:

- Wenn Sie für beide Initiativen über die erforderlichen Berechtigungen zum Bearbeiten verfügen, sehen Sie Folgendes:

    *Diese Empfehlung ist in mehreren Richtlinieninitiativen enthalten: [durch Kommas getrennte Initiativennamen]. Ausnahmen werden für alle erstellt.*  

- Wenn Sie nicht für beide Initiativen über die erforderlichen Berechtigungen verfügen, wird stattdessen diese Meldung angezeigt:

    *Sie verfügen über eingeschränkte Berechtigungen zum Anwenden der Ausnahme auf alle Richtlinieninitiativen. Die Ausnahmen werden nur für die Initiativen mit ausreichenden Berechtigungen erstellt.*

### <a name="are-there-any-recommendations-that-dont-support-exemption"></a>Gibt es Empfehlungen, die keine Ausnahme unterstützen?

Diese Empfehlungen unterstützen keine Ausnahme:

- Für Container müssen CPU- und Arbeitsspeicherlimits erzwungen werden
- Privilegierte Container müssen vermieden werden
- Containerimages dürfen nur aus vertrauenswürdigen Registrierungen bereitgestellt werden
- Container dürfen nur an zulässigen Ports lauschen
- Dienste dürfen nur an zulässigen Ports lauschen
- Linux-Funktionen mit den niedrigsten Berechtigungen müssen für Container erzwungen werden
- Unveränderliches (schreibgeschütztes) Stammdateisystem für Container erzwingen
- Container mit Rechteausweitung müssen vermieden werden
- Das Ausführen von Containern als Root-Benutzer muss vermieden werden
- Verwendung von Hostnetzwerken und -ports einschränken
- Container mit Freigabe sensibler Hostnamespaces vermeiden
- Verwendung von HostPath-Volumeeinbindungen von Pods sollte auf eine bekannte Liste beschränkt sein, um den Knotenzugriff von kompromittierten Containern zu begrenzen
- Überschreiben oder Deaktivieren des AppArmor-Profils für Container einschränken


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie eine Ressource aus einer Empfehlung ausschließen, damit sie sich nicht mehr auf Ihre Sicherheitsbewertung auswirkt. Weitere Informationen zur Sicherheitsbewertung finden Sie unter:

- [Secure Score in Azure Security Center](secure-score-security-controls.md)