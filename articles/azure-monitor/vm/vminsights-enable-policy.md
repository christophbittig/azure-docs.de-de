---
title: Aktivieren von VM Insights mithilfe von Azure Policy
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Policy VM Insights für mehrere Azure-VMs oder VM-Skalierungsgruppen aktivieren.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 63e68a247dd9d38cffe1555806ab23391c38f1fa
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177441"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>Aktivieren von VM Insights mithilfe von Azure Policy
In diesem Artikel wird erläutert, wie Sie VM Insights mithilfe von Azure Policy für Azure-VMs oder Hybrid-VMs aktivieren, die mit Azure Arc (Preview) verbunden sind. Azure Policy ermöglicht Ihnen das Zuweisen von Richtliniendefinitionen, mit denen die erforderlichen Agents für VM Insights in Ihrer Azure-Umgebung installiert werden. Außerdem wird bei der Erstellung der einzelnen VMs automatisch die Überwachung dieser VMs aktiviert. VM Insights bietet ein Feature, mit dem Sie nicht konforme VMs in Ihrer Umgebung ermitteln und warten können. Verwenden Sie diese Funktion, anstatt direkt mit Azure Policy zu arbeiten.

Wenn Sie nicht mit Azure Policy vertraut sind, finden Sie unter [Bedarfsorientiertes Bereitstellen von Azure Monitor mithilfe von Azure Policy](../deploy-scale.md) eine kurze Einführung.

> [!NOTE]
> Informationen zur Verwendung von Azure Policy mit Azure-VM-Skalierungsgruppen sowie zur direkten Verwendung von Azure Policy zum Aktivieren von Azure-VMs finden Sie unter [Bedarfsorientiertes Bereitstellen von Azure Monitor mithilfe von Azure Policy](../deploy-scale.md#vm-insights).

## <a name="vm-insights-initiatives"></a>VM Insights-Initiativen
VM Insights stellt integrierte Richtliniendefinitionen für die Installation des Log Analytics-Agents und des Dependency-Agents auf Azure-VMs bereit. Die folgenden integrierten Initiativen installieren beide Agents, um eine umfassende Überwachung zu ermöglichen. Weisen Sie diese Initiativen einer Verwaltungsgruppe, einem Abonnement oder einer Ressourcengruppe zu, um die Agents automatisch auf Azure-VMs mit Windows oder Linux im jeweiligen Bereich zu installieren.

|Name |BESCHREIBUNG |
|:---|:---|
|Aktivieren von VM Insights | Installiert den Log Analytics-Agent und den Dependency-Agent auf Azure-VMs und Hybrid-VMs, die mit Azure Arc verbunden sind. |
|Aktivieren von Azure Monitor für VM-Skalierungsgruppen | Installiert den Log Analytics-Agent und den Dependency-Agent in der Azure-VM-Skalierungsgruppe. |



## <a name="open-policy-coverage-feature"></a>Aufrufen des Features „Richtlinienabdeckung“
Navigieren Sie für den Zugriff auf **VM Insights Policy Coverage** im Azure-Portal im Menü **Azure Monitor** zu **Virtuelle Computer**. Wählen Sie **Andere Onboardingoptionen** und dann unter **Über Richtlinie aktivieren** die Option **Aktivieren** aus.

[![Registerkarte „Erste Schritte“ in Azure Monitor für VMs](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

### <a name="create-new-assignment"></a>Erstellen einer neuen Zuweisung
Wenn Sie noch nicht über eine Zuweisung verfügen, klicken Sie auf **Richtlinie zuweisen**, um eine neue Zuweisung zu erstellen.

[![Zuweisung erstellen](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

Diese Seite stimmt nahezu mit der Seite zum Zuweisen einer Initiative in Azure Policy überein. Die einzigen Unterschiede sind, dass der von Ihnen ausgewählte Bereich und die Definition der Initiative **Enable VM insights** (VM Insights aktivieren) hartcodiert sind. Optional kann der **Zuweisungsname** geändert und eine **Beschreibung** hinzugefügt werden. Wählen Sie **Ausschlüsse** aus, wenn Sie einen Ausschluss zum Bereich hinzufügen möchten. Wenn Sie beispielsweise eine Verwaltungsgruppe als Bereich verwenden, können Sie ein Abonnement in dieser Verwaltungsgruppe angeben, das von der Zuweisung ausgeschlossen werden soll.

[![Initiative zuweisen](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

Wählen Sie auf der Seite **Parameter** einen **Log Analytics-Arbeitsbereich** aus, der von allen VMs in der Zuweisung verwendet werden soll. Wenn Sie unterschiedliche Arbeitsbereiche für die verschiedenen VMs angeben möchten, müssen Sie mehrere Zuweisungen mit unterschiedlichen Bereichen erstellen. 

   > [!NOTE]
   > Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, erteilen Sie der Prinzipal-ID der Richtlinienzuweisung *Log Analytics-Mitwirkender*-Berechtigungen. Anderenfalls wird möglicherweise ein Bereitstellungsfehler wie der Folgende angezeigt: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Arbeitsbereich](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

Klicken Sie auf **Überprüfen + erstellen**, um die Details der Zuweisung zu überprüfen, bevor Sie auf **Erstellen** klicken, um die Zuweisung zu erstellen. Erstellen Sie zu diesem Zeitpunkt keinen Wartungstask. Wahrscheinlich benötigen Sie mehrere Wartungstasks, um vorhandene VMs zu aktivieren. Weitere Informationen finden Sie unten unter [Behandeln von Konformitätsproblemen](#remediate-compliance-results).

### <a name="review-compliance"></a>Überprüfen der Konformität
Nach der Erstellung einer Zuweisung können Sie die Abdeckung für die Initiative **Enable VM insights** (VM Insights aktivieren) für Ihre Verwaltungsgruppen und Abonnements überprüfen und verwalten. Sie können ermitteln, wie viele VMs in den einzelnen Verwaltungsgruppen und Abonnements vorhanden sind und welchen Konformitätsstatus sie aufweisen.

[![Seite für die Verwaltung von Richtlinien in VM Insights](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


Die folgende Tabelle enthält eine Beschreibung der Informationen in dieser Ansicht.

| Funktion | BESCHREIBUNG | 
|----------|-------------| 
| **Umfang** | Verwaltungsgruppen und Abonnements, auf die Sie Zugriff haben oder für die Sie den Zugriff geerbt haben, mit der Möglichkeit, die Hierarchie der Verwaltungsgruppen aufzuschlüsseln.|
| **Rolle** | Ihre Rolle innerhalb des Bereichs (Leser, Besitzer oder Mitwirkender). Dieses Feld ist leer, wenn Sie auf das Abonnement, jedoch nicht auf die zugehörige Verwaltungsgruppe zugreifen können. Über diese Rolle wird festgelegt, welche Daten Sie sehen und welche Aktionen Sie durchführen können. Die Rolle gibt also Auskunft darüber, welche Richtlinien oder Initiativen (Besitzer) zugewiesen oder bearbeitet werden können bzw. für welche Richtlinien oder Initiativen der Konformitätsstatus angezeigt werden kann. |
| **VMs gesamt** | Gesamtzahl der VMs innerhalb des Bereichs (unabhängig vom Status). Für eine Verwaltungsgruppe ist dies die Summe der VMs, die unter den Abonnements oder untergeordneten Verwaltungsgruppen geschachtelt sind. |
| **Zuweisungsabdeckung** | Prozentsatz der VMs, die von der Initiative abgedeckt sind. |
| **Zuweisungsstatus** | **Erfolg**: Auf allen VMs im Bereich sind der Log Analytics- und der Dependency-Agent bereitgestellt.<br>**Warnung**: Das Abonnement gehört zu keiner Verwaltungsgruppe.<br>**Nicht gestartet**: Eine neue Zuweisung wurde hinzugefügt.<br>**Sperre**: Sie haben nicht genügend Rechte für die Verwaltungsgruppe.<br>**Leer**: Es sind keine VMs vorhanden, oder es wurde keine Richtlinie zugewiesen. |
| **Konforme VMs** | Die Anzahl von konformen VMs, also die Anzahl von VMs, auf denen sowohl der Log Analytics-Agent als auch der Dependency-Agent installiert ist. Dieses Feld ist leer, wenn keine Zuweisungen, keine VMs im Bereich oder keine erforderlichen Berechtigungen vorhanden sind. |
| **Compliance** | Der Gesamtwert für die Konformität ist die Summe der einzelnen Ressourcen, die konform sind, geteilt durch die Summe aller einzelnen Ressourcen. |
| **Konformitätszustand** | **Konform**: Auf allen VMs im Bereich sind der Log Analytics- und der Dependency-Agent bereitgestellt bzw. neue VMs im Bereich, für die die Zuweisung gilt, wurden noch nicht ausgewertet.<br>**Nicht konform**: Es sind VMs vorhanden, die ausgewertet, jedoch nicht aktiviert wurden. Für diese VMs müssen möglicherweise Wartungsschritte ausgeführt werden.<br>**Nicht gestartet**: Eine neue Zuweisung wurde hinzugefügt.<br>**Sperre**: Sie haben nicht genügend Rechte für die Verwaltungsgruppe.<br>**Leer**: Es ist keine Richtlinie zugewiesen.  |


Wenn Sie die Initiative zuweisen, kann der in der Zuweisung ausgewählte Bereich der aufgelistete Bereich oder eine Teilmenge davon sein. Angenommen, Sie haben eine Zuweisung für ein Abonnement (Richtlinienumfang) und nicht für eine Verwaltungsgruppe (Abdeckungsumfang) vorgenommen. In diesem Fall gibt der Wert von **Zuweisungsabdeckung** die VMs im Bereich der Initiative dividiert durch die VMs im Abdeckungsumfang an. In einem anderen Fall haben Sie möglicherweise einige VMs, Ressourcengruppen oder ein Abonnement aus dem Richtlinienumfang ausgeschlossen. Wenn der Wert leer ist, bedeutet dies, dass entweder die Richtlinie oder Initiative nicht vorhanden ist oder Sie keine Berechtigung haben. Informationen werden unter **Zuweisungsstatus** angezeigt.


### <a name="remediate-compliance-results"></a>Behandeln von Konformitätsproblemen
Die Initiative wird auf VMs angewendet, wenn diese erstellt oder geändert werden. Auf vorhandene VMs wird sie jedoch nicht angewendet. Wenn für Ihre Zuweisung keine Konformität von 100 % ermittelt wird, erstellen Sie Wartungstasks, um vorhandene VMs auszuwerten und zu aktivieren. Wählen Sie zur Auswahl von **Konformität anzeigen** die Auslassungspunkte (...) aus.

[![Konformität anzeigen](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

Auf der Seite **Konformität** werden Zuweisungen aufgeführt, die dem angegebenen Filter entsprechen. Außerdem wird angezeigt, ob die Zuweisungen konform sind. Klicken Sie auf eine Zuweisung, um die jeweiligen Details anzuzeigen.

[![Richtlinienkonformität für Azure-VMs](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

Auf der Seite **Initiativenkonformität** werden die Richtliniendefinitionen in der Initiative aufgeführt. Außerdem wird der Konformitätsstatus angezeigt.

[![Konformitätsdetails](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

Klicken Sie auf eine Richtliniendefinition, um die zugehörigen Details anzuzeigen. Nachfolgend sind Szenarien aufgeführt, in denen Richtliniendefinitionen als nicht konform angezeigt werden:

* Der Log Analytics-Agent oder der Dependency-Agent wurde nicht bereitgestellt. Erstellen Sie einen Wartungstask, um das Problem zu behandeln.
* Das VM-Image (Betriebssystem) wird in der Richtliniendefinition nicht identifiziert. Die Kriterien der Bereitstellungsrichtlinie schließen nur VMs ein, die aus bekannten Azure VM-Images bereitgestellt werden. Überprüfen Sie anhand der Dokumentation, ob das Betriebssystem der VM unterstützt wird.
* VMs führen keine Protokollierung im angegebenen Log Analytics-Arbeitsbereich durch. Einige VMs im Bereich der Initiative sind mit einem anderen Log Analytics-Arbeitsbereich verbunden als dem, der in der Richtlinienzuweisung angegeben ist.

[![Details zur Richtlinienkonformität](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

Klicken Sie auf **Wartungstask erstellen**, um einen Wartungstask zum Behandeln von Konformitätsproblemen zu erstellen. 

[![Neuer Wartungstask](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

Klicken Sie auf **Wartung ausführen**, um den Wartungstask zu erstellen, und dann erneut auf **Wartung ausführen**, um den Task zu starten. Wahrscheinlich müssen Sie mehrere Wartungstasks erstellen (einen Task pro Richtliniendefinition). Es ist nicht möglich, einen Wartungstask für eine Initiative zu erstellen.

[![Screenshot des Bereichs „Policy Remediation“ (Richtlinienwartung) für Monitor | Virtual Machines](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


Nach Abschluss der Wartungstasks sollten Ihre VMs konform sein. Die Agents sollten installiert und für VM Insights aktiviert sein. 


## <a name="azure-policy"></a>Azure Policy
Wenn Sie Azure Policy zum Aktivieren der Überwachung von VM-Skalierungsgruppen verwenden möchten, weisen Sie die Initiative **Aktivieren von Azure Monitor für VM-Skalierungsgruppen** einer Azure-Verwaltungsgruppe, einem Abonnement oder einer Ressource zu (abhängig vom Umfang der Ressourcen, die überwacht werden sollen). Eine [Verwaltungsgruppe](../../governance/management-groups/overview.md) ist nützlich für bereichsbezogene Richtlinien, insbesondere wenn Ihre Organisation über mehrere Abonnements verfügt.

![Screenshot der Seite „Initiative zuweisen“ im Azure-Portal. Die Initiativdefinition ist auf „Aktivieren von Azure Monitor für VM-Skalierungsgruppen“ festgelegt.](media/vminsights-enable-policy/virtual-machine-scale-set-assign-initiative.png)

Wählen Sie den Arbeitsbereich aus, an den die Daten gesendet werden. In diesem Arbeitsbereich muss die *VMInsights*-Lösung installiert sein, wie unter [Konfigurieren eines Log Analytics-Arbeitsbereichs für VM Insights](vminsights-configure-workspace.md) beschrieben.

![Screenshot: Auswählen eines Arbeitsbereichs](media/vminsights-enable-policy/virtual-machine-scale-set-workspace.png)

Erstellen Sie einen Wartungstask, wenn Sie über vorhandene VM-Skalierungsgruppen verfügen, der diese Richtlinie zugewiesen werden muss.

![Screenshot: Erstellen eines Wartungstasks](media/vminsights-enable-policy/virtual-machine-scale-set-remediation.png)



## <a name="next-steps"></a>Nächste Schritte

Nachdem die Überwachung für Ihre virtuellen Computer aktiviert wurde, stehen diese Informationen nun für die Analyse mit VM Insights zur Verfügung. 

- Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Anzeigen der Zuordnung in VM Insights](vminsights-maps.md). 
- Informationen zum Erkennen von Engpässen und der Gesamtauslastung im Hinblick auf die Leistung Ihrer VM finden Sie unter [Anzeigen der Leistung von Azure-VMs](vminsights-performance.md).
