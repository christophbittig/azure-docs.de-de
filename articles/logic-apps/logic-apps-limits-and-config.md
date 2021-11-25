---
title: Referenzleitfaden zu Grenzwerten und zur Konfiguration
description: Referenzhandbuch zu Grenzwerten und Konfigurationsinformationen für Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: rohithah, rarayudu, azla
ms.topic: reference
ms.date: 11/02/2021
ms.openlocfilehash: 68d587888a915ed317b6a31f9000d0086adda21b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283484"
---
# <a name="limits-and-configuration-reference-for-azure-logic-apps"></a>Grenzwert- und Konfigurationsreferenz für Azure Logic Apps

> Für Power Automate lesen Sie bitte den Abschnitt [Grenzwerte und Konfiguration in Power Automate](/power-automate/limits-and-config).

In diesem Artikel werden die Grenzwert- und Konfigurationsinformationen für Azure Logic Apps und ähnliche Ressourcen beschrieben. Wählen Sie basierend auf Ihrem Szenario, den Lösungsanforderungen, den gewünschten Funktionen sowie der Umgebung, in der die Workflows ausgeführt werden sollen, den **Logik-App**-Ressourcentyp aus, um Logik-App-Workflows zu erstellen.

> [!NOTE]
> Viele Grenzwerte sind in den verfügbaren Umgebungen, in denen Azure Logic Apps ausgeführt wird, gleich, aber es gibt Unterschiede, auf die hingewiesen wird. 

In der folgenden Tabelle werden die Unterschiede zwischen dem ursprünglichen Ressourcentyp **Logik-App (Verbrauch)** und dem Ressourcentyp **Logik-App (Standard)** kurz zusammengefasst. Außerdem lernen Sie die Unterschiede zwischen einer *Einzelmandantenumgebung* und einer *Umgebung mit mehreren Mandanten* sowie der *Integrationsdienstumgebung* (Integration Service Environment, ISE) in Hinblick auf Bereitstellung, Hosten und Ausführung Ihrer Logik-App-Workflows kennen.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="definition-limits"></a>

## <a name="workflow-definition-limits"></a>Workflowdefinitionsgrenzwerte

In den folgenden Tabellen sind die Werte für eine einzelne Workflowdefinition aufgeführt:

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Workflows pro Region und Abonnement | 1 000 Workflows ||
| Trigger pro Workflow | 10 Trigger | Dieser Grenzwert gilt nur, wenn Sie an der JSON-Workflowdefinition arbeiten, unabhängig davon, ob sie in der Codeansicht oder in einer ARM-Vorlage (Azure Resource Manager) und nicht im Designer vorliegt. |
| Aktionen pro Workflow | 500 Aktionen | Zur Erhöhung dieses Grenzwerts können Sie bei Bedarf geschachtelte Workflows verwenden. |
| Schachtelungstiefe von Aktionen | 8 Aktionen | Zur Erhöhung dieses Grenzwerts können Sie bei Bedarf geschachtelte Workflows verwenden. |
| Trigger oder Aktion: Maximale Namenslänge | 80 Zeichen ||
| Trigger oder Aktion: Maximale Eingabe- oder Ausgabegröße | 104 857 600 Bytes <br>(105 MB) | Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Aktion: Maximale Kombinierte Eingabe- und Ausgabegröße | 209 715 200 Bytes <br>(210 MB) | Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Ausdruckszeichenlimit | 8 192 Zeichen ||
| `description` - Maximale Länge | 256 Zeichen ||
| `parameters` - Maximale Anzahl von Elementen | 50 Parameter ||
| `outputs` - Maximale Anzahl Elemente | 10 Ausgaben ||
| `trackedProperties` - Maximale Größe | 8\.000 Zeichen ||
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Grenzwerte für Ausführungsdauer und Aufbewahrungsverlauf

In der folgenden Tabelle sind die Werte für eine einzelne Workflow-Ausführung aufgeführt:

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| Aufbewahrung des Ausführungsverlaufs im Speicher | 90 Tage | 90 Tage <br>(Standardwert) | 366 Tage | Die Zeitdauer, für die der Workflow-Ausführungsverlauf im Speicher verbleibt, nachdem eine Ausführung gestartet wurde. <p><p>**Hinweis**: Wenn die Ausführungsdauer eines Workflows den aktuellen Aufbewahrungsgrenzwert überschreitet, wird die Ausführung aus dem Ausführungsverlauf im Speicher entfernt. Wenn eine Ausführung nach Erreichen des Aufbewahrungslimits nicht sofort entfernt wird, wird sie innerhalb von 7 Tagen entfernt. <p><p>Egal, ob eine Ausführung abgeschlossen wird oder einen Timeout hat, wird die Aufbewahrung des Ausführungsverlaufs immer anhand der Startzeit der Ausführung und des durch die Workfloweinstellung [**Aufbewahrung des Ausführungsverlaufs in Tagen**](#change-retention) festgelegten aktuellen Grenzwerts berechnet. Unabhängig vom vorherigen Grenzwert wird immer der aktuelle Grenzwert zum Berechnen der Aufbewahrung verwendet. <p><p>Weitere Informationen finden Sie unter [Ändern der Dauer und Aufbewahrung des Ausführungsverlaufs im Speicher](#change-retention). |
| Ausführungsdauer | 90 Tage | - Zustandsvoller Workflow: 90 Tage <br>(Standardwert) <p><p>- Zustandsloser Workflow: 5 Min <br>(Standardwert) | 366 Tage | Die Zeit, die ein Workflow weiter ausgeführt werden kann, bevor ein Timeout erzwungen wird. <p><p>Die Ausführungsdauer wird mithilfe der Startzeit einer Ausführung und des durch die Workfloweinstellung [**Aufbewahrung des Ausführungsverlaufs in Tagen**](#change-duration) zum Startzeitpunkt festgelegten Grenzwerts berechnet. <p>**Wichtig**: Stellen Sie sicher, dass der Wert für die Ausführungsdauer immer kleiner oder gleich der Aufbewahrungsdauer des Ausführungsverlaufs im Speicherwert ist. Andernfalls werden Ausführungsverläufe möglicherweise gelöscht, bevor die zugeordneten Aufträge abgeschlossen sind. <p><p>Weitere Informationen finden Sie unter [Ändern der Ausführungsdauer und der Verlaufsaufbewahrung im Speicher](#change-duration). |
| Wiederholungsintervall | - Min: 1 sek <p><p>- Max: 500 Tage | - Min: 1 sek <p><p>- Max: 500 Tage | - Min: 1 sek <p><p>- Max: 500 Tage ||
||||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Ändern von Ausführungsdauer und Verlaufsaufbewahrung im Speicher

Dieselbe Einstellung steuert im Designer die maximale Anzahl von Tagen, die ein Workflow ausgeführt werden kann, als auch für die Aufbewahrung des Ausführungsverlaufs im Speicher.

* Für den Dienst mit mehreren Mandanten ist der Standardgrenzwert von 90 Tagen gleich dem maximalen Grenzwert. Sie können diesen Wert nur verringern.

* Für den Einzelmandantendienst können Sie das Standardlimit von 90 Tagen verringern oder erhöhen. Weitere Informationen finden Sie unter [Bearbeiten von Host- und Anwendungseinstellungen für Logic Apps in Azure Logic Apps mit Einzelmandanten](edit-app-settings-host-settings.md).

* Für eine Integrationsdienstumgebung können Sie den Standardgrenzwert von 90 Tagen verringern oder erhöhen.

Nehmen wir beispielsweise an, dass Sie den Aufbewahrungsgrenzwert von 90 Tagen auf 30 Tage verringern möchten. Eine 60 Tage alte Ausführung wird dann aus dem Ausführungsverlauf entfernt. Wenn Sie die Aufbewahrungsdauer von 30 Tagen auf 60 Tage erhöhen, verbleibt eine 20 Tage alte Ausführung für weitere 40 Tage im Ausführungsverlauf.

> [!IMPORTANT]
> Wenn die Dauer einer Ausführung den aktuellen Aufbewahrungsgrenzwert für Ausführungsverläufe überschreitet, wird die Ausführung aus dem Ausführungsverlauf im Speicher entfernt. Um den Verlust des Ausführungsverlaufs zu vermeiden, stellen Sie sicher, dass die Aufbewahrungsdauer *immer* länger als die längste mögliche Dauer einer Ausführung ist.

Gehen Sie folgendermaßen vor, um den Standard- oder aktuellen Grenzwert für diese Eigenschaften zu ändern:

#### <a name="portal-multi-tenant-service"></a>[Portal (mehrinstanzenfähige Dienste)](#tab/azure-portal)

1. Suchen Sie Suchfeld des [Azure-Portals](https://portal.azure.com) nach **Logic Apps**, und wählen Sie es aus.

1. Suchen und öffnen Sie Ihre Logik-App im Logik-App-Designer.

1. Wählen Sie im Menü der Logik-App **Workfloweinstellungen** aus.

1. Wählen Sie unter **Laufzeitoptionen** in der Liste **Aufbewahrung des Ausführungsverlaufs in Tagen** die Option **Benutzerdefiniert** aus.

1. Ziehen Sie den Schieberegler bis zur gewünschten Anzahl von Tagen.

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste **Workfloweinstellungen** die Option **Speichern** aus.

#### <a name="resource-manager-template"></a>[Resource Manager: Vorlage](#tab/azure-resource-manager)

Wenn Sie eine Azure Resource Manager-Vorlage nutzen, wird diese Einstellung als Eigenschaft in der Ressourcendefinition Ihres Workflows angezeigt, die in der [Microsoft.Logic-Workflows-Vorlagenreferenz](/azure/templates/microsoft.logic/workflows) beschrieben ist:

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```
---

<a name="concurrency-looping-and-debatching-limits"></a>
<a name="looping-debatching-limits"></a>

## <a name="looping-concurrency-and-debatching-limits"></a>Schleifen, Parallelität und Auflösen von Batches

In der folgenden Tabelle sind die Werte für eine einzelne Workflow-Ausführung aufgeführt:

### <a name="loop-actions"></a>Schleifenaktionen

<a name="for-each-loop"></a>

#### <a name="for-each-loop"></a>Für jede Schleife

In der folgenden Tabelle werden die Werte einer **For each**-Schleife aufgelistet:

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| Array-Elemente | 100 000 Elemente | - Zustandsvoller Workflow: 100 000 Elemente <br>(Standardwert) <p><p>- Zustandsloser Workflow: 100 Elemente <br>(Standardwert) | 100 000 Elemente | Die Anzahl der Arrayelemente, die eine **For each**-Schleife verarbeiten kann. <p><p>Sie können die [Abfrageaktion](logic-apps-perform-data-operations.md#filter-array-action) verwenden, um größere Arrays zu filtern. <p><p>Informationen zum Ändern des Standardgrenzwerts in dem Dienst für einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Gleichzeitige Iterationen | Parallelität aus: 20 <p><p>Parallelität an: <p>- Standardwert: 20 <br>- Min: 1 <br>- Max: 50 | Parallelität aus: 20 <br>(Standardwert) <p><p>Parallelität an: <p><p>- Standardwert: 20 <br>- Min: 1 <br>- Max: 50 | Parallelität aus: 20 <p><p>Parallelität an: <p>- Standardwert: 20 <br>- Min: 1 <br>- Max: 50 | Dieser Grenzwert entspricht der maximalen Anzahl von **For each**-Schleifeniterationen, die gleichzeitig bzw. parallel ausgeführt werden können. <p><p>Informationen zum Ändern dieses Werts im mehr mandantenübergreifenden Dienst finden Sie unter [Ändern **des** For each-Parallelitätslimits](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) oder [Sequenzielles Ausführen der **For each**-Schleifen](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). <p><p>Informationen zum Ändern des Standardgrenzwerts in dem Dienst für einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
||||||

<a name="until-loop"></a>

#### <a name="until-loop"></a>Until-Schleife

In der folgenden Tabelle werden die Werte für die **Until**-Schleife aufgelistet:

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| Iterationen | - Standardwert: 60 <br>- Min: 1 <br>- Max: 5.000 | Zustandsvoller Workflow: <p><p>- Standardwert: 60 <br>- Min: 1 <br>- Max: 5.000 <p><p>\- Zustandsloser Workflow: <p><p>- Standardwert: 60 <br>- Min: 1 <br>- Max: 100 | - Standardwert: 60 <br>- Min: 1 <br>- Max: 5.000 | Die Anzahl von Zyklen, die eine **Until**-Schleife während einer Workflowausführung aufweisen kann. <p><p>Um diesen Wert in dem mehrinstanzenfähigen Dienst zu ändern, wählen Sie in der **Bis**-Schleifenform die Option **Grenzwerte ändern** aus und geben einen Wert für die **Anzahl**-Eigenschaft an. <p><p>Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Timeout | Standardwert: PT1H (1 Stunde) | Zustandsbehafteter Workflow: PT1H (1 Stunde) <p><p>Zustandsloser Workflow: PT5M (5 Min.) | Standardwert: PT1H (1 Stunde) | Die maximale Zeitspanne, die die **Until**-Schleife vor dem Beenden ausgeführt wird. Die Angabe erfolgt im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601). Der Timeoutwert wird für jeden Schleifendurchlauf ausgewertet. Wenn eine Aktion in der Schleife länger als die Zeitüberschreitung dauert, wird der aktuelle Zyklus nicht beendet. Der nächste Zyklus beginnt jedoch nicht, weil die Grenzwertbedingung nicht erfüllt ist. <p><p>Um diesen Wert in dem mehrinstanzenfähigen Dienst zu ändern, wählen Sie in der **Bis**-Schleifenform die Option **Grenzwerte ändern** aus und geben einen Wert für die **Zeitüberschreitung**-Eigenschaft an. <p><p>Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
||||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>Parallelität und Auflösen von Batches

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| Trigger: gleichzeitige Ausführungen | Parallelität aus: Unbegrenzt <p><p>Parallelität ein (nicht rückgängig zu machen): <p><p>- Standardwert: 25 <br>- Min: 1 <br>- Max: 100 | Parallelität aus: Unbegrenzt <p><p>Parallelität ein (nicht rückgängig zu machen): <p><p>– Standardwert: 100 <br>- Min: 1 <br>- Max: 100 | Parallelität aus: Unbegrenzt <p><p>Parallelität ein (nicht rückgängig zu machen): <p><p>- Standardwert: 25 <br>- Min: 1 <br>- Max: 100 | Die Anzahl gleichzeitiger Ausführungen, die ein Trigger gleichzeitig oder parallel starten kann. <p><p>**Hinweis**: Wenn Parallelität aktiviert ist, wird das **SplitOn**-Limit auf 100 Elemente für das [Auflösen von Arraybatches](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) reduziert. <p><p>Informationen zum Ändern dieses Werts im mehr mandantenübergreifenden Dienst finden Sie unter [Ändern des Trigger-Parallelitätslimits](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) oder [Sequentielles Auslösen von Trigger-Instanzen](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). <p><p>Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Maximale Anzahl von wartenden Ausführungen | Parallelität aus: <p><p>- Min = 1 Ausführung <p>- Max: 50 Ausführungen <p><p>Parallelität an: <p><p>- Min: 10 Ausführungen plus die Anzahl gleichzeitiger Ausführungen <p>- Max: 100 Ausführungen | Parallelität aus: <p><p>- Min = 1 Ausführung <br>(Standardwert) <p>- Max: 50 Ausführungen <br>(Standardwert) <p><p>Parallelität an: <p><p>- Min: 10 Ausführungen plus die Anzahl gleichzeitiger Ausführungen <p>– Max: 200 Ausführungen <br>(Standardwert) | Parallelität aus: <p><p>- Min = 1 Ausführung <p>- Max: 50 Ausführungen <p><p>Parallelität an: <p><p>- Min: 10 Ausführungen plus die Anzahl gleichzeitiger Ausführungen <p>- Max: 100 Ausführungen | Die Anzahl von Workflowinstanzen, die auf die Ausführung warten können, wenn für Ihre akktuelle Workflowinstanz bereits die maximale Anzahl von gleichzeitigen Instanzen ausgeführt wird. <p><p>Informationen zum Ändern dieses Werts im mehrinstanzenfähigen Dienst finden Sie unter Ändern des [Grenzwerts für wartende Ausführungen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). <p><p>Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| **SplitOn**-Elemente | Parallelität aus: 100 000 Elemente <p><p>Parallelität ein: 100 Elemente | Parallelität aus: 100 000 Elemente <p><p>Parallelität ein: 100 Elemente | Parallelität aus: 100 000 Elemente <br>(Standardwert) <p><p>Parallelität ein: 100 Elemente <br>(Standardwert) | Für Trigger, die ein Array zurückgeben, können Sie einen Ausdruck angeben, der eine **SplitOn**-Eigenschaft verwendet, um [Arrayelemente für die Verarbeitung in mehrere Workflowinstanzen aufzuteilen bzw. aufzulösen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch), anstatt eine **For each**-Schleife zu verwenden. Dieser Ausdruck verweist auf das Array, das zum Erstellen und Ausführen einer Workflowinstanz für jedes Arrayelement verwendet werden soll. <p><p>**Hinweis**: Wenn Parallelität aktiviert ist, wird das **SplitOn**-Limit auf 100 Elemente reduziert. |
||||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Durchsatzlimits

In der folgenden Tabelle sind die Werte für eine einzelne Workflowdefinition aufgeführt:

| Name | Mehrinstanzenfähig | Einzelmandant | Notizen |
|------|--------------|---------------|-------|
| Aktion: Ausführung alle 5 Minuten | Standard: 100.000 Ausführungen <br>– Modus für hohen Durchsatz: 300 000 Ausführungen | Keine | In dem mehrinstanzenfähigen Dienst können Sie den Standardwert für Ihren Workflow auf den Höchstwert erhöhen. Weitere Informationen finden Sie unter Ausführen im [Modus mit hohem Durchsatz](#run-high-throughput-mode). Dieser Modus ist ein Vorschau-Feature. Sie können bei Bedarf auch die [Workload auf mehrere Workloads verteilen](handle-throttling-problems-429-errors.md#logic-app-throttling). |
| Aktion: Gleichzeitig ausgehende Aufrufe | ~2 500 Aufrufe | Keine | Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
| Verwaltete Connectordrosselung | Das Drosselungslimit variiert je nach Connector | Das Drosselungslimit variiert je nach Connector | Informationen zu mehrmandantenbasierten Connectors finden Sie auf der technischen [Referenzseite für jeden verwalteten Connector](/connectors/connector-reference/connector-reference-logicapps-connectors). <p><p>Weitere Informationen zur Behandlung der Connectordrosselung finden Sie unter [Behandeln von Drosselungsproblemen („Fehler 429 – Zu viele Anforderungen“)](handle-throttling-problems-429-errors.md#connector-throttling). |
| Endpunkt zur Laufzeit: Gleichzeitig eingehende Aufrufe | ~1 000 Aufrufe | Keine | Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
| Endpunkt zur Laufzeit: Read-Aufrufe pro 5 Min  | 60 000 Read-Aufrufe | Keine | Dieser Grenzwert gilt für Aufrufe, die die unformatierten Eingaben und Ausgaben aus dem Ausführungsverlauf eines Workflows erhalten. Sie können bei Bedarf die Workload auf mehrere Workloads verteilen. |
| Endpunkt zur Laufzeit: Invoke-Aufrufe pro 5 Min | 45 000 Invoke-Aufrufe | Keine | Sie können bei Bedarf Workload auf mehrere Workloads verteilen. |
| Inhaltsdurchsatz pro 5 Min | 600 MB | Keine | Sie können bei Bedarf Workload auf mehrere Workloads verteilen. |
|||||

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Ausführen im Modus mit hohem Durchsatz

Für eine einzelne Workflowdefinition gilt für die Anzahl von Aktionen, die alle fünf Minuten ausgeführt werden, ein [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Um den Wert auf den [maximalen Wert](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) (das Dreifache des Standardwerts) für Ihren Workflow zu erhöhen, können Sie den Modus mit hohem Durchsatz (Vorschauversion) aktivieren. Sie können bei Bedarf auch die [Workload auf mehrere Workloads verteilen](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling).

#### <a name="portal-multi-tenant-service"></a>[Portal (mehrinstanzenfähige Dienste)](#tab/azure-portal)

1. Wählen Sie im Azure-Portal im Menü Ihrer Logik-App unter **Einstellungen** die Option **Workfloweinstellungen** aus.

1. Ändern Sie die Einstellung unter **Laufzeitoptionen** > **Hoher Durchsatz** in **Ein**.

   ![Screenshot des Logik-App-Menüs im Azure-Portal mit den „Workfloweinstellungen“ und dem Wert „Ein“ für die Einstellung „Hoher Durchsatz“](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

#### <a name="resource-manager-template"></a>[Resource Manager-Vorlage](#tab/azure-resource-manager)

Um diese Einstellung in einer ARM-Vorlage für die Bereitstellung Ihrer Logik-App zu aktivieren, fügen Sie im `properties`-Objekt für die Ressourcendefinition Ihrer Logik-App das `runtimeConfiguration`-Objekt hinzu, dessen `operationOptions`-Eigenschaft auf `OptimizedForHighThroughput` festgelegt ist:

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Weitere Informationen zur Definition Ihrer Logik-App-Ressource finden Sie unter [Übersicht über das Automatisieren der Bereitstellung für Azure Logic Apps mithilfe von Azure Resource Manager-Vorlagen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

---

### <a name="integration-service-environment-ise"></a>Integrationsdienstumgebung (Integration Service Environment, ISE)

* [Developer ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): Bietet bis zu 500 Ausführungen pro Minute, aber beachten Sie diese Aspekte:

  * Stellen Sie sicher, dass Sie diese SKU nur für Erkundung, Experimente, Entwicklung oder Tests verwenden, nicht jedoch für die Produktion oder für Leistungstests. Diese SKU umfasst keine Vereinbarung zum Servicelevel (Service-Level Agreement, SLA), Funktion zum zentralen Hochskalieren der Kapazität oder Redundanz während der Wiederverwendung. Dies bedeutet, dass Verzögerungen oder Ausfallzeiten auftreten können.

  * Der Dienst kann vorübergehend von Back-End-Updates unterbrochen werden.

* [Premium ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): In der folgenden Tabelle werden die Grenzwerte für den Durchsatz dieser SKU beschrieben. Wenn diese Grenzwerte jedoch bei der normalen Verarbeitung überschritten oder Auslastungstests ausgeführt werden sollen, bei denen diese Grenzwerte möglicherweise überschritten werden, [bitten Sie das Logic Apps-Team](mailto://logicappsemail@microsoft.com) um Unterstützung im Hinblick auf Ihre Anforderungen.

  | Name | Begrenzung | Notizen |
  |------|-------|-------|
  | Ausführungsgrenzwert für eine Basiseinheit | System gedrosselt, wenn die Infrastrukturkapazität 80 % erreicht | Bietet ca. 4.000 Aktionsausführungen pro Minute, was rund 160 Mio. Aktionsausführungen pro Monat entspricht. |
  | Ausführungsgrenzwert für eine Skalierungseinheit | System gedrosselt, wenn die Infrastrukturkapazität 80 % erreicht | Jede Skalierungseinheit kann ca. 2.000 weitere Aktionsausführungen pro Minute bereitstellen, was etwa 80 Mio. zusätzlichen Aktionsausführungen pro Monat entspricht |
  | Maximale Skalierungseinheiten, die Sie hinzufügen können | 10 Skalierungseinheiten | |
  ||||

<a name="gateway-limits"></a>

## <a name="data-gateway-limits"></a>Grenzwerte für Datengateway

Azure Logic Apps unterstützt Schreibvorgänge, einschließlich Einfügungen und Aktualisierungen, über das lokale Datengateway. Allerdings besitzen diese Vorgänge [Limits hinsichtlich Ihrer Nutzlastgröße](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="variables-action-limits"></a>

## <a name="variables-action-limits"></a>Aktionslimits für Variablen

In der folgenden Tabelle sind die Werte für eine einzelne Workflowdefinition aufgeführt:

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| Variablen pro Workflow | 250 Variablen | 250 Variablen <br>(Standardwert) | 250 Variablen ||
| Variable: Maximale Inhaltsgröße | 104 857 600 Zeichen | Zustandsvoller Workflow: 104 857 600 Zeichen <br>(Standardwert) <p><p>Zustandsloser Workflow: 1 024 Zeichen <br>(Standardwert) | 104 857 600 Zeichen | Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Variable (Arraytyp): Maximale Anzahl von Arrayelementen | 100 000 Elemente | 100 000 Elemente <br>(Standardwert) | Premium-SKU: 100 000 Elemente <p><p>Developer-SKU: 5 000 Elemente | Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
||||||

<a name="http-limits"></a>

## <a name="http-request-limits"></a>Grenzwerte für HTTP-Anforderungen

In den folgenden Tabellen sind die Werte für einen einzelnen eingehenden oder ausgehenden Aufruf aufgeführt:

<a name="http-timeout-limits"></a>

### <a name="timeout-duration"></a>Timeoutdauer

Standardmäßig folgen die HTTP-Aktion und die APIConnection-Aktionen dem standardmäßigen [asynchronen Vorgangsmuster](/azure/architecture/patterns/async-request-reply), während die Response-Aktion (Antwort) dem *synchronen Vorgangsmuster* folgt. Einige verwaltete Connectorvorgänge führen asynchrone Aufrufe aus oder lauschen auf Webhookanforderungen, sodass das Timeout für diese Vorgänge länger sein kann, als die folgenden Grenzwerte angeben. Weitere Informationen finden Sie auf [der technischen Referenzseite jedes Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) sowie in der Dokumentation zu [Workflowtriggern und -aktionen](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

> [!NOTE]
> Für den Ressourcentyp **Logik-App (Standard)** im Dienst für Einzelne Mandanten können zustandslose Workflows nur *synchron* ausgeführt werden.

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| Ausgehende Anforderung | 120 Sekunden <br>(2 min) | 235 Sekunden <br>(3,9 min) <br>(Standardwert) | 240 Sekunden <br>(4 min) | Ausgehende Anforderungen sind beispielsweise Aufrufe durch einen HTTP-Trigger bzw. eine HTTP-Aktion. <p><p>**Tipp**: Verwenden Sie für Vorgänge, die länger ausgeführt werden, ein [asynchrones Abrufmuster](../logic-apps/logic-apps-create-api-app.md#async-pattern) oder eine [„Until“-Schleife](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). Um Timeoutlimits zu umgehen, wenn Sie einen anderen Workflow aufrufen, der einen [aufrufbaren Endpunkt](logic-apps-http-endpoint.md) besitzt, können Sie stattdessen die integrierte Azure Logic Apps-Aktion verwenden, die Sie in der Auswahl für den Designervorgang unter **Integriert** finden. <p><p>Informationen zum Ändern des Standardgrenzwerts in dem Dienst für einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Eingehende Anforderungen | 120 Sekunden <br>(2 min) | 235 Sekunden <br>(3,9 min) <br>(Standardwert) | 240 Sekunden <br>(4 min) | Eingehende Anforderungen sind beispielsweise Aufrufe, die von einem Anforderungstrigger, einem HTTP-Webhooktrigger oder einer HTTP-Webhookaktion empfangen werden. <p><p>**Hinweis**: Damit der ursprüngliche Aufrufer die Antwort erhält, müssen alle Schritte in der Antwort innerhalb des Grenzwerts abgeschlossen werden, es sei denn, Sie rufen einen anderen geschachtelten Workflow auf. Weitere Informationen hierzu finden Sie unter [Aufrufen, Auslösen oder Schachteln von Logik-Apps](../logic-apps/logic-apps-http-endpoint.md). <p><p>Informationen zum Ändern des Standardgrenzwerts in dem Dienst für einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
||||||

<a name="message-size-limits"></a>

### <a name="messages"></a>Nachrichten

| Name | Blockierung aktiviert | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|------------------|--------------|-------------------------|---------------------------------|-------|
| Inhaltsdownload: Maximale Anzahl von Anforderungen | Ja | 1 000 Anforderungen | 1 000 Anforderungen <br>(Standardwert) | 1 000 Anforderungen ||
| Nachrichtengröße | Nein | 100 MB | 100 MB | 200 MB | Informationen, wie Sie diese Beschränkung umgehen können, finden Sie unter [Verarbeiten von großen Nachrichten durch Blockerstellung in Logic Apps](../logic-apps/logic-apps-handle-large-messages.md). Einige Connectors und APIs unterstützen die Blockerstellung (Segmentierung) oder sogar den Standardgrenzwert allerdings nicht. <p><p>- Connectors wie AS2, X12 und EDIFACT verfügen über eigene [B2B-Nachrichtenlimits](#b2b-protocol-limits). <p>- ISE-Connectors verwenden den ISE-Grenzwert, nicht die Grenzwerte für Nicht-ISE-Connectors. <p><p>Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Nachrichtengröße | Ja | 1 GB | 1 073 741 824 Bytes <br>(1 GB) <br>(Standardwert) | 5 GB | Dieser Grenzwert gilt für Aktionen, die die Blockerstellung automatisch unterstützen, oder für die Sie die Blockerstellung in der Laufzeitkonfiguration aktivieren können. <p><p>Wenn Sie eine ISE verwenden, unterstützt die Azure Logic Apps-Engine diesen Grenzwert. Connectors verfügen jedoch über eigene Blockerstellungsgrenzwerte bis zum Grenzwert der Engine. Beachten Sie hierzu z. B. die Informationen in der [Referenz zur API des Azure Blob Storage-Connectors](/connectors/azureblob/). Weitere Informationen zur Blockerstellung finden Sie unter [Verarbeiten von großen Nachrichten durch Blockerstellung](../logic-apps/logic-apps-handle-large-messages.md). <p><p>Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Größe des Inhaltsblocks | Ja | Variiert je nach Connector | 52 428 800 Bytes (52 MB) <br>(Standardwert) | Variiert je nach Connector | Dieser Grenzwert gilt für Aktionen, die die Blockerstellung automatisch unterstützen, oder für die Sie die Blockerstellung in der Laufzeitkonfiguration aktivieren können. <p><p>Informationen zum Ändern des Standardwerts in dem Dienst für Einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
|||||||

### <a name="character-limits"></a>Zeichengrenzwerte

| Name | Begrenzung | Notizen |
|------|-------|-------|
| Grenzwert für die Auswertung von Ausdrücken | 131.072 Zeichen | Keiner der Ausdrücke `@concat()`, `@base64()` und `@string()` darf länger sein, als dieser Grenzwert angibt. |
| Zeichengrenzwert für Anforderungs-URL | 16.384 Zeichen | |
||||

<a name="retry-policy-limits"></a>

### <a name="retry-policy"></a>Wiederholungsrichtlinie

| Name | Grenzwert bei mehreren Mandanten | Grenzwerte für einzelne Mandanten | Notizen |
|------|--------------------|---------------------|-------|
| Wiederholungsversuche | - Standard: 4 Versuche <br> - Max: 90 Versuche | - Standard: 4 Versuche | Verwenden Sie den [Wiederholungsrichtlinienparameter](logic-apps-exception-handling.md#retry-policies), um das Standardlimit in dem mehrinstanzenfähigen Dienst zu ändern. <p><p>Informationen zum Ändern des Standardgrenzwerts in dem Dienst für einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Wiederholungsintervall | Keine | Standardwert: 7 Sekunden | Verwenden Sie den [Wiederholungsrichtlinienparameter](logic-apps-exception-handling.md#retry-policies), um das Standardlimit in dem mehrinstanzenfähigen Dienst zu ändern. <p><p>Informationen zum Ändern des Standardgrenzwerts in dem Dienst für einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Maximale Wiederholungsverzögerung | Standardwert: 1 Tag | Standardwert: 1 Stunde | Verwenden Sie den [Wiederholungsrichtlinienparameter](logic-apps-exception-handling.md#retry-policies), um das Standardlimit in dem mehrinstanzenfähigen Dienst zu ändern. <p><p>Informationen zum Ändern des Standardgrenzwerts in dem Dienst für einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
| Minimale Wiederholungsverzögerung | Standardwert: 5 Sekunden | Standardwert: 5 Sekunden | Verwenden Sie den [Wiederholungsrichtlinienparameter](logic-apps-exception-handling.md#retry-policies), um das Standardlimit in dem mehrinstanzenfähigen Dienst zu ändern. <p><p>Informationen zum Ändern des Standardgrenzwerts in dem Dienst für einzelne Mandanten, finden Sie unter [Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](edit-app-settings-host-settings.md). |
|||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Authentifizierungslimits

Die folgende Tabelle zeigt die Werte für einen Workflow, der mit einem Anforderungstrigger startet und [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) für die Autorisierung eingehender Aufrufe an den Anforderungstrigger aktiviert:

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Azure AD-Autorisierungsrichtlinien | 5 Richtlinien | |
| Ansprüche pro Autorisierungsrichtlinie | 10 Ansprüche | |
| Anspruchswert: maximale Zeichenanzahl | 150 Zeichen |
||||

<a name="switch-action-limits"></a>

## <a name="switch-action-limits"></a>Umschalten von Aktionslimits

In der folgenden Tabelle sind die Werte für eine einzelne Workflowdefinition aufgeführt:

| Name | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Maximale Anzahl von Fällen pro Aktion | 25 ||
||||

<a name="inline-code-action-limits"></a>

## <a name="inline-code-action-limits"></a>Aktionslimits für Inlinecode

In der folgenden Tabelle sind die Werte für eine einzelne Workflowdefinition aufgeführt:

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| Maximale Anzahl von Codezeichen | 1\.024 Zeichen | 100 000 Zeichen | 1\.024 Zeichen | Um den höheren Grenzwert zu verwenden, erstellen Sie eine **Logik-App-Ressource (Standard)** , die in einem einzelnen Azure Logic Apps-Mandanten ausgeführt wird, entweder mithilfe des [Azure-Portals](create-single-tenant-workflows-azure-portal.md) oder [mithilfe von Visual Studio Code und der **Erweiterung „Azure Logic Apps (Standard)“**](create-single-tenant-workflows-visual-studio-code.md). |
| Maximale Dauer für die Codeausführung | 5 Sekunden | 15 Sekunden | 1\.024 Zeichen | Um den höheren Grenzwert zu verwenden, erstellen Sie eine **Logik-App-Ressource (Standard)** , die in einem einzelnen Azure Logic Apps-Mandanten ausgeführt wird, entweder mithilfe des [Azure-Portals](create-single-tenant-workflows-azure-portal.md) oder [mithilfe von Visual Studio Code und der **Erweiterung „Azure Logic Apps (Standard)“**](create-single-tenant-workflows-visual-studio-code.md). |
||||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Grenzwerte für einen benutzerdefinierten Connector

Nur in der mandantenfähigen Azure Logic Apps- und Integrationsdienstumgebung können Sie [benutzerdefinierte verwaltete Konnektoren](/connectors/custom-connectors) erstellen und verwenden, die eine vorhandene REST-API oder SOAP-API umhüllen. In Azure Logic Apps mit Einzelmandanten können Sie nur [benutzerdefinierte integrierte Konnektoren](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) erstellen und verwenden.

In der folgenden Tabelle werden die Werte für jeden benutzerdefinierten Connector aufgelistet:

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| Benutzerdefinierte Connectors | 1\.000 pro Azure-Abonnement | Unbegrenzt | 1\.000 pro Azure-Abonnement ||
| Benutzerdefinierte Anschlüsse - Anzahl der APIs | SOAP-basiert: 50 | Nicht zutreffend | SOAP-basiert: 50 ||
| Anforderungen pro Minute für einen benutzerdefinierten Connector | 500 Anforderungen pro Minute und Verbindung | Basierend auf Ihrer Implementierung | 2\.000 Anforderungen pro Minute und *benutzerdefiniertem Connector* ||
| Verbindungstimeout | 2 Min. | Verbindung im Leerlauf: <br>4 min <p><p>Aktive Verbindung: <br>10 Min. | 2 Min. ||
||||||

Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Übersicht über verwaltete benutzerdefinierte Connectors](/connectors/custom-connectors)
* [Aktivieren der integrierten Connectorerstellung – Visual Studio Code mit Azure Logic Apps-Erweiterung (Standard)](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)

<a name="managed-identity"></a>

## <a name="managed-identity-limits"></a>Grenzwerte für verwaltete Identitäten

| Name | Begrenzung |
|------|-------|
| Verwaltete Identitäten pro Logik-App | Entweder die vom System zugewiesene Identität oder eine benutzerseitig zugewiesene Identität |
| Anzahl von Logik-Apps mit einer verwalteten Identität in einem Azure-Abonnement pro Region | 1\.000 |
|||

> [!NOTE] 
> Standardmäßig wird die systemseitig zugewiesene verwaltete Identität einer Logik-App-Ressource (Standard) automatisch aktiviert, um Verbindungen zur Laufzeit zu authentifizieren. Diese Identität unterscheidet sich von den Anmeldeinformationen für die Authentifizierung oder der Verbindungszeichenfolge, die Sie verwenden, wenn Sie eine Verbindung herstellen. Wenn Sie diese Identität deaktivieren, funktionieren Verbindungen zur Laufzeit nicht. Wählen Sie zum Anzeigen dieser Einstellung im Menü Ihrer Logik-App unter **Einstellungen** die Option **Identität** aus.

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Grenzwerte für Integrationskonten

Für jedes Azure-Abonnement gelten folgende Grenzwerte für das Integrationskonto:

* Ein Integrationskonto im [Tarif „Free“](../logic-apps/logic-apps-pricing.md#integration-accounts) pro Azure-Region. Diese Dienstebene ist nur für öffentliche Regionen in Azure verfügbar, z. B. „USA, Westen“ oder „Asien, Südosten“, aber nicht für [Azure China 21ViaNet](/azure/china/overview-operations) oder [Azure Government](../azure-government/documentation-government-welcome.md).

* 1\.000 Integrationskonten insgesamt, einschließlich Integrationskonten in beliebigen [Integrationsdienstumgebungen (Integration Service Environments, ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) in [Developer- und Premium-SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Jede ISE, ob [Developer oder Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), kann ein einzelnes Integrationskonto ohne zusätzliche Kosten verwenden, obwohl der enthaltene Kontotyp von der ISE SKU abweicht. Sie können für Ihre ISE bis zur Gesamtgrenze für [Extrakosten](logic-apps-pricing.md#ise-pricing) weitere Integrationskonten erstellen.

  | ISE SKU | Grenzwerte für Integrationskonten |
  |---------|----------------------------|
  | **Premium** | 20 Konten insgesamt, einschließlich eines Standardkontos ohne Extrakosten. Mit dieser SKU können Sie nur [Standardkonten](../logic-apps/logic-apps-pricing.md#integration-accounts) haben. Es sind keine Free- oder Basic-Konten zulässig. |
  | **Developer** | 20 Konten gesamt, einschließlich eines [kostenlosen](../logic-apps/logic-apps-pricing.md#integration-accounts) Kontos (auf 1 beschränkt). Mit dieser SKU sind beide Kombinationen möglich: <p>– Ein kostenloses Konto und bis zu 19 [Standardkonten](../logic-apps/logic-apps-pricing.md#integration-accounts). <br>– Kein kostenloses Konto und bis zu 20 Standardkonten. <p>Es sind keine Basic- oder weiteren kostenlosen Konten zulässig. <p><p>**Wichtig**: Verwenden Sie die [Developer-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) für Experimente, Entwicklung und Tests, jedoch nicht für die Produktion oder Leistungstests. |
  |||

Weitere Informationen zur Preisgestaltung und Abrechnung für ISEs finden Sie unter [Feststehendes Preismodell](../logic-apps/logic-apps-pricing.md#ise-pricing). Eine Preisübersicht finden Sie unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefaktgrenzwerte pro Integrationskonto

In den folgenden Tabellen sind die Werte für die Anzahl von Artefakten aufgeführt, die auf die einzelnen Integrationskontoebenen beschränkt sind. Eine Preisübersicht finden Sie unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/). Informationen zur Preisgestaltung und Abrechnung für Integrationskonten finden Sie unter [Integrationskonten](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Verwenden Sie den Free-Tarif nur für Versuchsszenarios und nicht für Produktionsszenarios. Dieser Tarif beschränkt Durchsatz und Nutzung und ist nicht mit einer Vereinbarung zum Servicelevel (Service-Level Agreement, SLA) verbunden.

| Artefakt | Kostenlos | Basic | Standard |
|----------|------|-------|----------|
| EDI-Handelsverträge | 10 | 1 | 1\.000 |
| EDI-Handelspartner | 25 | 2 | 1\.000 |
| Karten | 25 | 500 | 1\.000 |
| Schemas | 25 | 500 | 1\.000 |
| Assemblys | 10 | 25 | 1\.000 |
| Zertifikate | 25 | 2 | 1\.000 |
| Batchkonfigurationen | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Artefaktkapazitätsgrenzen

| Artefakt | Begrenzung | Notizen |
| -------- | ----- | ----- |
| Assembly | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB ein [Azure Storage-Konto und einen Blobcontainer](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Zuordnung (XSLT-Datei) | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB die [Zuordnungen der REST-API für Azure Logic Apps](/rest/api/logic/maps/createorupdate). <p><p>**Hinweis**: Die Menge der Daten oder Datensätze, die eine Zuordnung erfolgreich verarbeiten kann, basiert auf den Grenzwerten für Nachrichtengröße und Aktionstimeout in Azure Logic Apps. Wenn Sie z. B. eine HTTP-Aktion verwenden, die auf [HTTP-Nachrichtengröße und -Timeoutlimits](#http-limits) basiert, kann eine Zuordnung Daten bis zum HTTP-Nachrichtengrößenlimit verarbeiten, wenn der Vorgang innerhalb des HTTP-Timeoutlimits abgeschlossen wird. |
| Schema | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB ein [Azure Storage-Konto und einen Blobcontainer](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Durchsatzlimits

| Endpunkt zur Laufzeit | Kostenlos | Basic | Standard | Notizen |
|------------------|------|-------|----------|-------|
| Read-Aufrufe pro 5 Min | 3,000 | 30.000 | 60.000 | Dieser Grenzwert gilt für Aufrufe, die die unformatierten Eingaben und Ausgaben aus dem Ausführungsverlauf einer Logik-App erhalten. Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Invoke-Aufrufe pro 5 Min | 3,000 | 30.000 | 45.000 | Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Nachverfolgungsaufrufe pro 5 Min | 3,000 | 30.000 | 45.000 | Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Gleichzeitige Blockierungsaufrufe | ca. 1.000 | ca. 1.000 | ca. 1.000 | Identisch für alle SKUs. Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Nachrichtengröße für B2B-Protokoll (AS2, X12, EDIFACT)

In der folgenden Tabelle sind die Grenzwerte für die Nachrichtengröße aufgeführt, die für B2B-Protokolle gelten:

| Name | Mehrinstanzenfähig | Einzelmandant | Integrationsdienstumgebung | Notizen |
|------|--------------|---------------|---------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1 – 25 MB | Nicht verfügbar | v2 – 200 MB <br>v1 – 25 MB | Gilt für das Decodieren und das Codieren |
| X12 | 50 MB | Nicht verfügbar | 50 MB | Gilt für das Decodieren und das Codieren |
| EDIFACT | 50 MB | Nicht verfügbar | 50 MB | Gilt für das Decodieren und das Codieren |
||||

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Firewallkonfiguration: IP-Adressen und Diensttags

Wenn Ihre Umgebung strenge Netzwerkanforderungen oder Firewalls besitzt, die den Datenverkehr auf bestimmte IP-Adressen beschränken, muss Ihre Umgebung oder Firewall den Zugriff *sowohl* für die [eingehenden](#inbound) als auch für die [ausgehenden](#outbound) IP-Adressen zulassen, die vom Azure Logic Apps-Dienst oder der Runtime in der Azure-Region verwendet werden, in der Ihre Logic App-Ressource existiert. Zum Einrichten dieses Zugriffs können Sie [Azure-Firewallregeln](../firewall/rule-processing.md) erstellen. Für *alle* Logik-Apps in derselben Region werden dieselben IP-Adressbereiche verwendet.

> [!NOTE]
> Wenn Sie [Power Automate](/power-automate/getting-started) verwenden, werden einige Aktionen wie **HTTP** und **HTTP + OpenAPI** direkt über den Azure Logic Apps-Dienst geleitet und stammen von den hier aufgeführten IP-Adressen. Weitere Informationen zu den von Power Automate verwendeten IP-Adressen finden Sie unter [Grenzwerte und Konfiguration für Power Automate](/power-automate/limits-and-config#ip-address-configuration).

Angenommen, Ihre Logik-Apps werden in der Region „USA, Westen“ bereitgestellt. Ihre Firewall muss den Zugriff für *alle* eingehenden IP-Adressen des Azure Logic Apps-Diensts *und* alle ausgehenden IP-Adressen, die in der Region „USA, Westen“ vorhanden sind, zulassen, damit alle Aufrufe unterstützt werden, die Ihre Logik-Apps über integrierte Trigger und Aktionen senden bzw. empfangen, wie etwa [HTTP-Trigger oder -Aktionen](../connectors/connectors-native-http.md).

Falls für Ihren Worfklow [verwaltete Connectors](../connectors/managed.md) (Office 365 Outlook-Connector oder SQL-Connector) oder [benutzerdefinierte Connectors](/connectors/custom-connectors/) verwendet werden, muss die Firewall in der Azure-Region Ihrer Logik-App zusätzlich den Zugriff für *alle* [ausgehenden IP-Adressen von verwalteten Connectors](/connectors/common/outbound-ip-addresses) zulassen. Wenn Ihr Workflow benutzerdefinierte Connectors verwendet, die über die [lokale Datengatewayressource in Azure](logic-apps-gateway-connection.md) auf lokale Ressourcen zugreifen, müssen Sie die Gatewayinstallation so einrichten, dass der Zugriff auf die [ausgehenden IP-Adressen des entsprechenden *verwalteten Connectors*](/connectors/common/outbound-ip-addresses) zugelassen wird. Weitere Informationen zum Einrichten von Kommunikationseinstellungen auf dem Gateway finden Sie in den folgenden Themen:

* [Anpassen von Kommunikationseinstellungen für das lokale Datengateway](/data-integration/gateway/service-gateway-communication)
* [Konfigurieren von Proxyeinstellungen für das lokale Datengateway](/data-integration/gateway/service-gateway-proxy)

> [!IMPORTANT]
> Wenn Sie [von 21Vianet betriebenes Microsoft Azure](/azure/china/) verwenden, verfügen verwaltete Connectors und benutzerdefinierte Connectors nicht über reservierte oder feste IP-Adressen. Daher können Sie keine Firewallregeln für Logik-Apps einrichten, die diese Connectors in dieser Cloud verwenden. Informationen zu den Dienst-IPs von Azure Logic Apps finden Sie in der [Dokumentationsversion für Azure, das von 21Vianet betrieben wird](https://docs.azure.cn/en-us/logic-apps/logic-apps-limits-and-config#firewall-ip-configuration).

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Überlegungen zur Firewall-IP-Konfiguration

Bevor Sie Ihre Firewall mit IP-Adressen einrichten, überprüfen Sie die folgenden Aspekte:

* Wenn Ihre Logik-App-Workflows in Azure Logic Apps mit Einzelmandanten ausgeführt werden, müssen Sie die vollqualifizierten Domänenname (FQDNs) für Ihre Verbindungen ermitteln. Weitere Informationen finden Sie in den entsprechenden Abschnitten in folgenden Artikeln:

  * [Firewallberechtigungen für Logik-Apps für einzelinstanzenfähige Logik-Apps – Azure-Portal](create-single-tenant-workflows-azure-portal.md#firewall-setup)
  * [Firewallberechtigungen für Logik-Apps für einzelinstanzenfähige Logik-Apps – Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)

* Wenn Ihre Logik-App-Workflows in einer [Integrationsdienstumgebung (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) ausgeführt werden, stellen Sie sicher, dass Sie [diese Ports ebenfalls öffnen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Um Sicherheitsregeln, die Sie erstellen möchten, zu vereinfachen, können Sie optional stattdessen [Diensttags](../virtual-network/service-tags-overview.md) verwenden, statt für jede Region IP-Adresspräfixe anzugeben. Diese Tags funktionieren in den Regionen, in denen der Logic Apps-Dienst verfügbar ist:

  * **LogicAppsManagement**: Steht für die IP-Adresspräfixe des Logic Apps-Diensts in eingehender Richtung.

  * **LogicApps**: Steht für die IP-Adresspräfixe des Logic Apps-Diensts in ausgehender Richtung.

  * **AzureConnectors**: Stellt die IP-Adresspräfixe für verwaltete Connectors dar, die eingehende Webhook-Rückrufe an den Logic Apps-Dienst und ausgehende Aufrufe an die entsprechenden Dienste wie Azure Storage oder Azure Event Hubs senden.

* Wenn Ihre Logik-Apps Probleme beim Zugriff auf Azure-Speicherkonten haben, die [Firewalls und Firewallregeln](../storage/common/storage-network-security.md) verwenden, stehen Ihnen [verschiedene weitere Optionen zum Ermöglichen des Zugriffs](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls) zur Verfügung.

  Beispielsweise können Logik-Apps nicht direkt auf Speicherkonten zugreifen, für die Firewallregeln gelten und die sich in derselben Region befinden. Wenn Sie jedoch die [ausgehenden IP-Adressen für verwaltete Connectors in Ihrer Region](/connectors/common/outbound-ip-addresses) zulassen, können Ihre Logik-Apps auf Speicherkonten in einer anderen Region zugreifen, außer wenn Sie den Azure Table Storage- oder Azure Queue Storage-Connector verwenden. Um auf Ihren Table Storage oder Queue Storage zuzugreifen, können Sie stattdessen HTTP-Trigger und -Aktionen verwenden. Weitere Optionen finden Sie unter [Zugreifen auf Speicherkonten hinter Firewalls](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>IP-Adressen für eingehende Richtung

In diesem Abschnitt sind nur die IP-Adressen des Azure Logic Apps-Diensts für die eingehende Richtung aufgeführt. Informationen zur Vorgehensweise bei Verwendung von Azure Government finden Sie unter [Azure Government: Eingehende IP-Adressen](#azure-government-inbound).

> [!TIP]
> Zur Reduzierung der Komplexität beim Erstellen von Sicherheitsregeln können Sie optional das [Diensttag](../virtual-network/service-tags-overview.md) **LogicAppsManagement** verwenden, anstatt für jede Region Logic Apps-IP-Adresspräfixe für die eingehende Richtung anzugeben.
>
> Manche Connectors führen eingehende Webhookrückrufe an den Azure Logic Apps-Dienst durch. Bei diesen verwalteten Connectors können Sie optional das **AzureConnectors**-Diensttag verwenden, anstatt für jede Region IP-Adresspräfixe des verwalteten Connectors für die ausgehende Richtung anzugeben. 
> Diese Tags funktionieren in den Regionen, in denen der Logic Apps-Dienst verfügbar ist.
>
> Die folgenden Connectors führen eingehende Webhookrückrufe an den Logic Apps-Dienst durch:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign Demo, Adobe Sign Preview, Adobe Sign Stage, Microsoft Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign Demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, Shifts for Microsoft Teams, Teamwork Projects, Typeform
>
> \* **SAP:** Der Aufrufer der Rückgabe hängt davon ab, ob es sich bei der Bereitstellungsumgebung um eine mehrinstanzenfähige Azure-Umgebung oder eine ISE handelt. In einer mehrinstanzenfähigen Umgebung führt das lokale Datengateway den Rückruf an den Logic Apps-Dienst aus. In einer ISE führt der SAP-Connector den Rückruf an den Logic Apps-Dienst aus.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant--single-tenant---inbound-ip-addresses"></a>Mehrinstanzenfähig und einzelmandantenfähig: Eingehende IP-Adressen

| Region | IP |
|--------|----|
| Australien (Osten) | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161, 13.70.78.192 – 13.70.78.223 |
| Australien, Südosten | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brasilien Süd | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198, 191.233.207.32 – 191.233.207.63 |
| Brasilien, Südosten | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Kanada, Mitte | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105, 20.38.149.160 – 20.38.149.191 |
| Kanada, Osten | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indien, Mitte | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA (Mitte) | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39, 52.182.141.160 – 52.182.141.191 |
| Asien, Osten | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| East US | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165, 20.42.72.160 – 20.42.72.191 |
| USA (Ost) 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253, 20.44.17.224 – 20.44.17.255 |
| Frankreich, Mitte | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203, 40.79.139.160 – 40.79.139.191 |
| Frankreich, Süden | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Deutschland, Norden | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Deutschland, Westen-Mitte | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0, 51.116.243.224 – 51.116.243.255 |
| Japan, Osten | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164, 13.78.111.160 – 13.78.111.191 |
| Japan, Westen | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Jio Indien, Westen | 20.193.206.48,20.193.206.49,20.193.206.50,20.193.206.51 |
| Korea, Mitte | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea, Süden | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA Nord Mitte | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Nordeuropa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39, 13.69.231.160–13.69.231.191 |
| Norwegen, Osten | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Südafrika, Norden | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Südafrika, Westen | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA Süd Mitte | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132, 13.73.244.160 – 13.73.244.191 |
| Indien (Süden) | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Asien, Südosten | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6, 13.67.13.224 – 13.67.13.255 |
| Schweiz, Norden | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Schweiz, Westen | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| VAE, Mitte | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Vereinigte Arabische Emirate, Norden | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| UK, Süden | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81, 51.105.69.96 – 51.105.69.127 |
| UK, Westen | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA, Westen-Mitte | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa, Westen | 13.95.155.53, 52.174.54.218, 52.174.49.6, 13.69.71.160 – 13.69.71.191 |
| Indien, Westen | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA (Westen) | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA, Westen 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68, 40.78.245.160 – 40.78.245.191 |
| USA, Westen 3| 20.150.172.240, 20.150.172.242, 20.150.172.243, 20.150.172.241 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government: IP-Adressen für die eingehende Richtung

| Azure Government-Region | IP |
|-------------------------|----|
| US Gov Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Government, Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD, Mitte | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>IP-Adressen für die ausgehende Richtung

In diesem Abschnitt sind die IP-Adressen des Azure Logic Apps-Diensts für die ausgehende Richtung aufgeführt. Informationen zur Vorgehensweise bei Verwendung von Azure Government finden Sie unter [Azure Government: Ausgehende IP-Adressen](#azure-government-outbound). Falls für Ihren Worfklow [verwaltete Connectors](../connectors/managed.md) (Office 365 Outlook-Connector oder SQL-Connector) oder [benutzerdefinierte Connectors](/connectors/custom-connectors/) verwendet werden, muss die Firewall in der Azure-Region Ihrer Logik-App zusätzlich den Zugriff für *alle* [ausgehenden IP-Adressen von verwalteten Connectors](/connectors/common/outbound-ip-addresses) zulassen. Wenn Ihr Workflow benutzerdefinierte Connectors verwendet, die über die [lokale Datengatewayressource in Azure](logic-apps-gateway-connection.md) auf lokale Ressourcen zugreifen, müssen Sie die Gatewayinstallation so einrichten, dass der Zugriff auf die [ausgehenden IP-Adressen des entsprechenden *verwalteten Connectors*](/connectors/common/outbound-ip-addresses) zugelassen wird. Weitere Informationen zum Einrichten von Kommunikationseinstellungen auf dem Gateway finden Sie in den folgenden Themen:

* [Anpassen von Kommunikationseinstellungen für das lokale Datengateway](/data-integration/gateway/service-gateway-communication)
* [Konfigurieren von Proxyeinstellungen für das lokale Datengateway](/data-integration/gateway/service-gateway-proxy)

> [!TIP]
> Zur Reduzierung der Komplexität beim Erstellen von Sicherheitsregeln können Sie optional das [Diensttag](../virtual-network/service-tags-overview.md) **LogicApps** verwenden, anstatt für jede Region Logic Apps-IP-Adresspräfixe für die ausgehende Richtung anzugeben. Optional können Sie auch das Diensttag **AzureConnectors** für verwaltete Connectors verwenden, die ausgehende Aufrufe an die entsprechenden Dienste wie Azure Storage oder Azure Event Hubs senden, anstatt für jede Region IP-Adresspräfixe des verwalteten Connectors für die ausgehende Richtung anzugeben. Diese Tags funktionieren in den Regionen, in denen der Logic Apps-Dienst verfügbar ist.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant--single-tenant---outbound-ip-addresses"></a>Mehrinstanzenfähig und einzelmandantenfähig: Ausgehende IP-Adressen

| Region | IP der Logik-Apps |
|--------|---------------|
| Australien (Osten) | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139, 13.70.78.192 – 13.70.78.223 |
| Australien, Südosten | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 |
| Brasilien Süd | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131, 191.233.207.32 – 191.233.207.63 |
| Brasilien, Südosten | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 |
| Kanada, Mitte | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150, 20.38.149.160 – 20.38.149.191 |
| Kanada, Osten | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 |
| Indien, Mitte | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 |
| USA (Mitte) | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16, 52.182.141.160 – 52.182.141.191 |
| Asien, Osten | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 |
| East US | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208, 20.42.72.160 – 20.42.72.191 |
| USA (Ost) 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236, 20.44.17.224 – 20.44.17.255 |
| Frankreich, Mitte | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104, 40.79.139.160 – 40.79.139.191 |
| Frankreich, Süden | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 |
| Deutschland, Norden | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 |
| Deutschland, Westen-Mitte | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51, 51.116.243.224 – 51.116.243.255 |
| Japan, Osten | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232, 13.78.111.160 – 13.78.111.191 |
| Japan, Westen | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 |
| Jio Indien, Westen | 20.193.206.128, 20.193.206.129, 20.193.206.130, 20.193.206.131, 20.193.206.132, 20.193.206.133, 20.193.206.134, 20.193.206.135 |
| Korea, Mitte | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 |
| Korea, Süden | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 |
| USA Nord Mitte | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 |
| Nordeuropa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181, 13.69.231.160 - 13.69.231.191 |
| Norwegen, Osten | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 |
| Südafrika, Norden | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 |
| Südafrika, Westen | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 |
| USA Süd Mitte | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225, 13.73.244.160 - 13.73.244.191 |
| Indien (Süden) | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 |
| Asien, Südosten | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128, 13.67.13.224 - 13.67.13.255 |
| Schweiz, Norden | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 |
| Schweiz, Westen | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 |
| VAE, Mitte | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 |
| Vereinigte Arabische Emirate, Norden | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 |
| UK, Süden | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24, 51.105.69.96 - 51.105.69.127 |
| UK, Westen | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 |
| USA, Westen-Mitte | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75, 13.71.199.128 - 13.71.199.159 |
| Europa, Westen | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167, 13.69.71.160 – 13.69.71.191 |
| Indien, Westen | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 |
| USA (Westen) | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 |
| USA, Westen 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219, 40.78.245.160 – 40.78.245.191 |
| USA, Westen 3 | 20.150.181.32, 20.150.181.33, 20.150.181.34, 20.150.181.35, 20.150.181.36, 20.150.181.37, 20.150.181.38, 20.150.173.192 |
|||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government: IP-Adressen für die ausgehende Richtung

| Region | IP der Logik-Apps |
|--------|---------------|
| US DoD, Mitte | 52.182.48.215, 52.182.92.143 |
| US Gov Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 |
| US Gov Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 |
| US Government, Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 |
|||

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Ihre erste Logik-App erstellen](../logic-apps/quickstart-create-first-logic-app-workflow.md).
* Lernen Sie [allgemeine Beispiele und Szenarien](../logic-apps/logic-apps-examples-and-scenarios.md) kennen.
