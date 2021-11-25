---
title: Bearbeiten von Runtime- und Umgebungseinstellungen in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten
description: Ändern Sie die Runtime- und Umgebungseinstellungen für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: eec377641eca7dd31ab04e9f6cdee949352d4a61
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491144"
---
# <a name="edit-host-and-app-settings-for-logic-apps-in-single-tenant-azure-logic-apps"></a>Bearbeiten von Einstellungen für Hosts und Apps für Logik-Apps in Azure Logic Apps-Instanzen mit einem einzelnen Mandanten

In Azure Logic Apps-Instanzen *mit einem einzelnen Mandanten* geben die *App-Einstellungen* für eine Logik-App die globalen Konfigurationsoptionen an, die für *alle Workflows* in dieser Logik-App gelten. Diese Einstellungen gelten jedoch *nur*, wenn diese Workflows in Ihrer *lokalen Entwicklungsumgebung* ausgeführt werden. Bei der lokalen Ausführung können die Workflows auf diese App-Einstellungen als *lokale Umgebungsvariablen* zugreifen, die von lokalen Entwicklungstools für Werte verwendet werden, die sich zwischen Umgebungen oft ändern können. Diese Werte können beispielsweise Verbindungszeichenfolgen enthalten. Bei Bereitstellungen in Azure werden App-Einstellungen ignoriert und nicht in Ihre Bereitstellung eingeschlossen.

Ihre Logik-App verfügt auch über *Hosteinstellungen*, die die Runtimekonfigurationseinstellungen und Werte angeben, die für *alle Workflows* in dieser Logik-App gelten. Beispiele hierfür sind Standardwerte für den Durchsatz, die Kapazität und die Datengröße, unabhängig davon, ob die *Ausführung lokal oder in Azure erfolgt*.

<a name="app-settings-parameters-deployment"></a>

## <a name="app-settings-parameters-and-deployment"></a>App-Einstellungen, Parameter und Bereitstellung

In Azure Logic Apps-Instanzen mit *mehreren Mandanten* hängt die Bereitstellung von ARM-Vorlagen (Azure Resource Manager) ab, die die Ressourcenbereitstellung für Logik-Apps und die Infrastruktur kombinieren und verarbeiten. Dieser Entwurf stellt eine Herausforderung dar, wenn Sie Umgebungsvariablen für Logik-Apps in verschiedenen Entwicklungs-, Test- und Produktionsumgebungen verwalten müssen. Alles in einer ARM-Vorlage wird bei der Bereitstellung definiert. Wenn Sie nur eine einzelne Variable ändern müssen, müssen Sie alles erneut bereitstellen.

In Azure Logic Apps-Instanzen *mit einem einzelnen Mandanten* wird die Bereitstellung einfacher, da Sie die Ressourcenbereitstellung zwischen Apps und der Infrastruktur trennen können. Sie können *Parameter* zum Abstrahieren von Werten verwenden, die sich zwischen Umgebungen ändern können. Durch das Definieren der in Ihren Workflows zu verwendenden Parameter können Sie sich zunächst auf das Entwerfen Ihrer Workflows konzentrieren und Ihre umgebungsspezifischen Variablen dann später einfügen. Mithilfe der App-Einstellungen und -Parameter können Sie Ihre Umgebungsvariablen zur Laufzeit aufrufen und darauf verweisen. Auf diese Weise müssen Sie Ihre Lösung nicht so oft erneut bereitstellen.

App-Einstellungen werden mit Azure Key Vault integriert. Sie können [direkt auf sichere Zeichenfolgen verweisen](../app-service/app-service-key-vault-references.md) (z. B. Verbindungszeichenfolgen und Schlüssel). Ähnlich wie bei ARM-Vorlagen (Azure Resource Manager), bei denen Sie Umgebungsvariablen zum Bereitstellungszeitpunkt definieren können, können Sie App-Einstellungen in der [Workflowdefinition für Ihre Logik-App](/azure/templates/microsoft.logic/workflows) definieren. Anschließend können Sie dynamisch generierte Infrastrukturwerte erfassen (z. B. Verbindungsendpunkte oder Speicherzeichenfolgen). App-Einstellungen haben jedoch Größenbeschränkungen, und in bestimmten Azure Logic Apps-Bereichen kann nicht auf diese verwiesen werden.

Weitere Informationen zur Einrichtung Ihrer Logik-Apps für die Bereitstellung finden Sie in der folgenden Dokumentation:

- [Erstellen von Parametern für Werte, die sich in Workflows zwischen Umgebungen für Azure Logic Apps-Instanzen mit einem einzelnen Mandanten ändern](parameterize-workflow-app.md)
- [Übersicht über die DevOps-Bereitstellung für auf einem einzelnen Mandanten basierende Logik-Apps](devops-deployment-single-tenant-azure-logic-apps.md)
- [Einrichten der DevOps-Bereitstellung für auf einem einzelnen Mandanten basierende Logik-Apps](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="visual-studio-code-project-structure"></a>Visual Studio Code-Projektstruktur

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="reference-local-settings-json"></a>

## <a name="reference-for-app-settings---localsettingsjson"></a>Verweis für App-Einstellungen (local.settings.json)

In Visual Studio Code enthält die Datei **local.settings.json** auf der Stammebene Ihres Logik-App-Projekts globale Konfigurationsoptionen, die bei der Ausführung in Ihrer lokalen Entwicklungsumgebung für *alle Workflows* in dieser Logik-App gelten. Wenn Ihre Workflows lokal ausgeführt werden, erfolgt der Zugriff auf diese Einstellungen als lokale Umgebungsvariablen, deren Werte sich zwischen den verschiedenen Umgebungen, in denen Sie Ihre Workflows ausführen, oft ändern können. Lesen Sie den Abschnitt [Verwalten von App-Einstellungen (local.settings.json)](#manage-app-settings), um zu erfahren, wie Sie diese Einstellungen anzeigen und verwalten.

App-Einstellungen in Azure Logic Apps funktionieren ähnlich wie App-Einstellungen in Azure Functions oder Azure-Web-Apps. Wenn Sie diese anderen Dienste bereits verwendet haben, sind Sie möglicherweise mit den App-Einstellungen vertraut. Weitere Informationen finden Sie unter [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) und [Arbeiten mit Azure Functions Core Tools – Datei für lokale Einstellungen](../azure-functions/functions-develop-local.md#local-settings-file).

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `AzureWebJobsStorage` | Keine | Hiermit wird die Verbindungszeichenfolge für ein Azure-Speicherkonto festgelegt. |
| `Workflows.<workflowName>.FlowState` | Keine | Hiermit wird der Status für <*Workflowname*> festgelegt. |
| `Workflows.<workflowName>.RuntimeConfiguration.RetentionInDays` | Keine | Hiermit werden die Vorgangsoptionen für <*Workflowname*> festgelegt. |
| `Workflows.Connection.AuthenticationAudience` | Keine | Hiermit wird die Zielgruppe für die Authentifizierung einer von Azure gehosteten Verbindung festgelegt. |
| `Workflows.WebhookRedirectHostUri` | Keine | Hiermit wird der Hostname festgelegt, der für Rückruf-URLs für Webhooks verwendet werden soll. |
| `WEBSITE_LOAD_ROOT_CERTIFICATES` | Keine | Hiermit werden die Fingerabdrücke für die Stammzertifikate festgelegt, die als vertrauenswürdig gelten. |
||||

<a name="manage-app-settings"></a>

## <a name="manage-app-settings---localsettingsjson"></a>Verwalten von App-Einstellungen (local.settings.json)

Informationen zum Hinzufügen, Aktualisieren oder Löschen von App-Einstellungen finden Sie in den folgenden Abschnitten zu Visual Studio Code, dem Azure-Portal, der Azure CLI oder ARM-Vorlagen (Bicep). App-Einstellungen, die für Logik-Apps spezifisch sind, finden Sie im [Referenzleitfaden für verfügbare App-Einstellungen (local.settings.json)](#reference-local-settings-json).

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie die folgenden Schritte aus, um die App-Einstellungen für Ihre Logik-App in Visual Studio Code zu überprüfen:

1. Suchen Sie die Datei **local.settings.json** auf der Stammprojektebene in Ihrem Logik-App-Projekt, und öffnen Sie diese.

1. Überprüfen Sie die App-Einstellungen für Ihre Logik-App im `Values`-Objekt.

   Weitere Informationen zu diesen Einstellungen finden Sie im [Referenzleitfaden für verfügbare App-Einstellungen (local.settings.json)](#reference-local-settings-json).

Führen Sie die folgenden Schritte aus, um eine App-Einstellung hinzuzufügen:

1. Suchen Sie in der Datei **local.settings.json** nach dem `Values`-Objekt.

1. Fügen Sie im `Values`-Objekt die App-Einstellung hinzu, die Sie bei lokalen Ausführungen in Visual Studio Code anwenden möchten. Einige Einstellungen ermöglichen es Ihnen, eine Einstellung für einen bestimmten Workflow hinzuzufügen. Beispiel:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "Workflows.WorkflowName1.FlowState" : "Disabled",
         <...>
     }
   }
   ```

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um die App-Einstellungen für Ihre auf einem einzelnen Mandanten basierende Logik-App im Azure-Portal zu überprüfen:

1. Suchen Sie im Suchfeld des [Azure-Portals](https://portal.azure.com/) Ihre Logik-App, und öffnen Sie diese.

1. Klicken Sie im Logik-App-Menü unter **Einstellungen** auf **Konfiguration**.

1. Überprüfen Sie auf der Seite **Konfiguration** auf der Registerkarte **Anwendungseinstellungen** die App-Einstellungen für Ihre Logik-App.

   Weitere Informationen zu diesen Einstellungen finden Sie im [Referenzleitfaden für verfügbare App-Einstellungen (local.settings.json)](#reference-local-settings-json).

1. Klicken Sie auf **Werte anzeigen**, um alle Werte anzuzeigen. Alternativ können Sie auf einen einzelnen Wert klicken, um diesen anzuzeigen.

Führen Sie die folgenden Schritte aus, um eine Einstellung hinzuzufügen:

1. Klicken Sie auf der Registerkarte **Anwendungseinstellungen** unter **Anwendungseinstellungen** auf **Neue Anwendungseinstellung**.

1. Geben Sie für **Name** den *Schlüssel* oder Namen für Ihre neue Einstellung ein.

1. Geben Sie für **Wert** den Wert für Ihre neue Einstellung ein.

1. Wenn Sie Ihr neues *Schlüssel-Wert*-Paar erstellen möchten, wählen Sie **OK** aus.

:::image type="content" source="./media/edit-app-settings-host-settings/portal-app-settings-values.png" alt-text="Screenshot: Azure-Portal und Konfigurationsbereich mit den App-Einstellungen und Werten für eine auf einem einzelnen Mandanten basierende Logik-App." lightbox="./media/edit-app-settings-host-settings/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie den Befehl `az logicapp config appsettings list` aus, um Ihre aktuellen App-Einstellungen mithilfe der Azure CLI zu überprüfen. Stellen Sie sicher, dass Ihr Befehl die Parameter `--name -n` und `--resource-group -g` enthält. Beispiel:

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Weitere Informationen zu diesen Einstellungen finden Sie im [Referenzleitfaden für verfügbare App-Einstellungen (local.settings.json)](#reference-local-settings-json).

Führen Sie den Befehl `az logicapp config appsettings set` aus, um eine App-Einstellung mithilfe der Azure CLI hinzuzufügen oder zu aktualisieren. Stellen Sie sicher, dass Ihr Befehl die Parameter `--name n` und `--resource-group -g` enthält. Der folgende Befehl erstellt beispielsweise eine Einstellung mit einem Schlüssel namens `CUSTOM_LOGIC_APP_SETTING` mit einem Wert von `12345`:

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

---

<a name="reference-host-json"></a>

## <a name="reference-for-host-settings---hostjson"></a>Referenz für Hosteinstellungen (host.json)

In Visual Studio Code enthält die Metadatendatei **host.json** auf der Stammebene Ihres Logik-App-Projekts die Runtimeeinstellungen und Standardwerte, die für *alle Workflows* in einer Logik-App-Ressource gelten, unabhängig davon, ob die Ausführung lokal oder in Azure erfolgt. Lesen Sie den Abschnitt [Verwalten von Hosteinstellungen (host.json)](#manage-host-settings), um zu erfahren, wie Sie diese Einstellungen anzeigen und verwalten. In der Dokumentation [Grenzwert- und Konfigurationsinformationen für Azure Logic Apps](logic-apps-limits-and-config.md#definition-limits) finden Sie ebenfalls auf Logik-Apps bezogene Informationen zu Grenzwerten.

<a name="job-orchestration"></a>

### <a name="job-orchestration-throughput"></a>Auftragsorchestrierungsdurchsatz

Diese Einstellungen wirken sich auf den Durchsatz und die Kapazität für Azure Logic Apps-Instanzen mit einem einzelnen Mandanten für die Ausführung von Workflowvorgängen aus.

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Jobs.BackgroundJobs.DispatchingWorkersPulseInterval` | `00:00:01` <br>(1 Sek.) | Hiermit wird der Intervall für Auftragsverteiler zum Abfragen der Auftragswarteschlange festgelegt, wenn die vorherige Abfrage keine Aufträge zurückgibt. Auftragsverteiler fragen die Warteschlange sofort ab, wenn die vorherige Abfrage einen Auftrag zurückgibt. |
| `Jobs.BackgroundJobs.NumWorkersPerProcessorCount` | `192` Verteilerworkerinstanzen | Hiermit wird die Anzahl der *Verteilerworkerinstanzen* oder *Auftragsverteiler* festgelegt, die pro Prozessorkern verfügbar sein sollen. Dieser Wert wirkt sich auf die Anzahl der Workflowausführungen pro Kern aus. |
| `Jobs.BackgroundJobs.NumPartitionsInJobTriggersQueue` | `1` Auftragswarteschlange | Hiermit wird die Anzahl der Auftragswarteschlangen festgelegt, die von Auftragsverteilern für die zu verarbeitenden Aufträge angezeigt werden. Dieser Wert wirkt sich auch auf die Anzahl der Speicherpartitionen aus, in denen Auftragswarteschlangen vorhanden sind. |
| `Jobs.BackgroundJobs.NumPartitionsInJobDefinitionsTable` | `4` Auftragspartitionen | Hiermit wird die Anzahl der Auftragspartitionen in der Auftragsdefinitionstabelle festgelegt. Dieser Wert steuert, inwieweit der Ausführungsdurchsatz von den Partitionsspeichergrenzwerten beeinflusst wird. |
||||

<a name="run-duration-history"></a>

### <a name="run-duration-and-history"></a>Ausführungsdauer und -verlauf

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.FlowRetentionThreshold` | `90.00:00:00` <br>(90 Tage) | Hiermit wird die Zeitdauer festgelegt, für die der Ausführungsverlauf von Workflows nach dem Start einer Ausführung gespeichert werden soll. |
| `Runtime.Backend.FlowRunTimeout` | `90.00:00:00` <br>(90 Tage) | Hiermit wird die Zeitdauer festgelegt, die ein Workflow weiter ausgeführt werden kann, bevor ein Timeout erzwungen wird. <p><p>**Wichtig:** Stellen Sie sicher, dass dieser Wert kleiner als oder gleich dem Wert `Runtime.FlowRetentionThreshold` ist. Andernfalls werden Ausführungsverläufe möglicherweise gelöscht, bevor die zugeordneten Aufträge abgeschlossen sind. |
||||
   
<a name="run-actions"></a>

### <a name="run-actions"></a>Ausführen von Aktionen

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.ActionJobExecutionTimeout` | `00:10:00` <br>(10 Minuten) | Legt die Zeitdauer für die Ausführung eines Workflow-Aktionsauftrags fest, bevor ein Time out und ein Wiederholungsversuch ausgeführt werden. |
||||

<a name="inputs-outputs"></a>

### <a name="inputs-and-outputs"></a>Eingaben und Ausgaben

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.FlowRunActionJob.MaximumActionResultSize` | `209715200` Byte | Hiermit wird die maximale Größe in Byte festgelegt, die die Eingaben und Ausgaben in einer Aktion kombiniert aufweisen können. |
| `Runtime.ContentLink.MaximumContentSizeInBytes` | `104857600` Zeichen | Hiermit wird die maximale Größe an Zeichen festgelegt, die eine Eingabe oder Ausgabe in einem Trigger oder in einer Aktion aufweisen kann. |
||||

<a name="pagination"></a>

### <a name="pagination"></a>Paginierung

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPageCount` | `1000` Seiten | Wenn die Paginierung für einen Vorgang unterstützt wird und aktiviert ist, wird die maximale Anzahl der Seiten festgelegt, die zur Laufzeit zurückgegeben oder verarbeitet werden. |
||||

<a name="chunking"></a>

### <a name="chunking"></a>Segmentierung

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumContentLengthInBytesForPartialContent` | `1073741824` Byte | Wenn die Segmentierung für einen Vorgang unterstützt wird und aktiviert ist, wird die maximale Größe für den heruntergeladenen oder hochgeladenen Inhalt in Byte festgelegt. |
| `Runtime.FlowRunRetryableActionJobCallback.MaxChunkSizeInBytes` | `52428800` Byte | Wenn die Segmentierung für einen Vorgang unterstützt wird und aktiviert ist, wird die maximale Größe für jede Inhaltssegmentierung in Byte festgelegt. |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumRequestCountForPartialContent` | `1000` Anforderungen | Wenn die Segmentierung für einen Vorgang unterstützt wird und aktiviert ist, wird die maximale Anzahl der Anforderungen festgelegt, die eine Aktionsausführung für Downloadinhalte durchführen kann. |
||||

<a name="trigger-concurrency"></a>

### <a name="trigger-concurrency"></a>Triggerparallelität

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.Trigger.MaximumRunConcurrency` | `100` Ausführungen | Hiermit wird die maximale Anzahl gleichzeitiger Ausführungen festgelegt, die ein Trigger starten kann. Dieser Wert wird in der Parallelitätsdefinition des Triggers angezeigt. |
| `Runtime.Trigger.MaximumWaitingRuns` | `200` Ausführungen | Hiermit wird die maximale Anzahl der Ausführungen festgelegt, die warten können, nachdem gleichzeitige Ausführungen den Maximalwert erreicht haben. Dieser Wert wird in der Parallelitätsdefinition des Triggers angezeigt. |
||||

<a name="for-each-loop"></a>

### <a name="for-each-loops"></a>„For each“-Schleife

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.Backend.FlowDefaultForeachItemsLimit` | `100000` <br>(100.000 Arrayelemente) | Hiermit wird die maximale Anzahl der Arrayelemente für einen *zustandsbehafteten Workflow* festgelegt, die in einer `For each`-Schleife verarbeitet werden sollen. |
| `Runtime.Backend.Stateless.FlowDefaultForeachItemsLimit` | `100` Elemente | Hiermit wird die maximale Anzahl der Arrayelemente für einen *zustandslosen Workflow* festgelegt, die in einer `For each`-Schleife verarbeitet werden sollen. |
| `Runtime.Backend.ForeachDefaultDegreeOfParallelism` | `20` Iterationen | Hiermit wird die Standardanzahl gleichzeitiger Iterationen oder der Parallelitätsgrad in einer `For each`-Schleife festgelegt. Legen Sie den Wert für eine sequenzielle Ausführung auf `1` fest. |
| `Runtime.Backend.FlowDefaultSplitOnItemsLimit` | `100000` <br>(100.000 Arrayelemente) | Hiermit wird die maximale Anzahl der Arrayelemente festgelegt, die basierend auf der `SplitOn`-Einstellung in mehrere Workflowinstanzen aufgelöst oder aufgeteilt werden sollen. |
||||

<a name="until-loop"></a>

### <a name="until-loops"></a>„Until“-Schleifen

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.Backend.MaximumUntilLimitCount` | `5000` Iterationen | Hiermit wird für einen *zustandsbehafteten Workflow* die maximal mögliche Anzahl für die `Count`-Eigenschaft in einer `Until`-Aktion festgelegt. |
| `Runtime.Backend.Stateless.MaximumUntilLimitCount` | `100` Iterationen | Hiermit wird für einen *zustandslosen Workflow* die maximal mögliche Anzahl für die `Count`-Eigenschaft in einer `Until`-Aktion festgelegt. |
| `Runtime.Backend.Stateless.FlowRunTimeout` | `00:05:00` <br>(5 Min.) | Hiermit wird die maximale Wartezeit für eine `Until`-Schleife in einem zustandslosen Workflow festgelegt. |
||||

<a name="variables"></a>

### <a name="variables"></a>Variablen

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.Backend.DefaultAppendArrayItemsLimit` | `100000` <br>(100.000 Arrayelemente) | Hiermit wird die maximale Anzahl der Elemente in einer Variablen mit dem Array-Typ festgelegt. |
| `Runtime.Backend.VariableOperation.MaximumVariableSize` | Zustandsbehafteter Workflow: `104857600` Zeichen | Hiermit wird die maximale Größe an Zeichen für die Inhalte festgelegt, die eine Variable speichern kann, wenn sie in einem zustandsbehafteten Workflow verwendet wird. |
| `Runtime.Backend.VariableOperation.MaximumStatelessVariableSize` | Zustandsloser Workflow: `1024` Zeichen | Hiermit wird die maximale Größe an Zeichen für die Inhalte festgelegt, die eine Variable speichern kann, wenn sie in einem zustandslosen Workflow verwendet wird. |
||||

<a name="http-webhook"></a>

### <a name="http-operations"></a>HTTP-Vorgänge

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.Backend.HttpOperation.RequestTimeout` | `00:03:45` <br>(3 Min. 45 Sek.) | Hiermit wird der Anforderungstimeoutwert für HTTP-Trigger und -Aktionen festgelegt. |
| `Runtime.Backend.HttpOperation.MaxContentSize` | `104857600` Byte | Hiermit wird die maximale Anforderungsgröße in Byte für HTTP-Trigger und -Aktionen festgelegt. |
| `Runtime.Backend.HttpOperation.DefaultRetryCount` | `4` Wiederholungsversuche | Hiermit wird die Standardwiederholungsanzahl für HTTP-Trigger und -Aktionen festgelegt. |
| `Runtime.Backend.HttpOperation.DefaultRetryInterval` | `00:00:07` <br>(7 Sek.) | Hiermit wird das Standardwiederholungsintervall für HTTP-Trigger und -Aktionen festgelegt. |
| `Runtime.Backend.HttpOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 Std.) | Hiermit wird das maximale Wiederholungsintervall für HTTP-Trigger und -Aktionen festgelegt. |
| `Runtime.Backend.HttpOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 Sek.) | Hiermit wird das minimale Wiederholungsintervall für HTTP-Trigger und -Aktionen festgelegt. |
||||

<a name="http-webhook"></a>

### <a name="http-webhook-operations"></a>HTTP-Webhookvorgänge

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.Backend.HttpWebhookOperation.RequestTimeout` | `00:02:00` <br>(2 min) | Hiermit wird der Anforderungstimeoutwert für HTTP-Webhooktrigger und -aktionen festgelegt. |
| `Runtime.Backend.HttpWebhookOperation.MaxContentSize` | `104857600` Byte | Hiermit wird die maximale Anforderungsgröße in Byte für HTTP-Webhooktrigger und -aktionen festgelegt. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryCount` | `4` Wiederholungsversuche | Hiermit wird die Standardwiederholungsanzahl für HTTP-Webhooktrigger und -aktionen festgelegt. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryInterval` | `00:00:07` <br>(7 Sek.) | Hiermit wird das Standardwiederholungsintervall für HTTP-Webhooktrigger und -aktionen festgelegt. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 Std.) | Hiermit wird das maximale Wiederholungsintervall für HTTP-Webhooktrigger und -aktionen festgelegt. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 Sek.) | Hiermit wird das minimale Wiederholungsintervall für HTTP-Webhooktrigger und -aktionen festgelegt. |
| `Runtime.Backend.HttpWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>(1 Std.) | Hiermit wird das Standardaktivierungsintervall für HTTP-Webhooktrigger und -aktionsaufträge festgelegt. |
||||

<a name="built-in-azure-functions"></a>

### <a name="built-in-azure-functions-operations"></a>Integrierte Azure Functions-Vorgänge

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.Backend.FunctionOperation.RequestTimeout` | `00:03:45` <br>(3 Min. 45 Sek.) | Hiermit wird der Anforderungstimeoutwert für Azure Functions-Aktionen festgelegt. |
| `Runtime.Backend.FunctionOperation.MaxContentSize` | `104857600` Byte | Hiermit wird die maximale Anforderungsgröße für Azure Functions-Aktionen in Byte angegeben. |
| `Runtime.Backend.FunctionOperation.DefaultRetryCount` | `4` Wiederholungsversuche | Hiermit wird die Standardwiederholungsanzahl für Azure Functions-Aktionen festgelegt. |
| `Runtime.Backend.FunctionOperation.DefaultRetryInterval` | `00:00:07` <br>(7 Sek.) | Hiermit wird das Standardwiederholungsintervall für Azure Functions-Aktionen festgelegt. |
| `Runtime.Backend.FunctionOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 Std.) | Hiermit wird das maximale Wiederholungsintervall für Azure Functions-Aktionen festgelegt. |
| `Runtime.Backend.FunctionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 Sek.) | Hiermit wird das minimale Wiederholungsintervall für Azure Functions-Aktionen festgelegt. |
||||

<a name="built-in-sql"></a>

### <a name="built-in-sql-operations"></a>Integrierte SQL-Vorgänge

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.ServiceProviders.Sql.QueryExecutionTimeout` | `00:00:30` <br>(30 Sek.) | Hiermit wird der Anforderungstimeoutwert für SQL-Dienstanbietervorgänge festgelegt. |
||||

<a name="built-in-service-bus"></a>

### <a name="built-in-azure-service-bus-operations"></a>Integrierte Azure Service Bus-Vorgänge

| Einstellung | Standardwert | BESCHREIBUNG |
|---------|---------------|-------------|
| `ServiceProviders.ServiceBus.MessageSenderOperationTimeout` | `00:01:00` <br>(1 min) | Es legt das Timeout für das Senden von Nachrichten mit dem integrierten Service Bus-Vorgang fest. |
| `Runtime.ServiceProviders.ServiceBus.MessageSenderPoolSizePerProcessorCount` | `64` Nachrichtenabsender | Hiermit wird die Anzahl der im Nachrichtenabsenderpool zu verwendenden Azure Service Bus-Nachrichtenabsender pro Prozessorkern festgelegt. |
||||

<a name="managed-api-connector"></a>

### <a name="managed-api-connector-operations"></a>Verwaltete API-Connectorvorgänge

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.Backend.ApiConnectionOperation.RequestTimeout` | `00:02:00` <br>(2 min) | Hiermit wird der Anforderungstimeoutwert für verwaltete API-Connectortrigger und -aktionen festgelegt. |
| `Runtime.Backend.ApiConnectionOperation.MaxContentSize` | `104857600` Byte | Hiermit wird die maximale Anforderungsgröße für verwaltete API-Connectortrigger und -aktionen in Byte festgelegt. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryCount` | `4` Wiederholungsversuche | Hiermit wird die Standardwiederholungsanzahl für verwaltete API-Connectortrigger und -aktionen festgelegt. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryInterval` | `00:00:07` <br>(7 Sek.) | Hiermit wird das Standardwiederholungsintervall für verwaltete API-Connectortrigger und -aktionen festgelegt. |
| `Runtime.Backend.ApiWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 Tag) | Hiermit wird das maximale Wiederholungsintervall für verwaltete API-Connectorwebhooktrigger und -aktionen festgelegt. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 Sek.) | Hiermit wird das minimale Wiederholungsintervall für verwaltete API-Connectortrigger und -aktionen festgelegt. |
| `Runtime.Backend.ApiWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>(1 Tag) | Hiermit wird das Standardaktivierungsintervall für verwaltete API-Connectorwebhooktrigger und -aktionsaufträge festgelegt. |
||||

<a name="blob-storage"></a>

### <a name="blob-storage"></a>Blob Storage

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.ContentStorage.RequestOptionsServerTimeout` | `00:00:30` <br>(30 Sek.) | Hiermit wird der Timeoutwert für Blobanforderungen von der Azure Logic Apps-Runtime festgelegt. |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:02:00` <br>(2 min) | Hiermit wird der Vorgangstimeoutwert einschließlich der Wiederholungsversuche für Tabellen- und Warteschlangenspeicheranforderungen von der Azure Logic Apps-Runtime festgelegt. |
| `Runtime.ContentStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>(2 Sek.) | Hiermit wird das Backoffintervall zwischen den an den Blobspeicher gesendeten Wiederholungsversuchen festgelegt. |
| `Runtime.ContentStorage.RequestOptionsMaximumAttempts` | `4` Wiederholungsversuche | Hiermit wird die maximale Anzahl der Wiederholungsversuche festgelegt, die an den Tabellen- und Warteschlangenspeicher gesendet werden. |
||||

<a name="store-inline-or-blob"></a>

### <a name="store-content-inline-or-use-blobs"></a>Inlinespeichern von Inhalten oder Verwenden von Blobs

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.FlowRunEngine.ForeachMaximumItemsForContentInlining` | `20` Elemente | Wenn eine `For each`-Schleife ausgeführt wird, wird der Wert aller Elemente entweder inline mit anderen Metadaten im Tabellenspeicher oder separat im Blobspeicher gespeichert. Hiermit wird die Anzahl der Elemente festgelegt, die inline mit anderen Metadaten gespeichert werden sollen. |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPagesForContentInlining` | `20` Seiten | Hiermit wird die maximale Anzahl der Seiten festgelegt, die als Inlineinhalt im Tabellenspeicher gespeichert werden sollen, bevor sie im Blobspeicher gespeichert werden. |
| `Runtime.FlowTriggerSplitOnJob.MaximumItemsForContentInlining` | `40` Elemente | Wenn die `SplitOn`-Einstellung Arrayelemente in mehrere Workflowinstanzen auflöst, wird der Wert aller Elemente entweder inline mit anderen Metadaten im Tabellenspeicher oder separat im Blobspeicher gespeichert. Hiermit wird die Anzahl der Elemente festgelegt, die inline gespeichert werden sollen. |
| `Runtime.ScaleUnit.MaximumCharactersForContentInlining` | `8192` Zeichen | Hiermit wird die maximale Anzahl der Zeichen für Vorgangseingaben und -ausgaben festgelegt, die inline im Tabellenspeicher gespeichert werden sollen, bevor sie im Blobspeicher gespeichert werden. |
||||

<a name="table-queue-storage"></a>

### <a name="table-and-queue-storage"></a>Tabellen- und Warteschlangenspeicher

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.DataStorage.RequestOptionsServerTimeout` | `00:00:16` <br>(16 Sek.) | Hiermit wird der Timeoutwert für Tabellen- und Warteschlangenspeicheranforderungen von der Azure Logic Apps-Runtime festgelegt. |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:00:45` <br>(45 Sek.) | Hiermit wird der Vorgangstimeoutwert einschließlich der Wiederholungsversuche für Tabellen- und Warteschlangenspeicheranforderungen von der Azure Logic Apps-Runtime festgelegt. |
| `Runtime.DataStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>(2 Sek.) | Hiermit wird das Backoffintervall zwischen den an den Tabellen- und Warteschlangenspeicher gesendeten Wiederholungsversuchen festgelegt. |
| `Runtime.DataStorage.RequestOptionsMaximumAttempts` | `4` Wiederholungsversuche | Hiermit wird die maximale Anzahl der Wiederholungsversuche festgelegt, die an den Tabellen- und Warteschlangenspeicher gesendet werden. |
||||

<a name="retry-policy"></a>

### <a name="retry-policy-for-all-other-operations"></a>Wiederholungsrichtlinie für alle anderen Vorgänge

| Einstellung | Standardwert | Beschreibung |
|---------|---------------|-------------|
| `Runtime.ScaleMonitor.MaxPollingLatency` | `00:00:30` <br>(30 Sek.) | Hiermit wird die maximale Abrufwartezeit für die Runtimeskalierung festgelegt. |
| `Runtime.Backend.Operation.MaximumRetryCount` | `90` Wiederholungsversuche | Hiermit wird die maximale Anzahl der Wiederholungsversuche in der Wiederholungsrichtliniendefinition für einen Workflowvorgang festgelegt. |
| `Runtime.Backend.Operation.MaximumRetryInterval` | `01:00:00:01` <br>(1 Tag 1 Sek.) | Hiermit wird das maximale Intervall in der Wiederholungsrichtliniendefinition für einen Workflowvorgang festgelegt. |
| `Runtime.Backend.Operation.MinimumRetryInterval` | `00:00:05` <br>(5 Sek.) | Hiermit wird das minimale Intervall in der Wiederholungsrichtliniendefinition für einen Workflowvorgang festgelegt. |
||||

<a name="manage-host-settings"></a>

## <a name="manage-host-settings---hostjson"></a>Verwalten von Hosteinstellungen (host.json)

Sie können Hosteinstellungen hinzufügen, aktualisieren oder löschen, die die Runtimekonfigurationseinstellungen und Werte angeben, die für *alle Workflows* in dieser Logik-App gelten. Beispiele hierfür sind Standardwerte für den Durchsatz, die Kapazität und die Datengröße, unabhängig davon, ob die *Ausführung lokal oder in Azure erfolgt*. Hosteinstellungen, die spezifisch für Logik-Apps sind, finden Sie im [Referenzleitfaden für verfügbare Runtime- und Bereitstellungseinstellungen (host.json)](#reference-host-json).

### <a name="visual-studio-code---hostjson"></a>Visual Studio Code (host.json)

Führen Sie die folgenden Schritte aus, um die Hosteinstellungen für Ihre Logik-App in Visual Studio Code zu überprüfen.

1. Suchen Sie die Datei **host.json** auf der Stammprojektebene in Ihrem Logik-App-Projekt, und öffnen Sie diese.

1. Überprüfen Sie im `extensions`-Objekt unter `workflows` und `settings` die Hosteinstellungen, die zuvor zu Ihrer Logik-App hinzugefügt wurden. Andernfalls wird das `extensions`-Objekt nicht in der Datei angezeigt.

   Weitere Informationen zu Hosteinstellungen finden Sie im [Referenzleitfaden für verfügbare Hosteinstellungen (host.json)](#reference-host-json).

Führen Sie die folgenden Schritte aus, um eine Hosteinstellung hinzuzufügen:

1. Fügen Sie in der Datei **host.json** unter dem `extensionBundle`-Objekt das `extensions`-Objekt hinzu, das die Objekte `workflow` und `settings` umfasst. Beispiel:

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. Fügen Sie im `settings`-Objekt eine flache Liste mit den Hosteinstellungen hinzu, die Sie für alle Workflows in Ihrer Logik-App verwenden möchten, unabhängig davon, ob diese Workflows lokal oder in Azure ausgeführt werden. Beispiel:

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

### <a name="azure-portal---hostjson"></a>Azure-Portal (host.json)

Führen Sie die folgenden Schritte aus, um die Hosteinstellungen für Ihre auf einem einzelnen Mandanten basierende Logik-App im Azure-Portal zu überprüfen:

1. Suchen Sie im Suchfeld des [Azure-Portals](https://portal.azure.com/) Ihre Logik-App, und öffnen Sie diese.

1. Wählen Sie im Menü Ihrer Logik-App unter **Entwicklungstools** die Option **Erweiterte Tools** aus.

1. Klicken Sie auf der Seite **Erweiterte Tools** auf **Los**, wodurch die **Kudu**-Umgebung für Ihre Logik-App geöffnet wird.

1. Klicken Sie auf der Kudu-Symbolleiste im Menü der **Debugging-Konsole** auf **CMD**.

1. Beenden Sie im Azure-Portal Ihre Logik-App.

   1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.

   1. Wählen Sie in der Symbolleiste des Bereichs **Übersicht** die Option **Beenden** aus.

1. Wählen Sie im Menü Ihrer Logik-App unter **Entwicklungstools** die Option **Erweiterte Tools** aus.

1. Wählen Sie im Bereich **Erweiterte Tools** die Option **Los** aus, wodurch die Kudu-Umgebung für Ihre Logik-App geöffnet wird.

1. Öffnen Sie auf der Kudu-Symbolleiste das Menü **Debugkonsole**, und wählen Sie **CMD** aus.

   Ein Konsolenfenster wird geöffnet, in dem Sie mithilfe der Eingabeaufforderung zum Ordner **wwwroot** navigieren können. Alternativ können Sie die Verzeichnisstruktur durchsuchen, die oben im Konsolenfenster angezeigt wird.

1. Navigieren Sie entlang des folgenden Pfads zum Ordner **wwwroot**: `...\home\site\wwwroot`.

1. Klicken Sie oben im Konsolenfenster in der Verzeichnistabelle neben der Datei **host.json** auf **Bearbeiten**.

1. Nachdem die Datei **host.json** geöffnet wurde, überprüfen Sie die Hosteinstellungen, die Sie zuvor zu Ihrer Logik-App hinzugefügt haben.

   Weitere Informationen zu Hosteinstellungen finden Sie im [Referenzleitfaden für verfügbare Hosteinstellungen (host.json)](#reference-host-json).

Führen Sie die folgenden Schritte aus, um eine Einstellung hinzuzufügen:

1. Beenden Sie Ihre Logik-App im Azure-Portal, bevor Sie Einstellungen hinzufügen oder bearbeiten.

   1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.
   1. Wählen Sie in der Symbolleiste des Bereichs **Übersicht** die Option **Beenden** aus.

1. Navigieren Sie zurück zur Datei **host.json**. Fügen Sie unter dem `extensionBundle`-Objekt das `extensions`-Objekt hinzu, das die Objekte `workflow` und `settings` umfasst. Beispiel:

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. Fügen Sie im `settings`-Objekt eine flache Liste mit den Hosteinstellungen hinzu, die Sie für alle Workflows in Ihrer Logik-App verwenden möchten, unabhängig davon, ob diese Workflows lokal oder in Azure ausgeführt werden. Beispiel:

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

1. Denken Sie daran, abschließend auf **Speichern** zu klicken.

1. Starten Sie jetzt Ihre Logik-App neu. Navigieren Sie zurück zur Seite **Übersicht** Ihrer Logik-App, und klicken Sie auf **Neu starten**.

---

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Parametern für Werte, die sich in Workflows zwischen Umgebungen für Azure Logic Apps-Instanzen mit einem einzelnen Mandanten ändern](parameterize-workflow-app.md)
- [Einrichten der DevOps-Bereitstellung für Azure Logic Apps-Instanzen mit einem einzelnen Mandanten](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
