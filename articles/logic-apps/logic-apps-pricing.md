---
title: Verwendungsmessung, Abrechnung und Preise
description: Hier erfahren Sie, wie die Modelle zu Verwendungsmessung, Abrechnung und Preisen in den Azure Logic Apps funktionieren.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 11/10/2021
ms.openlocfilehash: 90e0703313db99bee1a7d06e9c1bc9d0efac135f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372964"
---
# <a name="usage-metering-billing-and-pricing-models-for-azure-logic-apps"></a>Modelle für Verwendungsmessung, Abrechnung und Preise in den Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) unterstützt Sie dabei, automatisierte Integrationsworkflows zu erstellen und auszuführen, die in der Cloud abskaliert werden können. In diesem Artikel werden Modelle für Verwendungsmessung, Abrechnung und Preise für Azure Logic Apps und die zugehörigen Ressourcen beschrieben. Informationen zu bestimmten Preisen, Kostenplanung oder verschiedenen Hostingumgebungen finden Sie in den folgenden Artikeln:

* [Preise für Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps)
* [Planen und Verwalten von Kosten für Azure Logic Apps](plan-manage-costs.md)
* [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung](single-tenant-overview-compare.md)

<a name="consumption-pricing"></a>

## <a name="consumption-multi-tenant"></a>Verbrauch (mehrere Mandanten)

In Azure Logic Apps mit mehreren Mandanten wird für eine Logik-App und ihren Workflow der [**Verbrauchsplan**](https://azure.microsoft.com/pricing/details/logic-apps) für Preise und Abrechnung verwendet. Sie erstellen solche Logik-Apps auf unterschiedliche Weisen, z. B. wenn Sie den Ressourcentyp **Logik-App (Verbrauch)** auswählen, die Erweiterung **Azure Logic Apps (Verbrauch)** in Visual Studio Code verwenden oder [Automatisierungsaufgaben](create-automation-tasks-azure-resources.md) verwenden.

In der folgenden Tabelle wird zusammengefasst, wie das Verbrauchsmodell die Verbrauchsmessung und Abrechnung für die folgenden Komponenten verarbeitet, wenn es mit einer Logik-App und einem Workflow in Azure Logic Apps mit mehreren Mandanten verwendet wird:

| Komponente | Messung und Abrechnung |
| ----------|----------------------|
| Trigger- und Aktionsvorgänge | Das Verbrauchsmodell enthält eine *anfängliche Anzahl* von kostenlosen integrierten Vorgängen pro Azure-Abonnement, die ein Workflow ausführen kann. Über diesem Wert gilt für *jede Ausführung* die Verbrauchsmessung, und die Abrechnung erfolgt nach den [*Aktionspreisen* für den Verbrauchsplan](https://azure.microsoft.com/pricing/details/logic-apps). Für andere Vorgangstypen wie verwaltete Connectors erfolgt die Abrechnung nach den Preisen für [*Standard*- oder *Unternehmensconnectors* des Verbrauchsplans](https://azure.microsoft.com/pricing/details/logic-apps). Weitere Informationen finden Sie unter [Trigger- und Aktionsvorgänge im Verbrauchsmodell](#consumption-operations). |
| Speichervorgänge | Die Verbrauchsmessung gilt *nur für den Speicherverbrauch im Zusammenhang mit der Datenaufbewahrung*, z. B. das Speichern von Ein- und Ausgaben aus dem Ausführungsverlauf Ihres Workflows. Die Abrechnung erfolgt entsprechend den [Preisen für die Datenaufbewahrung des Verbrauchsplans](https://azure.microsoft.com/pricing/details/logic-apps/). Weitere Informationen finden Sie unter [Speichervorgänge](#storage-operations). |
| Integrationskonten | Die Verbrauchsmessung wird basierend auf dem Integrationskontotyp angewendet, den Sie mit Ihrer Logik-App erstellen und verwenden. Für die Abrechnung gelten die [Preise für *Integrationskonten*](https://azure.microsoft.com/pricing/details/logic-apps/), es sei denn, die Logik-App wird in einer [Integrationsdienstumgebung (Integration Service Environment, ISE)](#ise-pricing) ausgeführt. Weitere Informationen finden Sie unter [Integrationskonten](#integration-accounts). |
|||

<a name="consumption-operations"></a>

### <a name="trigger-and-action-operations-in-the-consumption-model"></a>Trigger- und Aktionsvorgänge im Verbrauchsmodell

Mit Ausnahme der anfänglichen Anzahl kostenloser integrierter Vorgangsausführungen pro Azure-Abonnement, die ein Workflow ausführen kann, wird ein Vorgang im Verbrauchsmodell auf Grundlage *jeder Ausführung* gemessen und abgerechnet. Dies ist unabhängig davon, ob der gesamte Workflow erfolgreich ausgeführt, abgeschlossen oder sogar instanziiert wird. Ein Vorgang wird in der Regel einmal ausgeführt, [es sei denn, für den Vorgang wurden Wiederholungsversuche aktiviert](#other-operation-behavior). Umgekehrt wird bei einer Ausführung in der Regel nur ein einzelner Aufruf ausgeführt, [es sei denn, der Vorgang unterstützt und ermöglicht die Blockerstellung oder Paginierung, um große Datenmengen abzurufen](logic-apps-handle-large-messages.md). Wenn Blockerstellung oder Paginierung aktiviert ist, verfügt eine Vorgangsausführung möglicherweise über mehrere Aufrufe. Mit dem Verbrauchsmodell wird ein Vorgang *pro Ausführung und nicht pro Aufruf* gemessen und abgerechnet.

Angenommen, ein Workflow beginnt mit einem Abfragetrigger, der Datensätze abruft, indem regelmäßig ausgehende Aufrufe an einen Endpunkt durchgeführt werden. Der ausgehende Aufruf wird gemessen und als einzelne Ausführung abgerechnet, unabhängig davon, ob der Trigger ausgelöst oder übersprungen wird, z. B. wenn ein Trigger einen Endpunkt überprüft, aber keine Daten oder Ereignisse findet. Der Triggerzustand steuert, ob die Workflowinstanz erstellt und ausgeführt wird. Nehmen wir nun an, dass der Vorgang außerdem die Blockerstellung oder Paginierung unterstützt und diese aktiviert ist. Wenn der Vorgang 10 Aufrufe ausführen muss, um alle Daten abzurufen, wird der Vorgang trotz mehrerer Aufrufe weiterhin als *eine einzelne Ausführung* gemessen und abgerechnet.

In der folgenden Tabelle wird zusammengefasst, wie das Verbrauchsmodell die Verbrauchsmessung und Abrechnung für diese Vorgangstypen verarbeitet, wenn es mit einer Logik-App und einem Workflow in Azure Logic Apps mit mehreren Mandanten verwendet wird:

| Vorgangsart | BESCHREIBUNG | Messung und Abrechnung |
|----------------|-------------|----------------------|
| [*Integriert*](../connectors/built-in.md) | Diese Vorgänge werden direkt und nativ mit der Azure Logic Apps-Runtime ausgeführt. Im Designer finden Sie diese Vorgänge unter der Bezeichnung **Integriert**. <p>Beispielsweise sind die Trigger „HTTP“ und „Anforderung“ integrierte Trigger. Die Aktionen „HTTP“ und „Antwort“ sind hingegen integrierte Aktionen. Andere integrierte Vorgänge umfassen Workflowsteuerungsaktionen wie Schleifen und Bedingungen, Datenvorgänge, Batchvorgänge usw. | Das Verbrauchsmodell enthält eine *anfängliche Anzahl von kostenlosen integrierten Vorgängen* pro Azure-Abonnement, die ein Workflow ausführen kann. Oberhalb dieser Anzahl gelten für Ausführungen von integrierten Vorgängen die [Preise für *Aktionen*](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Hinweis:** Einige Vorgänge für verwaltete Connectors sind *auch* als integrierte Vorgänge verfügbar, die in den ersten kostenlosen Vorgängen enthalten sind. Über die anfänglichen kostenlosen Vorgänge hinaus erfolgt die Abrechnung nach den [Preisen für *Aktionen*](https://azure.microsoft.com/pricing/details/logic-apps/), nicht nach den [Preisen für *Standard*- oder *Unternehmensconnectors*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Verwalteter Connector*](../connectors/managed.md) | Diese Vorgänge werden separat in Azure ausgeführt. Im Designer finden Sie diese Vorgänge unter der Bezeichnung **Standard** oder **Unternehmen**. | Für diese Ausführungen von Vorgängen gelten die [Preise für *Standard*- oder *Unternehmensconnectors*](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Hinweis:** Für die Vorgangsausführungen von Vorschau-Unternehmensconnectors gelten die [Preise nach Verbrauch für *Standardconnectors*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Benutzerdefinierter Connector*](../connectors/apis-list.md#custom-apis-and-connectors) | Diese Vorgänge werden separat in Azure ausgeführt. Im Designer finden Sie diese Vorgänge unter der Bezeichnung **Benutzerdefiniert**. Informationen zu Grenzwerten für die Anzahl von Connectors, Durchsatz und Timeout finden Sie unter [Grenzwerte für einen benutzerdefinierten Connector](logic-apps-limits-and-config.md#custom-connector-limits) in Azure Logic Apps. | Für diese Ausführungen von Vorgängen gelten die [Preise für *Standardconnectors*](https://azure.microsoft.com/pricing/details/logic-apps/). |
||||

Weitere Informationen zur Funktionsweise des Verbrauchsmodells mit Vorgängen, die in anderen Vorgängen wie Schleifen ausgeführt werden oder mehrere Elemente wie Arrays verarbeiten, sowie Wiederholungsrichtlinien finden Sie unter [Verhalten bei anderen Vorgängen](#other-operation-behavior).

<a name="consumption-cost-estimation-tips"></a>

### <a name="cost-estimation-tips-for-the-consumption-model"></a>Tipps zur Kostenschätzung für das Verbrauchsmodell

Diese Tipps helfen Ihnen beim Schätzen genauerer Nutzungskosten:

* Berücksichtigen Sie die mögliche Anzahl von Nachrichten oder Ereignissen, die an einem beliebigen Tag eingehen, statt Ihre Berechnungen nur auf das Abrufintervall zu stützen.

* Wenn ein Ereignis oder eine Nachricht die Auslösekriterien erfüllt, versuchen viele Trigger sofort, alle anderen wartenden Ereignisse oder Nachrichten, die die Kriterien erfüllen, zu lesen. Dieses Verhalten bedeutet, dass auch bei Auswahl eines längeren Abrufintervalls der Trigger aufgrund der Anzahl der wartenden Ereignisse oder Nachrichten, die für das Starten von Workflows qualifiziert sind, ausgelöst wird. Zu den Triggern, die diesem Verhalten folgen, zählen Azure Service Bus und Azure Event Hub.

  Angenommen, Sie haben einen Trigger eingerichtet, der täglich einen Endpunkt überprüft. Wenn der Trigger den Endpunkt überprüft und 15 Ereignisse findet, die die Kriterien erfüllen, wird der Trigger ausgelöst und führt den entsprechenden Workflow 15 mal aus. Der Dienst Azure Logic Apps misst alle Aktionen, die diese 15 Workflows ausführen, einschließlich der Triggeranforderungen.

<a name="standard-pricing"></a>

## <a name="standard-single-tenant"></a>Standard (ein Mandant)

In Azure Logic Apps mit einem Mandanten wird für eine Logik-App und ihren Workflow der [**Standardplan**](https://azure.microsoft.com/pricing/details/logic-apps/) für Preise und Abrechnung verwendet. Sie erstellen solche Logik-Apps auf unterschiedliche Weisen, z. B. wenn Sie den Ressourcentyp **Logik-App (Standard)** auswählen oder die Erweiterung **Azure Logic Apps (Standard)** in Visual Studio Code verwenden. Für dieses Preismodell müssen Logik-Apps einen Hostingplan und einen Tarif verwenden. Dies unterscheidet sich vom Verbrauchsplan insofern, dass Ihnen reservierte Kapazitäten und dedizierte Ressourcen in Rechnung gestellt werden, unabhängig davon, ob Sie sie verwenden.

Wenn Sie Logik-Apps mit dem Ressourcentyp **Logik-App (Standard)** erstellen oder bereitstellen, können Sie den Hostingplan „Workflow-Standard“ in allen Azure-Regionen verwenden. Sie haben auch die Möglichkeit, eine vorhandene **App Service-Umgebung v3**-Ressource als Bereitstellungsort zu wählen, aber Sie können nur den App Service-Plan mit dieser Option verwenden. Wenn Sie diese Option auswählen, werden Ihnen die vom App Service-Plan verwendeten Instanzen und die Ausführung Ihrer Logik-App-Workflows in Rechnung gestellt. Es fallen keine weiteren Gebühren an.

> [!IMPORTANT]
> Die folgenden Pläne und Ressourcen sind mit der öffentlichen Freigabe des Ressourcentyps **Logic App (Standard)** in Azure-Regionen nicht mehr verfügbar oder werden nicht mehr unterstützt: Funktionen Premium-Plan, App Service Environment v1 und App Service Environment v2. Der App Service-Plan ist nicht verfügbar und wird nicht unterstützt, mit Ausnahme von ASEv3.

In der folgenden Tabelle wird zusammengefasst, wie das Standardmodell die Verbrauchsmessung und Abrechnung für die folgenden Komponenten verarbeitet, wenn es mit einer Logik-App und einem Workflow in Azure Logic Apps mit einem Mandanten verwendet wird:

| Komponente | Messung und Abrechnung |
|-----------|----------------------|
| Virtuelle CPU (vCPU) und Arbeitsspeicher | Für das Standardmodell *muss* Ihre Logik-App den **Workflow Standard**-Hostingplan und einen Tarif verwenden, der die Ressourcenebenen und Preise für Compute- und Arbeitsspeicherkapazität bestimmt. Weitere Informationen finden Sie unter [Tarife im Standardmodell](#standard-pricing-tiers). |
| Trigger- und Aktionsvorgänge | Das Standardmodell umfasst eine *unbegrenzte Anzahl* kostenloser integrierter Vorgänge, die Ihr Workflow ausführen kann. <p>Wenn Ihr Workflow Vorgänge für verwaltete Connectors verwendet, gilt die Messung für diese Vorgänge für *jeden Aufruf*, während für die Abrechnung [dieselben Preise für *Standard*- oder *Unternehmensconnectors* wie im Verbrauchsplan](https://azure.microsoft.com/pricing/details/logic-apps) gelten. Weitere Informationen finden Sie unter [Trigger- und Aktionsvorgänge im Standardmodell](#standard-operations). |
| Speichervorgänge | Die Messung gilt für alle Speichervorgänge, die von Azure Logic Apps ausgeführt werden. Speichervorgänge werden beispielsweise ausgeführt, wenn der Dienst Eingaben und Ausgaben aus dem Ausführungsverlauf des Workflows speichert. Die Abrechnung erfolgt nach dem von Ihnen gewählten [Tarif](#standard-pricing-tiers). Weitere Informationen finden Sie unter [Speichervorgänge](#storage-operations). |
| Integrationskonten | Wenn Sie ein Integrationskonto für Ihre Logik-App erstellen, basiert die Messung auf dem Typ des erstellten Integrationskontos. Die Abrechnung erfolgt gemäß den [Preisen für *Integrationskonten*](https://azure.microsoft.com/pricing/details/logic-apps/). Weitere Informationen finden Sie unter [Integrationskonten](#integration-accounts). |
|||

<a name="standard-pricing-tiers"></a>

### <a name="pricing-tiers-in-the-standard-model"></a>Tarife im Standardmodell

Der Tarif, den Sie für die Messung und Abrechnung Ihrer Logik-App auswählen, umfasst bestimmte Computekapazitäten in virtuellen CPU- (vCPU) und Arbeitsspeicherressourcen. Derzeit ist nur der Hostingplan **Workflow Standard** für den Ressourcentyp **Logik-App (Standard)** verfügbar und bietet die folgenden Tarife:

| Tarif | Virtuelle CPU (vCPU) | Arbeitsspeicher (GB) |
|--------------|--------------------|-------------|
| **WS1** | 1 | 3,5 |
| **WS2** | 2 | 7 |
| **WS3** | 4 | 14 |
||||

> [!IMPORTANT]
>
> Das folgende Beispiel dient nur zur Veranschaulichung und enthält Beispielschätzungen, um die Funktionsweise eines Tarifs allgemein zu zeigen. 
> Informationen zu spezifischen vCPU- und Arbeitsspeicherpreisen basierend auf bestimmten Regionen, in denen Azure Logic Apps verfügbar ist, finden Sie im [Standardplan für eine ausgewählte Region auf der Seite mit Azure Logic Apps-Preisinformationen](https://azure.microsoft.com/pricing/details/logic-apps/).
>
> Angenommen, in einer Beispielregion gelten für die folgenden Ressourcen diese Stundensätze:
>
> | Ressource | Stundensatz (Beispielregion) |
> |----------|-----------------------------|
> | **vCPU** | 0,192 USD pro vCPU |
> | **Arbeitsspeicher** | 0,0137 USD pro GB |
> |||
>
> Die folgende Berechnung liefert eine geschätzte monatliche Rate:
>
> <*Monatliche Rate*> = 730 Stunden (pro Monat) * [(<*Anzahl_vCPU*> * <*Stundensatz_vCPU*>) + (<*Menge_GB_Arbeitsspeicher*> * <*Stundensatz_GB_Arbeitsspeicher*>)]
>
> Auf Grundlage der obigen Informationen zeigt die folgende Tabelle die geschätzten Monatsraten für die einzelnen Tarife und die darin enthaltenen Ressourcen:
>
> | Tarif | Virtuelle CPU (vCPU) | Arbeitsspeicher (GB) | Monatliche Rate (Beispielregion) |
> |--------------|--------------------|-------------|------------------------------|
> | **WS1** | 1 | 3,5 | 175,16 USD |
> | **WS2** | 2 | 7 | 350,33 USD |
> | **WS3** | 4 | 14 | 700,65 USD |
> |||||

<a name="standard-operations"></a>

### <a name="trigger-and-action-operations-in-the-standard-model"></a>Trigger- und Aktionsvorgänge im Standardmodell

Mit Ausnahme der unbegrenzten kostenlosen integrierten Vorgangsausführungen, die ein Workflow ausführen kann, misst und berechnet das Standardmodell einen Vorgang auf Grundlage *jedes Aufrufs*, unabhängig davon, ob der gesamte Workflow erfolgreich ausgeführt, abgeschlossen oder sogar instanziiert wird. Ein Vorgang wird in der Regel einmal ausgeführt, [es sei denn, für den Vorgang wurden Wiederholungsversuche aktiviert](#other-operation-behavior). Umgekehrt wird bei einer Ausführung in der Regel nur ein einzelner Aufruf ausgeführt, [es sei denn, der Vorgang unterstützt und ermöglicht die Blockerstellung oder Paginierung, um große Datenmengen abzurufen](logic-apps-handle-large-messages.md). Wenn Blockerstellung oder Paginierung aktiviert ist, verfügt eine Vorgangsausführung möglicherweise über mehrere Aufrufe. Mit dem Standardmodell wird ein Vorgang *pro Aufruf und nicht pro Ausführung* gemessen und abgerechnet.

Angenommen, ein Workflow beginnt mit einem Abfragetrigger, der Datensätze abruft, indem regelmäßig ausgehende Aufrufe an einen Endpunkt durchgeführt werden. Der ausgehende Aufruf wird gemessen und abgerechnet, unabhängig davon, ob der Trigger ausgelöst oder übersprungen wird. Der Triggerzustand steuert, ob die Workflowinstanz erstellt und ausgeführt wird. Nehmen wir nun an, dass der Vorgang außerdem die Blockerstellung oder Paginierung unterstützt und diese aktiviert ist. Wenn der Vorgang 10 Aufrufe ausführen muss, um alle Daten abzurufen, wird der Vorgang *pro Aufruf* gemessen und abgerechnet.

In der folgenden Tabelle wird zusammengefasst, wie das Standardmodell die Verbrauchsmessung und Abrechnung für Vorgangstypen verarbeitet, wenn es mit einer Logik-App und einem Workflow in Azure Logic Apps mit einem Mandanten verwendet wird:

| Vorgangsart | BESCHREIBUNG | Messung und Abrechnung |
|----------------|-------------|----------------------|
| [*Integriert*](../connectors/built-in.md) | Diese Vorgänge werden direkt und nativ mit der Azure Logic Apps-Runtime ausgeführt. Im Designer finden Sie diese Vorgänge unter der Bezeichnung **Integriert**. <p>Beispielsweise sind die Trigger „HTTP“ und „Anforderung“ integrierte Trigger. Die Aktionen „HTTP“ und „Antwort“ sind hingegen integrierte Aktionen. Andere integrierte Vorgänge umfassen Workflowsteuerungsaktionen wie Schleifen und Bedingungen, Datenvorgänge, Batchvorgänge usw. | Das Standardmodell umfasst unbegrenzte kostenlose integrierte Vorgänge. <p><p>**Hinweis:** Einige Vorgänge für verwaltete Connectors sind *auch* als integrierte Vorgänge verfügbar. Obwohl integrierte Vorgänge kostenlos sind, werden Vorgänge mit verwalteten Connectors im Standardmodell gemessen und abgerechnet. Dafür werden [dieselben Preise für *Standard*- oder *Unternehmensconnectors* verwendet wie im Verbrauchsmodell](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Verwalteter Connector*](../connectors/managed.md) | Diese Vorgänge werden separat in Azure ausgeführt. Im Designer finden Sie diese Vorgänge unter der zusammengefassten Bezeichnung **Azure**. | Im Standardmodell werden Vorgänge mit verwalteten Connectors gemäß [denselben Preisen für *Standard*- oder *Unternehmensconnectors* wie im Verbrauchsmodell](https://azure.microsoft.com/pricing/details/logic-apps/) gemessen und abgerechnet. <p><p>**Hinweis:** Für Unternehmensconnector-Vorgänge gelten die [Preise nach Verbrauch für *Standardconnectors*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Benutzerdefinierter Connector*](../connectors/apis-list.md#custom-apis-and-connectors) | Derzeit können Sie nur [benutzerdefinierte integrierte Connectorvorgänge](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) in Logik-App-Workflows mit einem Mandanten erstellen und verwenden. | Das Standardmodell umfasst unbegrenzte kostenlose integrierte Vorgänge. Informationen zu Grenzwerten für Durchsatz und Timeout finden Sie unter [Grenzwerte für einen benutzerdefinierten Connector](logic-apps-limits-and-config.md#custom-connector-limits) in Azure Logic Apps. |
||||

Weitere Informationen zur Funktionsweise des Standardmodells mit Vorgängen, die in anderen Vorgängen wie Schleifen ausgeführt werden oder mehrere Elemente wie Arrays verarbeiten, sowie Wiederholungsrichtlinien finden Sie unter [Verhalten bei anderen Vorgängen](#other-operation-behavior).

<a name="ise-pricing"></a>

## <a name="integration-service-environment-ise"></a>Integrationsdienstumgebung (Integration Service Environment, ISE)

Wenn Sie eine Logik-App mithilfe des Ressourcentyps **Logik-App (Verbrauch)** erstellen und sie in einer dedizierten [*Integrationsdienstumgebung* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) bereitstellen, wird für die Logik-App und ihren Workflow der [Tarif für Integrationsdienstumgebungen](https://azure.microsoft.com/pricing/details/logic-apps) für Preise und Abrechnung verwendet. Dieses Preismodell ist abhängig von Ihrer [ISE-Ebene oder *SKU*](connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). Dies unterscheidet sich vom Verbrauchsplan insofern, dass Ihnen reservierte Kapazitäten und dedizierte Ressourcen in Rechnung gestellt werden, unabhängig davon, ob Sie sie verwenden.

In der folgenden Tabelle wird zusammengefasst, wie das ISE-Modell die Messung und Abrechnung für Kapazität und andere dedizierte Ressourcen basierend auf der ISE-Ebene oder SKU verarbeitet:

| ISE SKU | Messung und Abrechnung |
|---------|----------------------|
| **Premium** | Die Basiseinheit bietet eine [feste Kapazität](logic-apps-limits-and-config.md#integration-service-environment-ise) und wird [für die SKU „Premium“ mit einem Stundensatz abgerechnet](https://azure.microsoft.com/pricing/details/logic-apps). Falls Sie mehr Durchsatz benötigen, können Sie bei der Erstellung Ihrer ISE oder nachträglich [weitere Skalierungseinheiten hinzufügen](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). Jede Skalierungseinheit wird mit einem [Stundensatz abgerechnet, der etwa der Hälfte des Satzes der Basiseinheit entspricht](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Informationen zu Grenzwerten und Kapazitäten finden Sie unter [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Developer** | Die Basiseinheit bietet eine [feste Kapazität](logic-apps-limits-and-config.md#integration-service-environment-ise) und wird [für die SKU „Developer“ mit einem Stundensatz abgerechnet](https://azure.microsoft.com/pricing/details/logic-apps). Diese SKU umfasst keine Vereinbarung zum Servicelevel (Service-Level Agreement, SLA), Funktion zum zentralen Hochskalieren der Kapazität oder Redundanz während der Wiederverwendung. Dies bedeutet, dass Verzögerungen oder Ausfallzeiten auftreten können. Der Dienst kann vorübergehend von Back-End-Updates unterbrochen werden. <p><p>**Wichtig**: Stellen Sie sicher, dass Sie diese SKU nur für Erkundung, Experimente, Entwicklung und Tests verwenden, nicht jedoch für die Produktion oder für Leistungstests. <p><p>Informationen zu Grenzwerten und Kapazitäten finden Sie unter [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

In der folgenden Tabelle wird zusammengefasst, wie das ISE-Modell die folgenden Komponenten behandelt, wenn es mit einer Logik-App und einem Workflow in einer ISE verwendet wird:

| Komponente | BESCHREIBUNG |
|-----------|-------------|
| Trigger- und Aktionsvorgänge | Das ISE-Modell umfasst kostenlose integrierte Vorgänge für verwaltete Connectors und benutzerdefinierte Connectors, die Ihr Workflow ausführen kann. Es gelten allerdings die [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise) und [Grenzwerte für benutzerdefinierte Connectors in Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). Weitere Informationen finden Sie unter [Trigger- und Aktionsvorgänge im ISE-Modell](#integration-service-environment-operations). |
| Speichervorgänge | Das ISE-Modell umfasst die kostenlose Speichernutzung, z. B. für die Datenaufbewahrung. Weitere Informationen finden Sie unter [Speichervorgänge](#storage-operations). |
| Integrationskonten | Das ISE-Modell enthält eine einzelne kostenlose Integrationskontoebene, die auf der ausgewählten ISE-SKU basiert. [Gegen Mehrkosten](https://azure.microsoft.com/pricing/details/logic-apps/) können Sie weitere Integrationskonten für Ihre ISE anlegen, die Sie bis zur [ISE-Obergrenze](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) nutzen können. Weitere Informationen finden Sie unter [Integrationskonten](#integration-accounts). |
|||

<a name="integration-service-environment-operations"></a>

### <a name="trigger-and-action-operations-in-the-ise-model"></a>Trigger- und Aktionsvorgänge im ISE-Modell

In der folgenden Tabelle wird zusammengefasst, wie das ISE-Modell die folgenden Vorgangstypen behandelt, wenn es mit einer Logik-App und einem Workflow in einer ISE verwendet wird:

| Vorgangsart | BESCHREIBUNG | Messung und Abrechnung |
|----------------|-------------|----------------------|
| [*Integriert*](../connectors/built-in.md) | Diese Vorgänge werden direkt und nativ mit der Azure Logic Apps-Runtime und in derselben ISE wie Ihr Logik-App-Workflow ausgeführt. Im Designer finden Sie diese Vorgänge unter der Bezeichnung **Integriert**, es wird aber für jeden Vorgang auch die Bezeichnung **CORE** angezeigt. <p>Beispielsweise sind die Trigger „HTTP“ und „Anforderung“ integrierte Trigger. Die Aktionen „HTTP“ und „Antwort“ sind hingegen integrierte Aktionen. Andere integrierte Vorgänge umfassen Workflowsteuerungsaktionen wie Schleifen und Bedingungen, Datenvorgänge, Batchvorgänge usw. | Das ISE-Modell enthält diese Vorgänge *kostenlos*, es gelten aber die [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| [*Verwalteter Connector*](../connectors/managed.md) | Unabhängig davon, ob es sich um *Standard*- oder *Unternehmensconnectors* handelt, werden Vorgänge für verwaltete Connectors in Ihrer ISE oder in Azure mit mehreren Mandanten ausgeführt. Dies hängt davon ab, ob für den Connector oder Vorgang die Bezeichnung **ISE** angezeigt wird. <p><p>- **ISE**-Bezeichnung: Diese Vorgänge werden in derselben ISE wie Ihre Logik-App ausgeführt und benötigen kein [lokales Datengateway](#data-gateway). <p><p>– Keine **ISE**-Bezeichnung: Diese Vorgänge werden in Azure mit mehreren Mandanten ausgeführt. | Das ISE-Modell enthält Vorgänge mit **ISE**- und ohne **ISE**- Bezeichnung *kostenlos*, es gelten aber die [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| [*Benutzerdefinierter Connector*](../connectors/apis-list.md#custom-apis-and-connectors) | Im Designer finden Sie diese Vorgänge unter der Bezeichnung **Benutzerdefiniert**. | Das ISE-Modell enthält diese Vorgänge *kostenlos*, es gelten aber die [Grenzwerte für benutzerdefinierte Connectors in Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). |
||||

Weitere Informationen zur Funktionsweise des ISE-Modells mit Vorgängen, die in anderen Vorgängen wie Schleifen ausgeführt werden oder mehrere Elemente wie Arrays verarbeiten, sowie Wiederholungsrichtlinien finden Sie unter [Verhalten bei anderen Vorgängen](#other-operation-behavior).

<a name="other-operation-behavior"></a>

## <a name="other-operation-behavior"></a>Verhalten bei anderen Vorgängen

Die folgende Tabelle zeigt die Funktionsweise des Verbrauchsmodells, Standardmodells und ISE-Modells mit Vorgängen, die in anderen Vorgängen wie Schleifen ausgeführt werden oder mehrere Elemente wie Arrays verarbeiten, und Wiederholungsrichtlinien:

| Vorgang | BESCHREIBUNG | Nutzung | Standard | ISE |
|-----------|-------------|-------------|----------|-----|
| [Schleifenaktionen](logic-apps-control-flow-loops.md) | Eine Schleifenaktion, z. B. eine **For each**- oder **Until**-Schleife, kann andere Aktionen enthalten, die während jedes Schleifendurchlaufs ausgeführt werden. | Mit Ausnahme der anfänglichen Anzahl der integrierten Vorgänge werden die Schleifenaktion und jede Aktion in der Schleife bei jedem Schleifendurchlauf gemessen. Wenn eine Aktion Elemente in einer Sammlung verarbeitet, z. B. eine Liste oder ein Array, wird die Anzahl der Elemente ebenfalls bei der Messung berücksichtigt. <p><p>Angenommen, Sie verfügen über eine **For each**-Schleife mit Aktionen, die eine Liste verarbeiten. Der Dienst multipliziert die Anzahl von Listenelementen mit der Anzahl von Aktionen in der Schleife. Anschließend wird die Aktion hinzugefügt, mit der die Schleife gestartet wird. Daher lautet die Berechnung für eine Liste mit zehn Elementen (10 * 1) + 1, sodass sich 11 Aktionsausführungen ergeben. <p><p>Die Preise basieren darauf, ob der Vorgangstyp „Integriert“, „Standard“ oder „Unternehmen“ lautet. | Mit Ausnahme der eingeschlossenen integrierten Vorgänge wie im Verbrauchsmodell. | Nicht gemessen oder abgerechnet. |
| [Wiederholungsrichtlinien](logic-apps-exception-handling.md#retry-policies) | Bei unterstützten Vorgängen können Sie eine grundlegende Ausnahme- und Fehlerbehandlung implementieren, indem Sie eine [Wiederholungsrichtlinie](logic-apps-exception-handling.md#retry-policies) einrichten. | Mit Ausnahme der anfänglichen Anzahl integrierter Vorgänge werden die ursprüngliche Ausführung plus jede wiederholte Ausführung gemessen. Beispielsweise werden für eine Aktion, die mit fünf Wiederholungen ausgeführt wird, sechs Ausführungen gemessen und berechnet. <p><p>Die Preise basieren darauf, ob der Vorgangstyp „Integriert“, „Standard“ oder „Unternehmen“ lautet. | Mit Ausnahme der eingeschlossenen integrierten Vorgänge wie im Verbrauchsmodell. | Nicht gemessen oder abgerechnet. |
||||||

<a name="storage-operations"></a>

## <a name="storage-operations"></a>Speichervorgänge

Azure Logic Apps verwendet [Azure Storage](../storage/index.yml) für alle erforderlichen Speichertransaktionen, z. B. die Verwendung von Warteschlangen zum Planen von Triggervorgängen oder die Verwendung von Tabellen und Blobs zum Speichern von Workflowzuständen. Basierend auf den Vorgängen in Ihrem Workflow variieren die Speicherkosten, da verschiedene Trigger, Aktionen und Nutzlasten zu unterschiedlichen Speichervorgängen und -anforderungen führen. Der Dienst speichert außerdem Eingaben und Ausgaben aus dem Ausführungsverlauf Ihres Workflows. Dabei gilt der [Grenzwert für die Aufbewahrung des Ausführungsverlaufs](logic-apps-limits-and-config.md#run-duration-retention-limits) für die Logik-App-Ressource. Sie können diesen Aufbewahrungsgrenzwert auf Logik-App-Ressourcenebene und nicht auf Workflowebene verwalten.

In der folgenden Tabelle wird zusammengefasst, wie Messung und Abrechnung für Speichervorgänge im Verbrauchs-, Standard- und ISE-Modell verarbeitet werden:

| Modell | BESCHREIBUNG | Messung und Abrechnung |
|-------|-------------|----------------------|
| Verbrauch (mehrere Mandanten) | Speicherressourcen und -nutzung werden an die Logik-App-Ressource angefügt. | Messung und Abrechnung beziehen sich *nur auf den Speicherverbrauch im Zusammenhang mit der Datenaufbewahrung*. Es gelten die [Preise für die Datenaufbewahrung für den Verbrauchsplan](https://azure.microsoft.com/pricing/details/logic-apps). |
| Standard (ein Mandant) | Sie können Ihr eigenes Azure-[Speicherkonto](../azure-functions/storage-considerations.md#storage-account-requirements) verwenden. Dies bietet Ihnen mehr Kontrolle und Flexibilität bei den Daten Ihres Workflows. |  Messung und Abrechnung erfolgen nach dem [Azure Storage-Preismodell](https://azure.microsoft.com/pricing/details/storage/). Speicherkosten werden in Ihrer Azure-Rechnung separat aufgeführt. <p><p>**Tipp:** Um eine Vorstellung von der Anzahl der Speichervorgänge, die ein Workflow ausführen könnte, und deren Kosten zu erhalten, verwenden Sie den [Logic Apps-Speicherrechner](https://logicapps.azure.com/calculator). Sie können entweder einen Beispielworkflow auswählen oder eine vorhandene Workflowdefinition verwenden. Die erste Berechnung schätzt die Anzahl der Speichervorgänge in Ihrem Workflow. Sie können diese Zahlen dann verwenden, um mögliche Kosten mithilfe des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) zu schätzen. Weitere Informationen finden Sie unter [Abschätzen der Speicheranforderungen und -kosten für Workflows in Azure Logic Apps für nur einen Mandanten](estimate-storage-costs.md). |
| Integrationsdienstumgebung (Integration Service Environment, ISE) | Speicherressourcen und -nutzung werden an die Logik-App-Ressource angefügt. | Nicht gemessen oder abgerechnet. |
||||

Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Anzeigen von Metriken für Ausführungen und Speichernutzung](plan-manage-costs.md#monitor-billing-metrics)
* [Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Lokales Datengateway

Das [lokale Datengateway](../logic-apps/logic-apps-gateway-install.md) ist eine gesonderte Azure-Ressource, die Sie erstellen, damit Ihre Logik-App-Workflows über bestimmte vom Gateway unterstützte Connectors auf lokale Daten zugreifen können. Für die Gatewayressource selbst fallen keine Gebühren an, aber für Vorgänge, die über das Gateway ausgeführt werden. Hierfür gelten die Preise und das Abrechnungsmodell, die von Ihrer Logik-App verwendet werden.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrationskonten

Ein [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) ist eine separate Azure-Ressource, die Sie als Container erstellen, um B2B-Artefakte (Business-to-Business) wie Handelspartner, Vereinbarungen, Schemas, Zuordnungen usw. zu definieren und zu speichern. Nachdem Sie dieses Konto erstellt und diese Artefakte definiert haben, verknüpfen Sie das Konto mit Ihrer Logik-App, damit Sie die Artefakte und verschiedene B2B-Vorgänge in Workflows verwenden können. So können Sie Integrationslösungen untersuchen, erstellen und testen, die [EDI](logic-apps-enterprise-integration-b2b.md)- und [XML-Verarbeitungsfunktionen](logic-apps-enterprise-integration-xml.md) verwenden.

In der folgenden Tabelle wird zusammengefasst, wie Messung und Abrechnung für Integrationskonten im Verbrauchs-, Standard- und ISE-Modell verarbeitet werden:

| Modell | Messung und Abrechnung |
|-------|----------------------|
| Verbrauch (mehrere Mandanten) | Für Messung und Abrechnung werden die [Preise für Integrationskonten](https://azure.microsoft.com/pricing/details/logic-apps/) auf Grundlage der von Ihnen verwendeten Kontoebene genutzt. |
| Standard (ein Mandant) | Für Messung und Abrechnung werden die [Preise für Integrationskonten](https://azure.microsoft.com/pricing/details/logic-apps/) auf Grundlage der von Ihnen verwendeten Kontoebene genutzt. |
| ISE | Dieses Modell umfasst ein einzelnes Integrationskonto, das auf Ihrer ISE-SKU basiert. [Gegen Mehrkosten](https://azure.microsoft.com/pricing/details/logic-apps/) können Sie weitere Integrationskonten für Ihre ISE anlegen, die Sie bis zur [ISE-Obergrenze](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) nutzen können. |
|||

Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Erstellen und Verwalten von Integrationskonten](logic-apps-enterprise-integration-create-integration-account.md)
* [Grenzwerte für Integrationskonten in Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="other-items-not-metered-or-billed"></a>Andere Elemente, die nicht gemessen oder abgerechnet werden

In allen Preismodellen werden die folgenden Elemente nicht gemessen oder abgerechnet:

* Aktionen, die nicht ausgeführt wurden, weil der Workflow vor Abschluss beendet wurde
* Deaktivierte Logik-Apps oder Workflows, da für sie keine neuen Instanzen erstellt werden können, wenn sie inaktiv sind

## <a name="next-steps"></a>Nächste Schritte

* [Planen und Verwalten von Kosten für Azure Logic Apps](plan-manage-costs.md)
