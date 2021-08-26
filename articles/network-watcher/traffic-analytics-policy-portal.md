---
title: Bereitstellen und Verwalten von Traffic Analytics mithilfe von Azure Policy
titleSuffix: Azure Network Watcher
description: In diesem Artikel wird erläutert, wie die integrierten Richtlinien zum Verwalten der Bereitstellung von Traffic Analytics verwendet werden.
services: network-watcher
documentationcenter: na
author: moagra
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2021
ms.author: moagra
ms.openlocfilehash: d55a3090e9f7c8af73f854074a5823b7fe6eba1e
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113688547"
---
# <a name="deploy-and-manage-traffic-analytics-using-azure-policy"></a>Bereitstellen und Verwalten von Traffic Analytics mithilfe von Azure Policy 

Azure Policy hilft bei der Durchsetzung von Organisationsstandards und bei der Bewertung der Compliance nach Bedarf. Häufige Anwendungsfälle für Azure Policy sind die Implementierung von Governance für Ressourcenkonsistenz, Einhaltung gesetzlicher Bestimmungen, Sicherheit, Kosten und Verwaltung. In diesem Artikel werden drei integrierte Richtlinien behandelt, die [Traffic Analytics](./traffic-analytics.md) zur Verwaltung Ihrer Einrichtung zur Verfügung stehen.

Wenn Sie zum ersten Mal eine Azure-Richtlinie erstellen, können Sie Folgendes lesen: 
- [Azure Policy – Übersicht](../governance/policy/overview.md) 
- [Tutorial zum Erstellen einer Richtlinie](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Suchen der Richtlinien
1. Wechseln Sie zum Azure-Portal: [portal.azure.com](https://portal.azure.com) 

Navigieren Sie zur Seite „Azure Policy“, indem Sie in der oberen Suchleiste nach „Policy“ suchen. ![Policy-Startseite](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Navigieren Sie im linken Bereich zur Registerkarte **Zuweisungen**.

![Registerkarte „Zuweisungen“](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Klicken Sie auf die Schaltfläche **Richtlinie zuweisen**. 

![Schaltfläche „Richtlinie zuweisen“](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Klicken Sie unter „Richtliniendefinitionen“ auf das Drei- Punkte-Menü, um die verfügbaren Richtlinien anzuzeigen.

5. Verwenden Sie den Filter „Typ“, und wählen Sie „Integriert“ aus. Suchen Sie dann nach „traffic analytics“.

Die drei integrierten Richtlinien sollten angezeigt werden. ![Richtlinienliste für Traffic Analytics](./media/traffic-analytics/policy-filtered-view.png)

6. Wählen Sie die Richtlinie aus, die Sie zuweisen möchten.

- *Für Network Watcher-Datenflussprotokolle sollte Traffic Analytics aktiviert sein* ist die Überwachungsrichtlinie, die nicht konforme Datenflussprotokolle kennzeichnet, d. h. Datenflussprotokolle ohne aktivierte Datenverkehrsanalyse.
- *Konfigurieren von Netzwerksicherheitsgruppen zur Verwendung eines bestimmten Arbeitsbereichs für Traffic Analytics* und *Konfigurieren von Netzwerksicherheitsgruppen zum Aktivieren von Traffic Analytics* sind die Richtlinien mit einer Bereitstellungsaktion. Sie ermöglichen die Datenverkehrsanalyse für alle NSGs, wobei bereits konfigurierte Einstellungen je nach aktivierter Richtlinie überschrieben bzw. nicht überschrieben werden.

Für jede Richtlinie sind unten separate Anweisungen aufgeführt.  

## <a name="audit-policy"></a>Überwachungsrichtlinie 

### <a name="network-watcher-flow-logs-should-have-traffic-analytics-enabled"></a>Für Network Watcher-Datenflussprotokolle sollte Traffic Analytics aktiviert sein.

Die Richtlinie überwacht alle vorhandenen Azure Resource Manager-Objekte vom Typ „Microsoft.Network/networkWatchers/flowLogs“ und überprüft, ob Traffic Analytics über die Eigenschaft „networkWatcherFlowAnalyticsConfiguration.enabled“ der Datenflussprotokolle-Ressource aktiviert ist. Die Datenflussprotokolle-Ressource, für die die Eigenschaft auf FALSE festgelegt ist, wird gekennzeichnet.

Wenn Sie die vollständige Definition der Richtlinie anzeigen möchten, rufen Sie die Registerkarte [Definitionen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) auf, und suchen Sie nach „traffic analytics“, um die Richtlinie zu finden.

### <a name="assignment"></a>Zuordnung

1. Geben Sie Ihre Richtliniendetails ein.

- Bereich: Dies kann ein Abonnement oder eine Ressourcengruppe sein. Wählen Sie im letzteren Fall die Ressourcengruppe aus, die die Datenflussprotokolle-Ressource (und nicht die Netzwerksicherheitsgruppe) enthält.
- Richtliniendefinition: Sollte wie im Abschnitt „Suchen der Richtlinien" ausgewählt werden.
- Zuweisungsname: Wählen Sie einen beschreibenden Namen aus. 

2. Klicken Sie auf „Überprüfen + erstellen“, um Ihre Zuweisung zu überprüfen.

Die Richtlinie erfordert keine Parameter. Wenn Sie eine Überwachungsrichtlinie zuweisen, müssen Sie die Registerkarte „Wartung“ nicht ausfüllen.  

![Überwachungsrichtlinie: Überprüfung in Traffic Analytics](./media/traffic-analytics/policy-one-assign.png)

### <a name="results"></a>Ergebnisse

Um die Ergebnisse zu überprüfen, öffnen Sie die Registerkarte „Konformität“, und suchen Sie nach dem Namen Ihrer Zuweisung.
Sobald Ihre Richtlinie ausgeführt wird, sollte die Anzeige dem folgenden Screenshot ähneln. Wenn Ihre Richtlinie nicht ausgeführt wurde, warten Sie noch etwas ab. 

![Überwachungsrichtlinie: Ergebnisse in Traffic Analytics](./media/traffic-analytics/policy-one-results.png)

## <a name="deploy-if-not-exists-policy"></a>Richtlinie für Bereitstellung, falls nicht vorhanden 

### <a name="configure-network-security-groups-to-use-specific-workspace-for-traffic-analytics"></a>Konfigurieren von Netzwerksicherheitsgruppen zur Verwendung eines bestimmten Arbeitsbereichs für Traffic Analytics 

Die NSG, für die Traffic Analytics nicht aktiviert ist, wird gekennzeichnet. Dies bedeutet, dass für die gekennzeichnete NSG entweder die entsprechende Datenflussprotokolle-Ressource nicht vorhanden ist, oder die Datenflussprotokolle-Ressource vorhanden ist, die Datenverkehrsanalyse darauf jedoch nicht aktiviert ist. Sie können einen Wartungstask erstellen, wenn sich die Richtlinie auf vorhandene Ressourcen auswirken soll.
Network Watcher ist ein regionaler Dienst, sodass diese Richtlinie nur für NSGs gilt, die zu einer bestimmten Region im ausgewählten Bereich gehören. (Erstellen Sie für eine andere Region eine weitere Richtlinienzuweisung.)
 
Wartung kann beim Zuweisen einer Richtlinie oder nach dem Zuweisen und Auswerten einer Richtlinie zugewiesen werden. Die Wartung aktiviert Traffic Analytics für alle gekennzeichneten Ressourcen mit den angegebenen Parametern. Beachten Sie Folgendes: Wenn für eine NSG bereits Datenflussprotokolle für eine bestimmte Speicher-ID aktiviert sind, aber nicht Traffic Analytics aktiviert ist, aktiviert die Wiederherstellung Traffic Analytics für diese NSG mit den bereitgestellten Parametern. Wenn sich die in den Parametern angegebene Speicher-ID für die gekennzeichnete NSG von der bereits für Datenflussprotokolle aktivierten unterscheidet, wird letztere im Wartungstask mit der angegebenen Speicher-ID überschrieben. Wenn Sie nicht überschreiben möchten, verwenden Sie die unten beschriebene Richtlinie *Konfigurieren von Netzwerksicherheitsgruppen zum Aktivieren von Traffic Analytics*.

Wenn Sie die vollständige Definition der Richtlinie anzeigen möchten, rufen Sie die Registerkarte [Definitionen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) auf, und suchen Sie nach „Traffic Analytics“, um die Richtlinie zu finden. 

### <a name="configure-network-security-groups-to-enable-traffic-analytics"></a>Konfigurieren von Netzwerksicherheitsgruppen zum Aktivieren von Traffic Analytics

Dies entspricht der obigen Richtlinie, außer dass während der Wartung keine Datenflussprotokolleinstellungen für die gekennzeichneten NSGs überschrieben werden, für die Datenflussprotokolle aktiviert sind, aber Traffic Analytics mit dem in der Richtlinienzuweisung angegebenen Parameter deaktiviert ist.

### <a name="assignment"></a>Zuordnung

1. Geben Sie Ihre Richtliniendetails ein.

- Bereich: Dies kann ein Abonnement oder eine Ressourcengruppe sein.  
- Richtliniendefinition: Sollte wie im Abschnitt „Suchen der Richtlinien" ausgewählt werden.
- Zuweisungsname: Wählen Sie einen beschreibenden Namen aus. 

2. Hinzufügen von Richtlinienparametern 

- NSG-Region: Azure-Regionen, auf die die Richtlinie ausgerichtet ist.
- Speicher-ID: Hierbei handelt es sich um die vollständige Ressourcen-ID des Speicherkontos. Das Speicherkonto sollte sich in derselben Region wie die NSG befinden.
- Network Watcher-Ressourcengruppe: Der Name der Ressourcengruppe, die Ihre Network Watcher-Ressource enthält. Wenn Sie diese nicht umbenannt haben, können Sie die Standardeinstellung „NetworkWatcherRG“ eingeben.
- Network Watcher-Name: Der Name des regionalen Network Watcher-Diensts. Format: NetworkWatcher_RegionName. Beispiel: NetworkWatcher_centralus.
- Ressourcen-ID des Arbeitsbereichs: Ressourcen-ID des Arbeitsbereichs, in dem Traffic Analytics aktiviert werden muss. Das Format ist „/subscriptions/<SubscriptionID>/resourceGroups/<ResouceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>“.
- WorkspaceID: Arbeitsbereichs-GUID
- WorkspaceRegion: Region des Arbeitsbereichs (beachten Sie, dass sie nicht mit der Region der NSG identisch sein muss)
- TimeInterval: Häufigkeit, mit der verarbeitete Protokolle per Push in den Arbeitsbereich übertragen werden. Derzeit sind 60 Minuten und 10 Minuten zulässig. Der Standardwert sind 60 Minuten.
- Effect: DeployIfNotExists (bereits zugewiesener Wert)

3. Hinzufügen von Wartungsdetails

- Aktivieren Sie das Kontrollkästchen *Wartungstask erstellen*, wenn sich die Richtlinie auf vorhandene Ressourcen auswirken soll.
- *Verwaltete Identität erstellen* sollte bereits aktiviert sein.
- Wählen Sie den gleichen Speicherort wie zuvor für Ihre verwaltete Identität aus.
- Sie benötigen Berechtigungen als Mitwirkender oder Besitzer, um diese Richtlinie zu verwenden. Wenn Sie über diese Berechtigungen verfügen, sollten keine Fehler angezeigt werden.

4. Klicken Sie auf „Überprüfen + erstellen“, um die Zuweisung zu überprüfen. Die Anzeige sollte dem folgenden Screenshot ähneln.

![DINE-Richtlinie: Überprüfung in Traffic Analytics](./media/traffic-analytics/policy-two-review.png) 


### <a name="results"></a>Ergebnisse

Um die Ergebnisse zu überprüfen, öffnen Sie die Registerkarte „Konformität“, und suchen Sie nach dem Namen Ihrer Zuweisung.
Wenn Ihre Richtlinie ausgeführt wurde, sollte ein Screenshot angezeigt werden, der dem folgenden ähnelt. Wenn Ihre Richtlinie nicht ausgeführt wurde, warten Sie noch etwas ab.

![DINE-Richtlinie: Ergebnisse in Traffic Analytics](./media/traffic-analytics/policy-two-results.png)  

### <a name="remediation"></a>Behebung

Wählen Sie zur manuellen Wartung die Option *Wartungstask erstellen* auf der oben gezeigten Konformitätsregisterkarte auf.

![DINE-Richtlinie: Wartung in Traffic Analytics](./media/traffic-analytics/policy-two-remediate.png) 


## <a name="troubleshooting"></a>Problembehandlung

### <a name="remediation-task-fails-with-policyauthorizationfailed-error-code"></a>Beim Wartungstask tritt mit dem Fehlercode „PolicyAuthorizationFailed“ ein Fehler auf.

Fehlerbeispiel: „Die Richtlinienzuweisungs-Ressourcenidentität '/subscriptions/123ds-fdf3657-fdjjjskms638/resourceGroups/DummyRG/providers/Microsoft.Authorization/policyAssignments/b67334e8770a4afc92e7a929/' verfügt nicht über die erforderlichen Berechtigungen zum Erstellen der Bereitstellung.“

In solchen Szenarien muss der verwalteten Identität der Zuweisung manuell Zugang gewährt werden. Wechseln Sie zum entsprechenden Abonnement bzw. zur entsprechenden Ressourcengruppe (mit den in den Richtlinienparametern bereitgestellten Ressourcen), und gewähren Sie Mitwirkenden Zugriff auf die verwaltete Identität, die von der Richtlinie erstellt wird. Im obigen Beispiel muss „b67334e8770a4afc92e7a929“ als Mitwirkender verwendet werden.


## <a name="next-steps"></a>Nächste Schritte 

-   Erfahren Sie mehr über [Schnellstart: Bereitstellen und Verwalten von NSG-Datenflussprotokollen mit Azure Policy](./nsg-flow-logs-policy-portal.md).
-   Erfahren Sie mehr über [Traffic Analytics](./traffic-analytics.md).
-   Erfahren Sie mehr über [Network Watcher](./index.yml).
