---
title: Bewährte Methoden für die Microsoft Sentinel-Arbeitsbereichsarchitektur
description: Erfahren Sie mehr über bewährte Methoden zum Entwerfen Ihres Microsoft Sentinel-Arbeitsbereichs.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 83909e0ef8667b4ee466b9e8f1ad04a717e8302a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524757"
---
# <a name="microsoft-sentinel-workspace-architecture-best-practices"></a>Bewährte Methoden für die Microsoft Sentinel-Arbeitsbereichsarchitektur

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie die Bereitstellung Ihres Microsoft Sentinel-Arbeitsbereichs planen, müssen Sie auch Ihre Log Analytics-Arbeitsbereichsarchitektur entwerfen. Entscheidungen über die Arbeitsbereichsarchitektur werden in der Regel von den geschäftlichen und technischen Anforderungen gesteuert. In diesem Artikel werden wichtige Faktoren für die Entscheidung behandelt, anhand derer Sie die richtige Arbeitsbereichsarchitektur für Ihre Organisationen ermitteln können. Dies umfasst u. a.:

- ob Sie einen einzelnen oder mehrere Mandanten verwenden wollen
- sämtliche Konformitätsanforderungen für die Datensammlung und -speicherung
- Steuern des Zugriffs auf Microsoft Sentinel-Daten
- die Kostenauswirkungen verschiedener Szenarien

Weitere Informationen finden Sie unter [Entwerfen Ihrer Microsoft Sentinel-Arbeitsbereichsarchitektur](design-your-workspace-architecture.md), [Beispielentwürfe von Arbeitsbereichen](sample-workspace-designs.md) für gängige Szenarien und [Aktivitäten vor der Bereitstellung und Voraussetzungen für die Bereitstellung von Microsoft Sentinel](prerequisites.md).

Weitere Informationen finden Sie in unserem Video: [Entwerfen erfolgreicher SecOps: Bewährte Methoden zum Bereitstellen von Microsoft Sentinel](https://youtu.be/DyL9MEMhqmI).


## <a name="tenancy-considerations"></a>Überlegungen zu Mandanten

Wenige Arbeitsbereiche sind einfach zu verwalten, aber möglicherweise gibt es in Ihrer Organisation bestimmte Anforderungen für mehrere Mandanten und mehrere Arbeitsbereiche. Viele Organisationen verfügen z. B. über eine Cloudumgebung, die mehrere [Azure Active Directory-Mandanten (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) enthält. Solche Umgebungen entstehen vor allem durch Fusionen und Übernahmen oder aufgrund von Anforderungen an die Identitätstrennung.

Berücksichtigen Sie bei der Ermittlung der benötigten Mandanten und Arbeitsbereiche, dass die meisten Microsoft Sentinel-Features mit einem einzelnen Arbeitsbereich oder einer einzelnen Microsoft Sentinel-Instanz betrieben werden, und dass Microsoft Sentinel alle Protokolle erfasst, die sich im Arbeitsbereich befinden.

> [!IMPORTANT]
> Die Kosten sind einer der wichtigsten Aspekte bei der Festlegung der Microsoft Sentinel-Architektur. Weitere Informationen finden Sie unter [Microsoft Sentinel – Kosten und Abrechnung](azure-sentinel-billing.md).
>
### <a name="working-with-multiple-tenants"></a>Verwenden mehrerer Mandanten

Wenn Sie über mehrere Mandanten verfügen, z. B. wenn Sie ein Managed Security Service Provider (MSSP) sind, empfiehlt es sich, mindestens einen Arbeitsbereich für jeden Azure AD-Mandanten zu erstellen, um integrierte [Dienst-zu-Dienst-Connectors](connect-data-sources.md#service-to-service-integration) zu unterstützen, die nur innerhalb ihres eigenen Azure AD-Mandanten funktionieren.

Connectors, die auf Diagnoseeinstellungen basieren, können nicht mit einem Arbeitsbereich verbunden werden, der sich nicht in dem Mandanten befindet, in dem sich auch die Ressource befindet. Dies gilt für Connectors wie [Azure Firewall](./data-connectors-reference.md#azure-firewall), [Azure Storage](./data-connectors-reference.md#azure-storage-account), [Azure Activity](./data-connectors-reference.md#azure-activity) oder [Azure Active Directory](connect-azure-active-directory.md).

Nutzen Sie [Azure Lighthouse](../lighthouse/how-to/onboard-customer.md), um mehrere Microsoft Sentinel-Instanzen in verschiedenen Mandanten zu verwalten.

> [!NOTE]
> [Partnerdatenconnectors](data-connectors-reference.md) basieren in der Regel auf API- oder Agent-Sammlungen und sind daher keinem bestimmten Azure AD-Mandanten angefügt.
>



## <a name="compliance-considerations"></a>Überlegungen zur Vorschrifteneinhaltung

Nach dem Sammeln, Speichern und Verarbeiten Ihrer Daten wird die Konformität zu einer wichtigen Grundlage für Entwurfsentscheidungen, was erhebliche Auswirkungen auf Ihre Microsoft Sentinel-Architektur hat. Die Fähigkeit, zu überprüfen und nachzuweisen, wer unter welchen Bedingungen Zugriff auf welche Daten hat, ist in vielen Ländern und Regionen eine wichtige Anforderung an die Datenhoheit. Darüber hinaus ist die Bewertung von Risiken und das Gewinnen von Erkenntnissen über Microsoft Sentinel-Workflows für viele Kunden von besonderer Priorität.

In Microsoft Sentinel werden Daten größtenteils in demselben Gebiet oder in derselben Region gespeichert und verarbeitet. Es gibt jedoch auch ein paar Ausnahmen, z. B. wenn Erkennungsregeln im Zusammenhang mit dem Machine Learning von Microsoft genutzt werden. In solchen Fällen können Daten zur Verarbeitung in ein Gebiet außerhalb Ihres Arbeitsbereichs kopiert werden.

Weitere Informationen finden Sie unter:

- [Geografische Verfügbarkeit und Data Residency](quickstart-onboard.md#geographical-availability-and-data-residency)
- [Datenresidenz in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)
- [Speichern und Verarbeiten von EU-Daten in der EU – Blog zu EU-Richtlinien](https://blogs.microsoft.com/eupolicy/2021/05/06/eu-data-boundary/)

Um Ihre Konformität zu überprüfen, müssen Sie sowohl Ihre Datenquellen als auch wie und wohin diese die Daten senden bewerten.

> [!NOTE]
> Der [Log Analytics-Agent](connect-windows-security-events.md) unterstützt TLS 1.2, um die Datensicherheit während der Übertragung zwischen dem Agent und dem Log Analytics-Dienst sicherzustellen, sowie den FIPS 140-Standard. 
>
> Wenn Sie Daten an ein Gebiet oder eine Region senden, die sich von Ihrem Microsoft Sentinel-Arbeitsbereich unterscheidet (unabhängig davon ob sich die sendende Ressource in Azure befindet oder nicht), sollten Sie die Nutzung eines Arbeitsbereichs in demselben Gebiet oder derselben Region in Betracht ziehen.
>

## <a name="region-considerations"></a>Überlegungen zu Regionen

Verwenden Sie unterschiedliche Microsoft Sentinel-Instanzen für jede Region. Obwohl Microsoft Sentinel in mehreren Regionen verwendet werden kann, gibt es möglicherweise gesonderte Anforderungen für das Trennen von Daten nach Team, Region oder Standort sowie Vorschriften und Kontrollen, durch die Modelle mit mehreren Regionen unmöglich oder unnötig komplex werden. Die Nutzung separater Instanzen und Arbeitsbereiche für jede Region trägt dazu bei, Bandbreitenkosten und Kosten für ausgehende Daten im Zusammenhang mit dem regionsübergreifenden Verschieben von Daten zu vermeiden.

Beachten Sie Folgendes, wenn Sie mit mehreren Regionen arbeiten:

- Kosten für ausgehende Daten fallen in der Regel an, wenn der [Log Analytics- oder Azure Monitor-Agent](connect-windows-security-events.md) Protokolle sammeln muss, z. B. auf virtuellen Computern.

- Auch ausgehende Internetdaten werden in Rechnung gestellt. Dies betrifft Sie nur, wenn Sie Daten außerhalb Ihres Log Analytics-Arbeitsbereichs exportieren. Es können z. B. Gebühren für ausgehenden Internetdatenverkehr anfallen, wenn Sie Ihre Log Analytics-Daten auf einen lokalen Server exportieren.

- Die Bandbreitenkosten variieren je nach Quell- und Zielregion sowie nach Sammlungsmethode. Weitere Informationen finden Sie unter:

    - [Bandbreitenpreise](https://azure.microsoft.com/pricing/details/bandwidth/)
    - [Gebühren für die Datenübertragung mit Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

- Verwenden Sie Vorlagen für Ihre Analyseregeln, benutzerdefinierten Abfragen, Arbeitsmappen und anderen Ressourcen, um Ihre Bereitstellungen effizienter zu gestalten. Stellen Sie die Vorlagen bereit, anstatt jede Ressource in jeder Region manuell bereitzustellen.

- Connectors, die auf Diagnoseeinstellungen basieren, verursachen keine Bandbreitenkosten. Weitere Informationen finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/logs/manage-cost-storage.md#data-transfer-charges-using-log-analytics).

Wenn Sie beispielsweise Protokolle von VMs in der Region „USA, Osten“ sammeln und an einen Microsoft Sentinel-Arbeitsbereich in der Region „USA, Westen“ senden möchten, werden Ihnen die Kosten für diese Datenübertragung in Rechnung gestellt. Da der Log Analytics-Agent die Daten während der Übertragung komprimiert, kann die als Bandbreite abgerechnete Größe kleiner sein, als die tatsächliche Größe der Protokolle in Microsoft Sentinel.

Wenn Sie Syslog- und CEF-Protokolle aus mehreren Quellen auf der ganzen Welt sammeln, sollten Sie einen Syslog-Collector in derselben Region wie Ihren Microsoft Sentinel-Arbeitsbereich einrichten, um Bandbreitenkosten zu vermeiden. Dies gilt unter der Voraussetzung, dass dadurch keine Konformitätsprobleme entstehen.

Die Überlegungen, ob Bandbreitenkosten separate Microsoft Sentinel-Arbeitsbereiche rechtfertigen, hängt von der Datenmenge ab, die Sie zwischen verschiedenen Regionen übertragen müssen. Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/details/bandwidth/), um Ihre Kosten zu ermitteln.

Weitere Informationen finden Sie unter [Datenresidenz in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

## <a name="access-considerations"></a>Überlegungen zum Zugriff

Möglicherweise treten bei Ihnen Situationen auf, in denen verschiedene Teams Zugriff auf dieselben Daten benötigen. Ihr SOC-Team muss beispielsweise Zugriff auf alle Microsoft Sentinel-Daten haben, während die Betriebs- und Anwendungsteams nur Zugriff auf bestimmte Teile der Daten benötigen. Unabhängige Sicherheitsteams müssen möglicherweise ebenfalls auf Microsoft Sentinel-Features zugreifen können, aber mit jeweils unterschiedlichen Datensätzen.

Kombinieren Sie [Ressourcenkontext-RBAC](resource-context-rbac.md) und [RBAC auf Tabellenebene](../azure-monitor/logs/manage-access.md#table-level-azure-rbac), um für Ihr Team eine große Bandbreite an Zugriffsoptionen bereitzustellen, mit denen die meisten Anwendungsfälle abgedeckt werden können.

Weitere Informationen hierzu finden Sie unter [Berechtigungen in Microsoft Sentinel](roles.md).

### <a name="resource-context-rbac"></a>Ressourcenkontext-RBAC

Die folgende Abbildung zeigt die vereinfachte Version einer Arbeitsbereichsarchitektur, bei der Sicherheits- und Betriebsteams Zugriff auf verschiedene Datasets benötigen. Hier wird die Ressourcenkontext-RBAC verwendet, um die erforderlichen Berechtigungen zu erteilen.


[ ![Diagramm einer Beispielarchitektur für Ressourcenkontext-RBAC](media/resource-context-rbac/resource-context-rbac-sample.png) ](media/resource-context-rbac/resource-context-rbac-sample.png#lightbox)

In dieser Abbildung ist zu sehen, dass der Microsoft Sentinel-Arbeitsbereich in einem separaten Abonnement platziert wird, um Berechtigungen besser zu isolieren.

> [!NOTE]
> Eine weitere Möglichkeit besteht darin, Microsoft Sentinel in einer separaten Verwaltungsgruppe zu platzieren, die speziell auf Sicherheit ausgelegt ist. Auf diese Weise wird sichergestellt, dass nur minimale Berechtigungszuweisungen vererbt werden. Innerhalb des Sicherheitsteams werden mehreren Gruppen gemäß ihren Funktionen die entsprechenden Berechtigungen zugewiesen. Da diese Teams Zugriff auf den gesamten Arbeitsbereich haben, haben sie ebenfalls Zugriff auf die gesamte Microsoft Sentinel-Benutzeroberfläche. Dies wird nur durch die Microsoft Sentinel-Rollen eingeschränkt, denen sie zugewiesen sind. Weitere Informationen hierzu finden Sie unter [Berechtigungen in Microsoft Sentinel](roles.md).
>

Zusätzlich zum Sicherheitsabonnement wird ein separates Abonnement genutzt, in dem Anwendungsteams ihre Workloads hosten können. Die Anwendungsteams erhalten Zugriff auf ihre jeweiligen Ressourcengruppen, in denen sie ihre Ressourcen verwalten können. Durch dieses separate Abonnement und die Ressourcenkontext-RBAC können diese Teams Protokolle sehen, die von den Ressourcen generiert wurden, auf die sie Zugriff haben. Dies funktioniert auch, wenn die Protokolle in einem Arbeitsbereich gespeichert sind, in dem sie *keinen* direkten Zugriff haben. Die Anwendungsteams können entweder über den Bereich **Protokolle** im Azure-Portal auf ihre Protokolle zugreifen, um Protokolle für eine bestimmte Ressource anzusehen. Oder sie können sich über Azure Monitor alle Protokolle anzeigen lassen, auf die sie gleichzeitig zugreifen können.

Azure-Ressourcen verfügen über eine integrierte Unterstützung für Ressourcenkontext-RBAC, benötigen jedoch möglicherweise eine zusätzliche Optimierung für die Arbeit mit Nicht-Azure-Ressourcen. Weitere Informationen finden Sie unter [Explizites Konfigurieren der rollenbasierten Zugriffssteuerung im Ressourcenkontext](resource-context-rbac.md#explicitly-configure-resource-context-rbac).

### <a name="table-level-rbac"></a>Table-level RBAC (RBAC auf Tabellenebene)

Mit der RBAC auf Tabellenebene können Sie bestimmte Datentypen (Tabellen) definieren, auf die nur eine bestimmte Benutzergruppe zugreifen kann.

Gehen wir z. B. davon aus, dass die Organisation, deren Architektur in der obigen Abbildung beschrieben ist, einem internen Überwachungsteam Zugriff auf Office 365-Protokolle gewähren muss. In diesem Fall kann RBAC auf Tabellenebene verwendet werden, um dem Überwachungsteam Zugriff auf die gesamte **OfficeActivity**-Tabelle zu gewähren, ohne dabei auch Berechtigungen für eine andere Tabelle zu erteilen.

### <a name="access-considerations-with-multiple-workspaces"></a>Überlegungen zum Zugriff bei mehreren Arbeitsbereichen

Wenn Sie in Ihrer Organisation über verschiedene Entitäten, Niederlassungen oder Gebiete verfügen, und diese jeweils eigene Sicherheitsteams haben, die Zugriff auf Microsoft Sentinel benötigen, sollten Sie separate Arbeitsbereiche für jede Entität oder Niederlassung verwenden. Implementieren Sie die separaten Arbeitsbereiche entweder in einem einzelnen Azure AD-Mandanten oder mandantenübergreifend mithilfe von Azure Lighthouse. 

Es ist ebenfalls möglich, dass Ihr zentrales SOC-Team einen zusätzlichen, optionalen Microsoft Sentinel-Arbeitsbereich nutzt, um zentralisierte Artefakte wie Analyseregeln oder Arbeitsmappen zu verwalten.

Weitere Informationen finden Sie unter [Vereinfachung der Arbeit mit mehreren Arbeitsbereichen](#simplify-working-with-multiple-workspaces).


## <a name="technical-best-practices-for-creating-your-workspace"></a>Bewährte technische Methoden für das Erstellen Ihres Arbeitsbereichs

Beachten Sie beim Erstellen des Log Analytics-Arbeitsbereichs für Microsoft Sentinel die folgenden bewährten Methoden:

- Integrieren Sie bei der **Benennung Ihres Arbeitsbereichs** *Microsoft Sentinel* oder einen anderen Indikator in den Namen, sodass dieser leicht von Ihren anderen Arbeitsbereichen unterschieden werden kann.

- **Verwenden Sie den gleichen Arbeitsbereich sowohl für Microsoft Sentinel als auch für Microsoft Defender für Cloud,** sodass alle von Microsoft Defender für Cloud gesammelten Protokolle auch von Microsoft Sentinel erfasst und verwendet werden können. Der von Microsoft Defender für Cloud erstellte Standardarbeitsbereich wird nicht als verfügbarer Arbeitsbereich für Microsoft Sentinel angezeigt.

- **Verwenden Sie einen dedizierten Arbeitsbereichscluster, wenn Ihre voraussichtliche Datenerfassung etwa bei 1 TB pro Tag oder höher liegt.** Mit einem [dedizierten Cluster](../azure-monitor/logs/logs-dedicated-clusters.md) können Sie Ressourcen für Ihre Microsoft Sentinel-Daten schützen, wodurch eine bessere Abfrageleistung für große Datensätze ermöglicht wird. Dedizierte Cluster bieten darüber hinaus die Möglichkeit, mehr Verschlüsselung und eine bessere Kontrolle über die Schlüssel Ihrer Organisation zu erhalten.

## <a name="simplify-working-with-multiple-workspaces"></a>Vereinfachung der Arbeit mit mehreren Arbeitsbereichen

Wenn Sie mit mehreren Arbeitsbereichen arbeiten müssen, sollten Sie das Management und die Untersuchung von Incidents vereinfachen, indem Sie [alle Incidents aus jeder Microsoft Sentinel-Instanz an einem einzigen Speicherort zusammenfassen und auflisten](multiple-workspace-view.md).

Nutzen Sie [arbeitsbereichsübergreifende Abfragen](extend-sentinel-across-workspaces-tenants.md), um auf Daten zu verweisen, die sich in anderen Microsoft Sentinel-Arbeitsbereichen befinden, z. B. in [arbeitsbereichsübergreifenden Arbeitsmappen](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks).

Die Verwendung von arbeitsbereichsübergreifender Abfragen ist vor allem dann sinnvoll, wenn wertvolle Informationen, die von Nutzen für Ihre aktuelle Aktion wären, in einem anderen Arbeitsbereich, Abonnement oder Mandanten gespeichert sind. Der folgende Code enthält ein Beispiel für eine arbeitsbereichsübergreifende Abfrage:

```Kusto
union Update, workspace("contosoretail-it").Update, workspace("WORKSPACE ID").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

Weitere Informationen finden Sie unter [Erweitern von Microsoft Sentinel auf Arbeitsbereiche und Mandanten](extend-sentinel-across-workspaces-tenants.md).
## <a name="next-steps"></a>Nächste Schritte


> [!div class="nextstepaction"]
>[Entwerfen der Microsoft Sentinel-Arbeitsbereichsarchitektur](design-your-workspace-architecture.md)

> [!div class="nextstepaction"]
>[Beispiele für Microsoft Sentinel-Arbeitsbereichsentwürfe](sample-workspace-designs.md)

> [!div class="nextstepaction"]
>[Durchführen des Onboardings für Microsoft Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Einblick in Warnungen](get-visibility.md)
