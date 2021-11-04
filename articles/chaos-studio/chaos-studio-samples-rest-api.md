---
title: Verwenden der REST-APIs zum Verwalten von Azure Chaos Studio-Experimenten
description: Ausführen und Verwalten eines Chaos-Experiments mit Azure Chaos Studio mithilfe von REST-APIs.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: edbaeece6a486c170603304dfe328974e6b70879
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095359"
---
# <a name="use-the-chaos-studio-rest-apis-to-run-and-manage-chaos-experiments"></a>Verwenden der REST-APIs von Chaos Studio zum Ausführen und Verwalten von Chaos-Experimenten

> [!WARNING]
> Das Einschleusen von Fehlern kann sich auf Ihre Anwendung oder Ihren Dienst auswirken. Achten Sie darauf, dass Kunden nicht gestört werden.  

Die Chaos Studio-API bietet Unterstützung für das programmgesteuerte Starten von Experimenten. Sie können auch den ARMClient und die Azure CLI verwenden, um diese Befehle über die Konsole auszuführen. Die folgenden Beispiele gelten für die Azure CLI.

> [!Warning]
> Diese APIs befinden sich noch in der Entwicklung und können jederzeit geändert werden.

## <a name="rest-apis"></a>REST-APIs

Die Squall-REST-APIs können zum Starten und Beenden von Experimenten, zum Abfragen des Zielstatus, zum Abfragen des Experimentstatus sowie zum Abfragen und Löschen von Abonnementkonfigurationen verwendet werden. Das Hilfsprogramm `AZ CLI` kann verwendet werden, um diese Aktionen über die Befehlszeile auszuführen.

> [!TIP]
> Um eine ausführlichere Ausgabe in der AZ CLI zu erhalten, fügen Sie **--verbose** am Ende der einzelnen Befehle an. Dadurch werden bei der Ausführung von Befehlen mehr Metadaten zurückgegeben, z. B. **x-ms-correlation-request-id**, die das Debuggen erleichtert.

### <a name="chaos-provider-commands"></a>Befehle für Chaos-Anbieter

#### <a name="enumerate-details-about-the-microsoftchaos-resource-provider"></a>Auflisten von Details zum Microsoft.Chaos-Ressourcenanbieter

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-all-the-operations-of-the-chaos-studio-resource-provider"></a>Auflisten aller Vorgänge des Chaos Studio-Ressourcenanbieters

```bash
az rest --method get --url "https://management.azure.com/providers/Microsoft.Chaos/operations?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-chaos-provider-configurations"></a>Auflisten der Chaos-Anbieterkonfigurationen

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/?api-version={apiVersion}" --resource "https://management.azure.com" --verbose 
```

#### <a name="create-chaos-provider-configuration"></a>Auflisten der Chaos-Anbieterkonfiguration

```bash
az rest --method put --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/{chaosProviderType}?api-version={apiVersion}" --body @{providerSettings.json} --resource "https://management.azure.com"
```

### <a name="chaos-target-and-agent-commands"></a>Chaos-Befehl für Ziele und Agents

#### <a name="list-all-the-targets-or-agents-under-a-subscription"></a>Auflisten aller Ziele oder Agents unter einem Abonnement

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos/chaosTargets/?api-version={apiVersion}" --url-parameter "chaosProviderType={chaosProviderType}" --resource "https://management.azure.com"
```

### <a name="chaos-experiment-commands"></a>Befehle für Chaos-Experimente

#### <a name="list-all-experiments-in-a-resource-group"></a>Auflisten aller Experimente in einer Ressourcengruppe

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Chaos/chaosExperiments?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-an-experiment-configuration-details-by-name"></a>Abrufen von Details zur Experimentkonfiguration anhand des Namens

```bash
az rest --method get --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="create-or-update-an-experiment"></a>Erstellen oder Aktualisieren eines Experiments

```bash
az rest --method put --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --body @{experimentName.json} --resource "https://management.azure.com"
```

#### <a name="delete-an-experiment"></a>Löschen eines Experiments

```bash
az rest --method delete --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com" --verbose
```

#### <a name="start-an-experiment"></a>Starten Sie ein Experiment

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/start?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-statuses-history-of-an-experiment"></a>Abrufen der Status (Verlauf) eines Experiments

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/statuses?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-status-of-an-experiment"></a>Abrufen des Status eines Experiments

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/status?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="cancel-stop-an-experiment"></a>Abbrechen (Beenden) eines Experiments

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/cancel?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-the-last-two-experiment-executions"></a>Auflisten der Details der letzten beiden Experimentausführungen

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-a-specific-experiment-execution"></a>Auflisten der Details einer bestimmten Experimentausführung

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails/{executionDetailsId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

## <a name="parameter-definitions"></a>Parameterdefinitionen

| Parametername | Definition | Nachschlagen |
| --- | --- | --- |
| {apiVersion} | Version der API, die bei der Ausführung des angegebenen Befehls verwendet werden soll | Diese Information finden Sie in der [API-Dokumentation](https://aka.ms/chaosrestapi). |
| {experimentId} | Azure-Ressourcen-ID für das Experiment | Diese Information finden Sie auf dem [Portalblatt für Chaos Studio-Experimente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments). |
| {chaosProviderType} | Typ oder Name des Chaos Studio-Anbieters | Verfügbare Anbieter finden Sie in der [Liste der aktuellen Anbieterkonfigurationstypen](chaos-studio-fault-providers.md). |
| {experimentName.json} | JSON mit der Konfiguration des Chaos-Experiments | Wird vom Benutzer generiert. |
| {subscriptionId} | ID des Abonnements, in dem sich die Zielressource befindet | Sie finden diese Information im [Portalblatt für Abonnementportals](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). |
| {resourceGroupName} | Name der Ressourcengruppe, in der sich die Zielressource befindet | Sie finden diese Information im [Portalblatt für Ressourcengruppen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). |
| {executionDetailsId} | Ausführungs-ID einer Experimentausführung | Diese Information finden Sie auf dem [Portalblatt für Chaos Studio-Experimente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments). |
