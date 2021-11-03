---
title: Mit nur einem Mandanten im Vergleich zu mehrinstanzenfähigen Azure Logic Apps
description: Hier lernen Sie die Unterschiede zwischen einer Umgebung mit einem Mandanten, einer Umgebung mit mehreren Mandanten sowie einer Integrationsdienstumgebung (Integration Service Environment, ISE) für Azure Logic Apps kennen.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8e7c77093ba3ddd2496d2e8ac8a78481d71fd1f6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088158"
---
# <a name="single-tenant-versus-multi-tenant-and-integration-service-environment-for-azure-logic-apps"></a>Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps

Azure Logic Apps ist eine cloudbasierte Plattform zum Erstellen und Ausführen von automatisierten *Logik-App-Workflows*, um Ihre Apps, Daten, Dienste und Systeme zu integrieren. Mit dieser Plattform können Sie schnell hochgradig skalierbare Integrationslösungen für Unternehmens- und B2B-Szenarios (Business-to-Business) erstellen. Verwenden Sie zum Erstellen einer Logik-App den Ressourcentyp **Logik-App (Verbrauch)** oder **Logik-App (Standard)** . Der Ressourcentyp „Verbrauch“ kann in einer Azure Logic Apps-*Umgebung mit mehreren Mandanten* oder einer *Integrationsdienstumgebung* ausgeführt werden, der Ressourcentyp „Standard“ in einer Azure Logic Apps-*Einzelmandantenumgebung*.

Bevor Sie den Ressourcentyp auswählen, lesen Sie diesen Artikel, um sich über die Unterschiede zwischen den Ressourcentypen und Dienstumgebungen zu informieren. Anschließend können Sie basierend auf den Anforderungen Ihres Szenarios, den Lösungsanforderungen und der Umgebung, in der Sie Ihre Workflows bereitstellen, hosten und ausführen möchten, entscheiden, welchen Typ Sie verwenden möchten.

Falls Sie noch nicht mit Azure Logic Apps vertraut sind, finden Sie weitere Informationen in den folgenden Artikeln:

* [Übersicht: Was ist Azure Logic Apps?](logic-apps-overview.md)
* [Was ist ein *Logik-App-Workflow*?](logic-apps-overview.md#logic-app-concepts)

<a name="resource-environment-differences"></a>

## <a name="resource-types-and-environments"></a>Ressourcentypen und Umgebungen

Wählen Sie basierend auf Ihrem Szenario, den Lösungsanforderungen, den gewünschten Funktionen sowie der Umgebung, in der Ihre Workflows ausgeführt werden sollen, den **Logik-App**-Ressourcentyp aus, um Logik-App-Workflows zu erstellen.

In der folgenden Tabelle werden die Unterschiede zwischen dem Ressourcentyp **Logik-App (Standard)** und dem Ressourcentyp **Logik-App (Verbrauch)** kurz zusammengefasst. Außerdem lernen Sie die Unterschiede zwischen einer *Einzelmandantenumgebung* und einer *Umgebung mit mehreren Mandanten* sowie einer *Integrationsdienstumgebung* (Integration Service Environment, ISE) in Hinblick auf Bereitstellung, Hosten und Ausführung Ihrer Logik-App-Workflows kennen.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="resource-type-introduction"></a>

## <a name="logic-app-standard-resource"></a>Ressourcentyp „Logik-App (Standard)“

Der Ressourcentyp **Logik-App (Standard)** wird von der neu gestalteten Runtime für Azure Logic Apps-Instanzen mit einem Mandanten unterstützt. Die Runtime wendet das [Azure Functions-Erweiterbarkeitsmodell](../azure-functions/functions-bindings-register.md) an und wird als Erweiterung der Azure Functions-Runtime gehostet. Dieser Entwurf bietet Portabilität, Flexibilität und eine höhere Leistung für Ihre Logik-App-Workflows sowie weitere Funktionen und Vorteile der Azure Functions-Plattform und des Azure App Service-Ökosystems. Beispielsweise können Sie Logik-Apps mit nur einem Mandanten und deren Workflows in [Azure App Service-Umgebung v3](../app-service/environment/overview.md) erstellen, bereitstellen und ausführen.

Mit dem Ressourcentyp „Standard“ wird eine Ressourcenstruktur zum Hosten mehrerer Workflows eingeführt. Dies ist vergleichbar mit einer Azure Functions-App, die mehrere Funktionen hosten kann. Bei einer 1:n-Zuordnung nutzen Workflows in derselben Logik-App und demselben Mandanten Compute- und Verarbeitungsressourcen gemeinsam und bieten aufgrund ihrer Nähe eine bessere Leistung. Diese Struktur stellt einen Unterschied zum Ressourcentyp **Logik-App (Verbrauch)** dar, bei dem zwischen einer Logik-App-Ressource und einem Workflow eine 1:1-Zuordnung besteht.

Weitere Informationen zu Portabilität, Flexibilität und Leistungsverbesserungen finden Sie in den folgenden Abschnitten. Weitere Informationen zur Runtime für Azure Logic Apps-Instanzen mit einem Mandanten sowie Azure Functions-Erweiterungen finden Sie in den folgenden Artikeln:

* [Azure Logic Apps Running Anywhere - Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564) (Azure Logic Apps läuft überall – Runtimeanalyse)
* [Einführung in Azure Functions](../azure-functions/functions-overview.md)
* [Trigger und Bindungen in Azure Functions](../azure-functions/functions-triggers-bindings.md)

<a name="portability"></a>
<a name="flexibility"></a>

### <a name="portability-and-flexibility"></a>Portabilität und Flexibilität

Wenn Sie Logik-Apps mit dem Ressourcentyp **Logik-App (Standard)** erstellen, können Sie Ihre Workflows in anderen Umgebungen, wie z. B. [Azure App Service-Umgebung v3](../app-service/environment/overview.md) bereitstellen und ausführen. Wenn Sie Visual Studio Code mit der Erweiterung **Azure Logic Apps (Standard)** verwenden, können Sie Ihre Workflows *lokal* in Ihrer Entwicklungsumgebung entwickeln, erstellen und ausführen, ohne eine Bereitstellung in Azure durchführen zu müssen. Falls in Ihrem Szenario Container erforderlich sind, [erstellen Sie Logik-Apps mit nur einem Mandanten mithilfe von Logic Apps mit Azure Arc-Unterstützung](azure-arc-enabled-logic-apps-create-deploy-workflows.md). Weitere Informationen finden Sie unter [Was ist Logic Apps mit Azure Arc-Unterstützung?](azure-arc-enabled-logic-apps-overview.md)

Verglichen mit dem mehrinstanzenfähigen Modell, bei dem Sie für eine vorhandene, in Azure ausgeführte Ressource entwickeln müssen, stellen diese Funktionen eine erhebliche Verbesserung und einen entscheidenden Vorteil dar. Zudem basiert das mehrinstanzenfähige Modell für die automatisierte Bereitstellung des Ressourcentyps **Logik-App (Verbrauch)** vollständig auf ARM-Vorlagen (Azure Resource Manager), mit denen die Ressourcenbereitstellung für Apps und die Infrastruktur zusammengeführt und verarbeitet wird.

Mit dem Ressourcentyp **Logik-App (Standard)** wird die Bereitstellung einfacher, da Sie Apps und die Infrastruktur getrennt voneinander bereitstellen können. Sie können die Runtime für die Azure Logic Apps-Instanz mit einem Mandanten und Workflows als Teil Ihrer Logik-App in einem Paket zusammenfassen. Mit allgemeinen Schritten oder Aufgaben können Sie die Ressourcen Ihrer Logik-App als einsatzbereite Artefakte erstellen, zusammenstellen und zippen. Für die Bereitstellung Ihrer Infrastruktur können Sie weiterhin ARM-Vorlagen verwenden, um die einzelnen Ressourcen zusammen mit anderen Prozessen und Pipelines für diese Zwecke bereitzustellen.

Kopieren Sie zum Bereitstellen Ihrer App die Artefakte in die Hostumgebung. Starten Sie anschließend die Apps, um Ihre Workflows auszuführen. Alternativ können Sie Ihre Artefakte mithilfe der Tools und Prozesse, die Sie bereits kennen und verwenden, in Bereitstellungspipelines integrieren. Auf diese Weise können Sie die Bereitstellung mit Ihren eigenen Tools durchführen, unabhängig vom Technologiestapel, den Sie für die Entwicklung verwenden.

Durch die Verwendung von Standardbuild- und -bereitstellungsoptionen können Sie sich unabhängig von der Infrastrukturbereitstellung auf die App-Entwicklung konzentrieren. So erhalten Sie ein allgemeineres Projektmodell, in dem Sie viele ähnliche oder identische Bereitstellungsoptionen wie für eine generische App anwenden können. Außerdem profitieren Sie von einer konsistenteren Umgebung zum Erstellen von Bereitstellungspipelines für Ihre App-Projekte und zum Ausführen der erforderlichen Tests und Überprüfungen vor der Veröffentlichung in der Produktion.

<a name="performance"></a>

### <a name="performance"></a>Leistung

Mit dem Ressourcentyp **Logik-App (Standard)** können Sie mehrere Workflows in derselben Logik-App und demselben Mandanten erstellen und ausführen. Bei dieser 1:n-Zuordnung nutzen diese Workflows Ressourcen wie Compute-, Verarbeitungs-, Speicher- und Netzwerkressourcen gemeinsam und bieten aufgrund ihrer Nähe eine bessere Leistung.

Der Ressourcentyp **Logik-App (Standard)** und die Azure Logic Apps-Runtime für Einzelmandanten bieten einen weiteren erheblichen Vorteil: Sie stellen gängige verwaltete Connectors als integrierte Vorgänge bereit. So stehen Ihnen etwa die folgenden integrierten Vorgänge zur Verfügung: Azure Service Bus, Azure Event Hubs, SQL usw. Die verwalteten Connectorversionen sind weiterhin verfügbar und funktionieren.

Wenn Sie die neuen integrierten Vorgänge verwenden, erstellen Sie Verbindungen, die als *integrierte Verbindungen* oder *Dienstanbieterverbindungen* bezeichnet werden. Die entsprechenden verwalteten Verbindungen werden *API-Verbindungen* genannt. Diese werden separat als Azure-Ressourcen erstellt und ausgeführt, die Sie anschließend auch mithilfe von ARM-Vorlagen bereitstellen müssen. Integrierte Vorgänge und die zugehörigen Verbindungen werden lokal in demselben Prozess ausgeführt wie Ihre Workflows. Beide werden in der Azure Logic Apps-Runtime für den Einzelmandanten gehostet. Aufgrund der Nähe zu Ihren Workflows bieten integrierte Vorgänge und deren Verbindungen eine bessere Leistung. Diese Methode ist auch für Bereitstellungspipelines geeignet, da die Dienstanbieterverbindungen in dasselbe Buildartefakt gepackt werden.

<a name="data-residency"></a>

### <a name="data-residency"></a>Datenresidenz

Logik-App-Ressourcen, die mit dem Ressourcentyp **Logik-App (Standard)** erstellt wurden, werden in einer Azure Logic Apps-Instanz mit nur einem Mandanten gehostet, die [Daten außerhalb der Region, in der Sie diese Logik-App-Ressourcen bereitstellen, nicht speichert, verarbeitet oder repliziert](https://azure.microsoft.com/global-infrastructure/data-residency), was bedeutet, dass Daten in Ihren Logik-App-Workflows in derselben Region bleiben, in der Sie deren übergeordneten Ressourcen erstellen und bereitstellen.

## <a name="create-build-and-deploy-options"></a>Erstellen und Bereitstellen von Optionen

Zum Erstellen einer Logik-App in der von Ihnen gewünschten Umgebung stehen Ihnen beispielsweise folgende Optionen zur Verfügung:

**Umgebung mit einem Mandanten**

| Option | Ressourcen und Tools | Weitere Informationen |
|--------|---------------------|------------------|
| Azure-Portal | Ressourcentyp **Logik-App (Standard)** | [Erstellen von Integrationsworkflows für Azure Logic Apps-Instanzen mit einem Mandanten – Azure-Portal](create-single-tenant-workflows-azure-portal.md) |
| Visual Studio Code | [Erweiterung **Azure Logic Apps (Standard)**](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurelogicapps) | [Erstellen von Integrationsworkflows für Azure Logic Apps-Instanzen mit einem Mandanten – Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) |
| Azure CLI | Erweiterung „Logic Apps Azure CLI“ | Noch nicht verfügbar |
||||

**Umgebungen mit mehreren Mandanten**

| Option | Ressourcen und Tools | Weitere Informationen |
|--------|---------------------|------------------|
| Azure-Portal | Ressourcentyp **Logik-App (Verbrauch)** | [Schnellstart: Erstellen von Integrationsworkflows in Azure Logic Apps-Instanzen mit mehreren Mandanten – Azure-Portal](quickstart-create-first-logic-app-workflow.md) |
| Visual Studio Code | [Erweiterung **Azure Logic Apps (Verbrauch)**](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) | [Schnellstart: Erstellen von Integrationsworkflows in Azure Logic Apps-Instanzen mit mehreren Mandanten – Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
| Azure CLI | [Erweiterung **Logic Apps Azure CLI**](https://github.com/Azure/azure-cli-extensions/tree/master/src/logic) | - [Schnellstart: Erstellen und Verwalten von Integrationsworkflows in Azure Logic Apps-Instanzen mit mehreren Mandanten – Azure CLI](quickstart-logic-apps-azure-cli.md) <p><p>- [az logic](/cli/azure/logic) |
| Azure Resource Manager | [**Erstellen einer** ARM-Vorlage](https://azure.microsoft.com/resources/templates/logic-app-create/) für Logik-App | [Schnellstart: Erstellen und Bereitstellen von Integrationsworkflows in Azure Logic Apps-Instanzen mit mehreren Mandanten – ARM-Vorlage](quickstart-create-deploy-azure-resource-manager-template.md) |
| Azure PowerShell | [Modul „Az.LogicApp“](/powershell/module/az.logicapp) | [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps) |
| Azure-REST-API | [Azure Logic Apps-REST-API](/rest/api/logic) | [Erste Schritte mit der Azure-REST-API-Referenz](/rest/api/azure) |
||||

**Integrationsdienstumgebung**

| Option | Ressourcen und Tools | Weitere Informationen |
|--------|---------------------|------------------|
| Azure-Portal | Ressourcentyp **Logik-App (Verbrauch)** mit vorhandener ISE-Ressource | Identisch mit [Schnellstart: Erstellen von Integrationsworkflows in Azure Logic Apps-Instanzen mit mehreren Mandanten – Azure-Portal](quickstart-create-first-logic-app-workflow.md), aber wählen Sie eine ISE und keine mehrinstanzenfähige Region aus. |
||||

Trotz der Unterschiede bei der Entwicklung einer Logik-App-Ressource vom Typ **Verbrauch** und einer Ressource vom Typ **Standard** können Sie in Ihrem Azure-Abonnement auf alle von Ihnen bereitgestellten Logik-Apps zugreifen.

Im Azure-Portal werden beispielsweise auf der Seite **Logik-Apps** die Ressourcentypen **Verbrauch** und **Standard** angezeigt. In Visual Studio Code werden bereitgestellte Logik-Apps in Ihrem Azure-Abonnement angezeigt. Diese werden jedoch nach der von Ihnen verwendeten Erweiterung gruppiert in **Azure: Logik-Apps (Verbrauch)** und **Azure: Logik-Apps (Standard)** .

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Zustandsbehaftete und zustandslose Workflows

Mit dem Ressourcentyp **Logik-App (Standard)** können Sie die folgenden Workflowtypen in derselben Logik-App erstellen:

* *Zustandsbehaftet*

  Erstellen Sie einen zustandsbehafteten Workflow, wenn Sie Daten von vorherigen Ereignissen beibehalten, überprüfen oder referenzieren müssen. Diese Workflows behalten bei und übertragen sämtliche Ein- und Ausgaben für jede Aktion und deren Zustände im externen Speicher, sodass die Ausführungsdetails und der Ausführungsverlauf nach Abschluss der jeweiligen Ausführung überprüft werden können. Zustandsbehaftete Workflows bieten hohe Resilienz, falls es zu Ausfällen kommen sollte. Nachdem Dienste und Systeme wiederhergestellt wurden, können Sie unterbrochene Ausführungen aus dem gespeicherten Zustand rekonstruieren und die Workflows bis zum Abschluss erneut ausführen. Zustandsbehaftete Workflows können wesentlich länger ausgeführt werden als zustandslose Workflows.

  Standardmäßig laufen zustandsbehaftete Workflows sowohl in multimandantenfähigen als auch in einzelmandantenfähigen Azure Logic Apps asynchron. Alle HTTP-basierten Aktionen folgen dem Standardmuster[ für asynchrone Operationen](/azure/architecture/patterns/async-request-reply). Laut diesem Muster gibt der Empfänger sofort eine [202 ACCEPTED](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3)-Antwort zurück, wenn eine HTTP-Aktion einen Endpunkt, einen Dienst, das System oder die API aufruft oder eine Anforderung an ebendiese sendet. Dieser Code bestätigt, dass der Empfänger die Anforderung akzeptiert, aber die Verarbeitung noch nicht abgeschlossen hat. Die Antwort kann einen `location` Header enthalten, der den URI und eine Refresh-ID angibt, mit der Aufrufer den Status der asynchronen Anforderung abfragen oder überprüfen kann, bis der Empfänger die Verarbeitung beendet und eine ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) Erfolgsmeldung oder eine andere Nicht-202-Antwort zurückgibt. Der Aufrufer muss jedoch nicht darauf warten, dass die Verarbeitung der Anforderung abgeschlossen wird, und kann mit der Ausführung der nächsten Aktion fortfahren. Weitere Informationen finden Sie unter [Gegenüberstellung von synchronem und asynchronem Messaging](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* *Zustandslos*

  Erstellen Sie einen zustandslosen Workflow, wenn Sie Daten von vorherigen Ereignissen nicht zur späteren Überprüfung in externem Speicher sichern, überprüfen oder referenzieren müssen, nachdem jede Ausführung beendet wurde und einer Überprüfung unterzogen werden kann. Diese Workflows speichern sämtliche Ein- und Ausgaben für jede Aktion und deren Zustände nur *im Arbeitsspeicher* und nicht im externen Speicher. Daher sind die Ausführungen zustandsloser Workflows kürzer (in der Regel weniger als 5 Minuten), sie bieten eine höhere Leistung mit schnelleren Antwortzeiten, einen höheren Durchsatz und niedrigere Ausführungskosten, da die Ausführungsdetails und der Ausführungsverlauf nicht im externen Speicher gespeichert werden. Wenn es aber zu Ausfällen kommen sollte, werden unterbrochene Ausführungen nicht automatisch wiederhergestellt, sodass der Aufrufer unterbrochene Ausführungen manuell erneut übermitteln muss.

  > [!IMPORTANT]
  > Ein zustandsloser Workflow bietet die beste Leistung bei der Verarbeitung von Daten oder Inhalten, z. B. einer Datei, die *insgesamt* 64 KB nicht überschreitet. Größere Inhalte, z. B. mehrere große Anlagen, können die Leistung Ihres Workflows erheblich beeinträchtigen oder sogar dazu führen, dass Ihr Workflow aufgrund von Ausnahmen wegen nicht genügend Arbeitsspeicher abstürzt. Wenn Ihr Workflow möglicherweise größere Inhalte verarbeiten muss, verwenden Sie stattdessen einen zustandsbehafteten Workflow.

  Zustandslose Workflows laufen nur synchron ab. Sie verwenden also nicht das standardmäßige [asynchrone Operationsmuster](/azure/architecture/patterns/async-request-reply), das von zustandsabhängigen Workflows verwendet wird. Stattdessen fahren alle HTTP-basierten Aktionen, die eine [„202 ACCEPTED"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3)-Antwort zurückgeben, mit dem nächsten Schritt der Workflow-Ausführung fort. Wenn die Antwort einen `location`Header enthält, wird ein zustandsloser Arbeitsablauf die angegebene URI nicht abfragen, um den Status zu überprüfen. Um dem Standardmuster für asynchrone Operationen zu folgen, verwenden Sie stattdessen einen zustandsabhängigen Workflow.

  Um das Debuggen zu vereinfachen, können Sie den Ausführungsverlauf für einen zustandslosen Workflow aktivieren und den Ausführungsverlauf wieder deaktivieren, wenn Sie fertig sind, da die Aktivierung die Leistung beeinträchtigt. Weitere Informationen finden Sie unter [Erstellen von Workflows für Instanzen mit einem Mandanten in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless) oder [Erstellen von Workflows für Instanzen mit einem Mandanten im Azure-Portal](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Zustandslose Workflows unterstützen derzeit nur *Aktionen* für in Azure bereitgestellte [verwaltete Connectors](../connectors/managed.md), keine Aktionen für Trigger. Um Ihren Workflow zu starten, wählen Sie einen der [Trigger für integrierte Anforderungen, Event Hubs oder Service Bus](../connectors/built-in.md) aus. Diese Trigger werden nativ in der Azure Logic Apps-Runtime ausgeführt. Weitere Informationen zu eingeschränkten, nicht verfügbaren oder nicht unterstützten Triggern, Aktionen und Connectors finden Sie unter [Geänderte, eingeschränkte, nicht verfügbare oder nicht unterstützte Funktionen](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Unterschiede im Verhalten von geschachtelten zustandsbehafteten und geschachtelten zustandslosen Workflows

Sie können einen Workflow so erstellen, dass er aus anderen Workflows heraus, die in derselben Ressource vom Typ **Logik-App (Standard)** enthalten sind, [aufrufbar](logic-apps-http-endpoint.md) ist. Verwenden Sie hierfür den [Anforderungstrigger](../connectors/connectors-native-reqres.md), den [HTTP-Webhooktrigger](../connectors/connectors-native-webhook.md) oder Trigger für verwaltete Connectors vom Typ [ApiConnectionWebhook](logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger), die HTTPS-Anforderungen empfangen können.

Im Folgenden sind die Verhaltensmuster geschachtelter Workflows aufgeführt, nachdem ein übergeordneter Workflow einen untergeordneten Workflow aufruft:

* Asynchrones Abrufmuster

  Der übergeordnete Workflow wartet nicht auf eine Antwort auf seinen ursprünglichen Aufruf, sondern überprüft fortlaufend den Ausführungsverlauf Verlauf des untergeordneten Workflows, bis dessen Ausführung beendet wird. Standardmäßig halten zustandsbehaftete Workflows dieses Muster ein, das sich ideal für untergeordnete Workflows mit langer Laufzeit eignet, die [Anforderungstimeout-Limits](logic-apps-limits-and-config.md) überschreiten können.

* Synchrones Muster („Fire and Forget“)

  Der untergeordnete Workflow bestätigt den Aufruf durch sofortige Rückgabe einer `202 ACCEPTED`-Antwort, und der übergeordnete Workflow fährt mit der nächsten Aktion fort, ohne auf die Ergebnisse des untergeordneten Workflows zu warten. Stattdessen empfängt der übergeordnete Workflow die Ergebnisse, wenn der untergeordnete Workflow abgeschlossen ist. Untergeordnete zustandsbehaftete Workflows, die keine Antwortaktion enthalten, folgen immer dem synchronen Muster. Für untergeordnete zustandsbehaftete Workflows steht Ihnen der Ausführungsverlauf zur Prüfung zur Verfügung.

  Um dieses Verhalten zu aktivieren, legen Sie in der JSON-Definition des Workflows die `operationOptions`-Eigenschaft auf `DisableAsyncPattern` fest. Weitere Informationen finden Sie unter [Trigger- und Aktionstypen: Optionen für Vorgänge](logic-apps-workflow-actions-triggers.md#operation-options).

* Auslösen und Warten

  Bei einem untergeordneten zustandslosen Workflow wartet der übergeordnete Workflow auf eine Antwort, mit der die Ergebnisse vom untergeordneten Workflow zurückgegeben werden. Dieses Muster funktioniert ähnlich wie die Verwendung des integrierten [HTTP-Triggers oder der HTTP-Aktion](../connectors/connectors-native-http.md), um einen untergeordneten Workflow aufzurufen. Untergeordnete zustandslose Workflows, die keine Antwortaktion enthalten, geben sofort eine `202 ACCEPTED`-Antwort zurück, aber der übergeordnete Workflow wartet, bis der untergeordnete Workflow abgeschlossen ist, bevor er mit der nächsten Aktion fortfährt. Diese Verhalten gelten nur für untergeordnete zustandslose Workflows.

Diese Tabelle gibt das Verhalten des untergeordneten Workflows an, je nachdem, ob der übergeordnete und untergeordnete Workflow zustandsbehaftete, zustandslose oder gemischte Workflowtypen sind:

| Übergeordneter Workflow | Untergeordneter Workflow | Untergeordnetes Verhalten |
|-----------------|----------------|----------------|
| Zustandsbehaftet | Zustandsbehaftet | Asynchron oder synchron mit `"operationOptions": "DisableAsyncPattern"`-Einstellung |
| Zustandsbehaftet | Zustandslos | Auslösen und Warten |
| Zustandslos | Zustandsbehaftet | Synchron |
| Zustandslos | Zustandslos | Auslösen und Warten |
||||

<a name="other-capabilities"></a>

## <a name="other-single-tenant-model-capabilities"></a>Weitere Funktionen des Einzelmandantenmodells

Das Einzelmandantenmodell und der Ressourcentyp **Logik-App (Standard)** enthalten viele aktuelle und neue Funktionen, wie beispielsweise:

* Erstellen von Logik-Apps und deren Workflows mit [mehr als 400 verwalteten Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) für SaaS- (Software-as-a-Service) und PaaS-Apps (Platform-as-a-Service) und -Dienste sowie Connectors für lokale Systeme.

  * Weitere verwaltete Connectors sind jetzt als integrierte Vorgänge verfügbar und werden ähnlich wie andere integrierte Vorgänge ausgeführt, wie z. B. Azure Functions. Integrierte Vorgänge werden nativ in der Runtime für Azure Logic Apps-Instanzen mit einem Mandanten ausgeführt. Zu den neuen integrierten Operationen gehören zum Beispiel Azure Service Bus, Azure Event Hubs, SQL Server, MQ, DB2 und IBM Host File.

    > [!NOTE]
    > Für die integrierte SQL Server-Version kann nur die Aktion **Abfrage ausführen** direkt eine Verbindung mit den virtuellen Azure-Netzwerken herstellen, ohne dass das [lokale Datengateway](logic-apps-gateway-connection.md) erforderlich ist.

  * Sie können Ihre eigenen integrierten Connectors für jeden benötigten Dienst erstellen, indem Sie das [Erweiterbarkeitsframework für Azure Logic Apps-Instanzen mit einem Mandanten](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) verwenden. Ähnlich wie integrierte Vorgänge wie Azure Service Bus und SQL Server, jedoch anders als [benutzerdefinierte verwaltete Connectors](../connectors/apis-list.md#custom-apis-and-connectors), die derzeit nicht unterstützt werden, bieten benutzerdefinierte integrierte Connectors einen höheren Durchsatz, niedrige Wartezeiten und lokale Konnektivität, da sie nativ im selben Prozess wie die Runtime für die Instanz mit einem Mandanten ausgeführt werden.

    Die Erstellungsfunktion ist derzeit nur in Visual Studio Code verfügbar, aber nicht standardmäßig aktiviert. Um diese Connectors zu erstellen, [konvertieren Sie Ihr Projekt von erweiterungspaketbasiert (Node.js) in NuGet-Paketbasiert (.NET)](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring). Weitere Informationen finden Sie unter [Azure Logic Apps Running Anywhere: Built-in connector extensibility](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) (Azure Logic Apps läuft überall: Erweiterbarkeit integrierter Connectors).

  * Sie können die folgenden Aktionen für Liquid- und XML-Vorgänge ohne Integrationskonto verwenden. Dazu gehören die folgenden Aktionen:

    * XML: **Transformieren von XML** und **XML-Überprüfung**

    * Liquid: **Json in JSON transformieren**, **JSON in TEXT transformieren**, **XML in JSON transformieren** und **XML in Text transformieren**

    > [!NOTE]
    > Um diese Aktionen in einzelmandantenbasierten Azure Logic Apps (Standard) verwenden zu können, benötigen Sie Liquid-Zuordnungen, XML-Zuordnungen oder XML-Schemas. Sie können diese Artefakte im Azure-Portal über das Ressourcenmenü Ihrer Logik-App unter **Artifacts** hochladen, das die Abschnitte **Schemas** und **Karten** enthält. Sie können diese Artefakte auch dem Ordner **Artifacts** ihres Visual Studio Code-Projekts hinzufügen, indem Sie die entsprechenden Ordner **Karten** und **Schemas** verwenden. Sie können diese Artefakte dann in mehreren Workflows innerhalb *derselben Logik-App-Ressource* verwenden.

  * **Logic App (Standard)** -Ressourcen können überall ausgeführt werden, da Azure Logic Apps SAS-Verbindungszeichenfolgen (Shared Access Signature) generiert, die diese Logic Apps zum Senden von Anforderungen an den Cloud-Verbindungslaufzeitendpunkt verwenden können. Azure Logic Apps speichert diese Verbindungszeichenfolgen zusammen mit anderen Anwendungseinstellungen, sodass Sie diese Werte bei einer Bereitstellung in Azure problemlos in Azure Key Vault speichern können.

    > [!NOTE]
    > Standardmäßig ist für den Ressourcentyp **Logic App (Standard)** die [systemseitig zugewiesene verwaltete Identität](create-managed-service-identity.md) automatisch aktiviert, um Verbindungen zur Laufzeit zu authentifizieren. Diese Identität unterscheidet sich von den Anmeldeinformationen für die Authentifizierung oder der Verbindungszeichenfolge, die Sie verwenden, wenn Sie eine Verbindung herstellen. Wenn Sie diese Identität deaktivieren, funktionieren Verbindungen zur Laufzeit nicht. Wählen Sie zum Anzeigen dieser Einstellung im Menü Ihrer Logik-App unter **Einstellungen** die Option **Identität** aus.
    >
    > Die benutzerseitig zugewiesene verwaltete Identität ist derzeit für den Ressourcentyp **Logic App (Standard)** nicht verfügbar.

* Sie können Ihre Logik-Apps und die zugehörigen Workflows in der Visual Studio Code-Entwicklungsumgebung lokal ausführen, testen und debuggen.

  Bevor Sie Ihre Logik-App ausführen und testen, können Sie das Debuggen vereinfachen, indem Sie in der Datei **workflow.json** für einen Workflow Breakpoints hinzufügen und verwenden. Breakpoints werden derzeit jedoch nur für Aktionen unterstützt, nicht für Trigger. Weitere Informationen finden Sie unter [Erstellen von Workflows für Instanzen mit einem Mandanten in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints).

* Veröffentlichen Sie Ihre Logik-Apps und die zugehörigen Workflows direkt aus Visual Studio Code, oder stellen Sie sie in verschiedenen Hostumgebungen bereit, wie z. B. Azure und durch Azure Arc aktivierte Logic Apps.

* Aktivieren Sie die Funktionen zur Diagnoseprotokollierung und Ablaufverfolgung für Ihre Logik-App, indem Sie [Application Insights](../azure-monitor/app/app-insights-overview.md) verwenden, sofern dies von Ihrem Azure-Abonnement und den Einstellungen der Logik-App unterstützt wird.

* Der [Premium-Tarif für Azure Functions](../azure-functions/functions-premium-plan.md) bietet Zugriff auf Netzwerkfunktionen wie das Herstellen einer privaten Verbindung zu virtuellen Azure-Netzwerken und das Integrieren dieser Netzwerke. Diese Funktionen ähneln den Funktionen in Azure Functions für das Erstellen und Bereitstellen Ihrer Logik-Apps. Weitere Informationen finden Sie in der folgenden Dokumentation:

  * [Netzwerkoptionen von Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps ohne Grenzen: Netzwerkoptionen mit Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Generieren Sie Zugriffsschlüssel für verwaltete Verbindungen erneut, die von einzelnen Workflows in einer Ressource vom Typ **Logik-App (Standard)** verwendet werden. Führen Sie für diese Aufgabe [die gleichen Schritte wie für die Ressource vom Typ **Logik-App (Verbrauch)** aus, jedoch auf der individuellen Workflowebene](logic-apps-securing-a-logic-app.md#regenerate-access-keys) und nicht auf der Ebene der Logik-App-Ressource.

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Geänderte, eingeschränkte, nicht verfügbare oder nicht unterstützte Funktionen

Für Ressourcen vom Typ **Logik-App (Standard)** wurden die folgenden Funktionen geändert, sind zurzeit eingeschränkt oder nicht verfügbar oder werden nicht unterstützt:

* **Auslöser und Aktionen**: Integrierte Auslöser und Aktionen laufen direkt in Azure Logic Apps, während verwaltete Konnektoren in Azure gehostet und ausgeführt werden. Einige integrierte Auslöser und Aktionen sind nicht verfügbar, z. B. Sliding Window, Batch, Azure App Services und Azure API Management. Starten Sie einen zustandsbehafteten oder zustandslosen Workflow mithilfe der [integrierten Trigger „Wiederholung“, „Anforderung“, „HTTP“, „HTTP-Webhook“, „Event Hubs“ oder „Service Bus“](../connectors/apis-list.md). Im Designer werden integrierte Trigger und Aktionen auf der Registerkarte **Integriert** angezeigt.

  Für *zustandsbehaftete* Workflows werden [Trigger und Aktionen für verwaltete Connectors](../connectors/managed.md) auf der Registerkarte **Azure** angezeigt, mit Ausnahme der unten aufgeführten nicht verfügbaren Vorgänge. Bei *zustandslosen* Workflows wird die Registerkarte **Azure** bei der Auswahl eines Triggers nicht angezeigt. Sie können nur [*Aktionen* für verwaltete Connectors auswählen, keine Trigger](../connectors/managed.md). Obwohl Sie in Azure gehostete, verwaltete Connectors für zustandslose Workflows aktivieren können, werden im Designer keine Trigger für verwaltete Connectors angezeigt, die Sie hinzufügen können.

  > [!NOTE]
  > Damit webhookbasierte Trigger und Aktionen lokal in Visual Studio Code ausgeführt werden können, sind zusätzliche Einrichtungsschritte erforderlich. Weitere Informationen finden Sie unter [Erstellen von Workflows für Instanzen mit einem Mandanten in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#webhook-setup).

  * Diese Trigger und Aktionen wurden entweder geändert, sind zurzeit eingeschränkt oder nicht verfügbar oder werden nicht unterstützt:

    * [Lokale Datengateway *trigger*](../connectors/managed.md#on-premises-connectors) sind nicht verfügbar, aber Gatewayaktionen *sind* verfügbar.

    * Die integrierte Aktion [Azure Functions: Azure-Funktion auswählen](logic-apps-azure-functions.md) heißt nun **Azure Functions-Vorgänge: Azure-Funktion aufrufen**. Diese Aktion funktioniert derzeit nur für Funktionen, die über die Vorlage für **HTTP-Trigger** erstellt werden.

      Sie können im Azure-Portal eine HTTP-Triggerfunktion auswählen, auf die Sie Zugriff haben, indem Sie über die Benutzeroberfläche eine Verbindung erstellen. Wenn Sie die JSON-Definition der Funktionsaktion in der Codeansicht oder der Datei **workflow.json** mithilfe von Visual Studio Code überprüfen, verweist die Aktion über einen `connectionName`-Verweis auf die Funktion. Diese Version abstrahiert die Informationen der Funktion als Verbindung, die Sie in der Datei **connections.json** des Projekts Ihrer Logik-App finden, die nach dem Erstellen einer Verbindung in Visual Studio Code verfügbar ist.

      > [!NOTE]
      > Im Einzelmandantenmodell unterstützt die Funktionsaktion nur die Authentifizierung über Abfragezeichenfolgen. Azure Logic Apps ruft den Standardschlüssel beim Herstellen der Verbindung aus der Funktion ab, speichert ihn in den Einstellungen Ihrer App und verwendet ihn beim Aufrufen der Funktion für die Authentifizierung.
      >
      > Wie im mehrinstanzenfähigen Modell gilt auch hier: Wenn Sie diesen Schlüssel erneuern (z. B. über die Azure Functions-Benutzeroberfläche im Portal), funktioniert die Funktionsaktion aufgrund eines ungültigen Schlüssels nicht mehr. Um dieses Problem zu beheben, müssen Sie die Verbindung mit der Funktion, die Sie aufrufen möchten, neu erstellen oder die App-Einstellungen mit dem neuen Schlüssel aktualisieren.

    * Der Name der integrierten Aktion [Inline-Code](logic-apps-add-run-inline-code.md) wird in **Inlinecodevorgänge** geändert, es ist kein Integrationskonto mehr erforderlich, und die [Grenzwerte wurden aktualisiert](logic-apps-limits-and-config.md).

    * Die integrierte Aktion [Azure Logic Apps: Logik-App-Workflow auswählen](logic-apps-http-endpoint.md) heißt nun **Workflowvorgänge: Workflow in dieser Workflow-App aufrufen**.

    * Einige [Auslöser und Aktionen für Integrationskonten](../connectors/managed.md#integration-account-connectors) sind nicht verfügbar, z. B. die AS2 (V2)-Aktionen und RosettaNet-Aktionen.

    * [Benutzerdefinierte verwaltete Connectors](../connectors/apis-list.md#custom-apis-and-connectors) werden derzeit nicht unterstützt. Mit Visual Studio Code können Sie jedoch *benutzerdefinierte integrierte Vorgänge* erstellen. Weitere Informationen finden Sie unter [Erstellen von Workflows für Instanzen mit einem Mandanten in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring).

* **Authentifizierung**: Die folgenden Authentifizierungstypen sind derzeit für den Ressourcentyp **Logic App (Standard)** nicht verfügbar:

  * Azure Active Directory Open Authentication (Azure AD OAuth) für eingehende Anrufe an anfragebasierte Auslöser, wie den Anfrageauslöser und den HTTP-Webhook-Auslöser.

  * Dem Benutzer zugewiesene verwaltete Identität. Derzeit ist nur die vom System zugewiesene verwaltete Identität verfügbar und automatisch aktiviert.

* **XML-Transformation**: Unterstützung für die Referenzierung von Zusammenstellungen aus Zuordnungen ist derzeit nicht verfügbar. Außerdem wird derzeit nur XSLT 1.0 unterstützt.

* **Breakpoints für das Debuggen in Visual Studio Code:** Sie können zwar in der Datei **workflow.json** Breakpoints für einen Workflow hinzufügen und verwenden, aber diese Breakpoints werden derzeit nur für Aktionen unterstützt, nicht für Trigger. Weitere Informationen finden Sie unter [Erstellen von Workflows für Instanzen mit einem Mandanten in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints).

* **Trigger- und Ausführungsverlauf**: Für den Ressourcentyp **Logik-App (Standard)** werden der Trigger- und der Ausführungsverlauf im Azure-Portal auf Workflowebene angezeigt, jedoch nicht auf der Ebene der Logik-App. Weitere Informationen finden Sie unter [Erstellen von Workflows für Instanzen mit einem Mandanten im Azure-Portal](create-single-tenant-workflows-azure-portal.md).

* **Zoomsteuerelement:** Das Zoomsteuerelement ist im Designer derzeit nicht verfügbar.

* **Bereitstellungsziele**: Sie können den Ressourcentyp **Logik-App (Standard)** weder in einer [Integrationsdienstumgebung (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) noch in Azure-Bereitstellungsslots bereitstellen.

* **Azure API Management**: Sie können den Ressourcentyp **Logik-App (Standard)** derzeit nicht in Azure API Management importieren. Sie können jedoch den Ressourcentyp **Logik-App (Verbrauch)** importieren.

<a name="firewall-permissions"></a>

## <a name="strict-network-and-firewall-traffic-permissions"></a>Strenge Berechtigungen für Netzwerk- und Firewalldatenverkehr

Wenn in Ihrer Umgebung strenge Netzwerkanforderungen gelten oder Firewalls vorhanden sind, die den Datenverkehr einschränken, müssen Sie den Zugriff für alle Trigger- oder Aktionsverbindungen in Ihren Logik-App-Workflows zulassen. Informationen zum Auffinden der vollqualifizierten Domänennamen (FQDNs) für diese Verbindungen finden Sie in den entsprechenden Abschnitten in den folgenden Themen:

* [Firewallberechtigungen für Logik-Apps für einzelinstanzenfähige Logik-Apps – Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)
* [Firewallberechtigungen für Logik-Apps für einzelinstanzenfähige Logik-Apps – Azure-Portal](create-single-tenant-workflows-azure-portal.md#firewall-setup)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Workflows für Instanzen mit einem Mandanten im Azure-Portal](create-single-tenant-workflows-azure-portal.md)
* [Erstellen von Workflows für Instanzen mit einem Mandanten in Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md)

Teilen Sie uns gerne Ihre Erfahrungen mit den Azure Logic Apps-Instanzen mit einem Mandanten mit.

* Bei Fehlern oder Problemen [erstellen Sie Ihre Probleme in GitHub](https://github.com/Azure/logicapps/issues).
* Bei Fragen, Anforderungen, Kommentaren und anderem Feedback [verwenden Sie dieses Feedbackformular](https://aka.ms/lafeedback).
