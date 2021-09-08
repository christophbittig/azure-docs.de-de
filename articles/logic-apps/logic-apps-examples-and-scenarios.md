---
title: Beispiele und häufige Szenarios
description: Hier finden Sie Beispiele, allgemeine Szenarien, Tutorials und exemplarische Vorgehensweisen für Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: 4d10cc4dab0b390ce5c22996a5f0a8bc5174440e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449243"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Gängige Szenarien, Beispiele, Tutorials und exemplarische Vorgehensweisen für Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) unterstützt Sie bei der Orchestrierung und Integration verschiedener Dienste durch die Bereitstellung von [Hunderten einsatzbereiten Connectors](../connectors/apis-list.md) – von der SQL Server-Instanz über SAP bis hin zu Azure Cognitive Services. Der Logic Apps-Dienst arbeitet „serverlos“. Daher müssen Sie sich über Skalierung oder Instanzen keine Gedanken machen. Sie brauchen lediglich den Workflow mit einem Trigger und Aktionen definieren, die im Workflow ausgeführt werden. Die zugrundeliegende Plattform kümmert sich um Skalierung, Verfügbarkeit und Leistung. Logic Apps ist besonders nützlich für Anwendungsfälle und Szenarien, in denen Sie Aktionen über verschiedene Systeme und Dienste hinweg koordinieren müssen.

Um Informationen über die Funktionen und Muster zu erhalten, die von Azure Logic Apps unterstützt werden, beschreibt dieser Artikel gängige Ausgangspunkte, Beispiele und Szenarien.

## <a name="common-starting-points-for-logic-app-workflows"></a>Gängige Ausgangspunkte für Logik-App-Workflows

Jede Logik-App beginnt mit nur einem [*Trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts), der Ihren Logik-App-Workflow startet und alle Daten als Teil dieses Triggers übergibt. Einige Connectors bieten Trigger, die einen der folgenden Typen haben:

* *Abfragetrigger*: Fragt einen Dienstendpunkt regelmäßig auf neue Daten ab. Wenn neue Daten vorhanden sind, wird der Trigger erstellt, der eine neue Workflowinstanz mit den Daten als Eingabe ausführt.

* *Pushtrigger*: Lauscht auf Daten am Dienstendpunkt und wartet, bis ein bestimmtes Ereignis erfolgt. Wenn das Ereignis eintritt, wird der Trigger sofort ausgelöst. Eine neue Workflowinstanz wird angelegt und ausgeführt, die alle verfügbaren Daten als Eingabe verwendet.

In diesen Beispielen werden häufig verwendete Trigger beschrieben:

* *Abfrage* trigger:

  * Der [Trigger **Wiederholung**](../connectors/connectors-native-recurrence.md) ermöglicht Ihnen das Festlegen von Startdatum und -uhrzeit sowie der Wiederholung zum Auslösen Ihrer Logik-App. Beispielsweise können Sie die Wochentage und Tageszeiten auswählen, um Ihre Logik-App auszulösen. Weitere Informationen finden Sie in den folgenden Themen:<p>

    * [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Tutorial: Erstellen automatisierter, zeitplanbasierter periodischer Workflows mithilfe von Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Mit dem Trigger **Wenn eine E-Mail empfangen wird** können Sie Ihre Logik-App von jedem E-Mail-Anbieter, der von Logic Apps unterstützt wird, z. B. [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/) usw., auf neue E-Mails überprüfen.

    > [!IMPORTANT]
    > Wenn Sie den Gmail-Connector verwenden möchten, können nur G-Suite-Geschäftskonten diesen Connector ohne Einschränkung in Logik-Apps verwenden. Wenn Sie über ein Gmail-Consumerkonto verfügen, können Sie diesen Connector nur mit bestimmten von Google genehmigten Diensten verwenden, oder Sie können [eine Google-Client-App erstellen, die für die Authentifizierung mit Ihrem Gmail-Connector verwendet werden soll](/connectors/gmail/#authentication-and-bring-your-own-application). Weitere Informationen finden Sie unter [Datensicherheit und Datenschutzrichtlinien für Google-Connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

    Weitere Informationen finden Sie in den folgenden Themen:<p>

    * [Tutorial: Erstellen automatisierter genehmigungsbasierter Workflows mithilfe von Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Tutorial: Automatisieren von Aufgaben zur Verarbeitung von E-Mails mithilfe von Azure Logic Apps, Azure Functions und Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Der [**HTTP**-Trigger](../connectors/connectors-native-http.md) kann einen Dienstendpunkt über HTTP oder HTTPS aufrufen. Weitere Informationen finden Sie unter [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten ](../logic-apps/logic-apps-http-endpoint.md).

* *Push* trigger:

  * Der [**Anforderungs** trigger](../connectors/connectors-native-reqres.md) kann eingehende HTTPS-Anforderungen empfangen.

  * Der Trigger [**HTTP-Webhook**](../connectors/connectors-native-webhook.md) abonniert einen Dienstendpunkt durch Registrieren einer *Rückruf-URL* beim jeweiligen Dienst. Auf diese Weise kann der Dienst den Trigger einfach benachrichtigen, wenn das angegebene Ereignis eintritt, sodass der Trigger den Dienst nicht mehr abfragen muss.

Nach dem Eintreten des angegebenen Ereignisses wird der Trigger ausgelöst und erzeugt eine neue Logik-App-Workflowinstanz und führt die Aktionen in dem Workflow aus. Sie können im gesamten Workflow auf beliebige Daten im Trigger zugreifen. Der Trigger **Bei einem neuen Tweet** übergibt z. B. den Tweetinhalt an die Ausführung der Logik-App. Probieren Sie für den Einstieg in Azure Logic Apps die folgenden Schnellstartthemen aus:

* [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Schnellstart: Erstellen von automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Schnellstart: Erstellen und Verwalten automatisierter Logik-App-Workflows mit Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Funktionen für Ablaufsteuerung und Fehlerbehandlung

Logik-Apps enthalten umfangreiche Funktionen für die erweiterte Ablaufsteuerung, wie Bedingungen, Schalter, Schleifen und Bereiche. Um robuste Lösungen sicherzustellen, können Sie darüber hinaus in Ihren Workflows Fehler- und Ausnahmebehandlung implementieren.

* Durchführen verschiedener Aktionen basierend auf [Bedingungsanweisungen](../logic-apps/logic-apps-control-flow-conditional-statement.md) und [Wechseln von Anweisungen](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Wiederholen von Schritten oder Verarbeiten von Elementen in Arrays und Sammlungen mit Schleifen](../logic-apps/logic-apps-control-flow-loops.md)
* [Gruppieren von Aktionen im Zusammenhang mit Bereichen](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Hinzufügen von Fehler- und Ausnahmebehandlung zu einem Workflow](../logic-apps/logic-apps-exception-handling.md)
* [Anwendungsfall: Wie ein Unternehmen im Gesundheitswesen Logic Apps-Ausnahmebehandlung für HL7 FHIR-Workflows einsetzt](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Erstellen benutzerdefinierter APIs und Connectors

Für Systeme und Dienste, für die keine veröffentlichten Connectors verfügbar sind, können Sie Logik-Apps darüber hinaus erweitern.

* [Erstellen benutzerdefinierter APIs zum Aufrufen aus Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Regelmäßiges Überprüfen auf neue Daten oder Ereignisse mit dem Abruftriggermuster](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Warten und Lauschen auf neue Daten oder Ereignisse mit dem Webhooktriggermuster](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Ausführen zeitintensiver Aufgaben mit dem Abrufaktionsmuster](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Ausführen zeitaufwändiger Aufgaben mit dem Webhookaktionsmuster](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Benutzerdefinierte Connectors in Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Erstellen von Business-to-Business-Lösungen (B2B)

Für Enterprise Integration-Lösungen und eine nahtlose Kommunikation zwischen Organisationen können Sie mithilfe des Enterprise Integration Packs (EIP) mit Azure Logic Apps automatisierte skalierbare Workflows für diese Szenarien erstellen. Organisationen können Nachrichten elektronisch austauschen, auch wenn sie unterschiedliche Protokolle und Formate verwenden. Das EIP wandelt verschiedene Formate in ein Format um, das von den Systemen Ihrer Organisation verarbeitet werden kann, und unterstützt branchenübliche Protokolle, einschließlich AS2, X12, EDIFACT und RosettaNet. Um diese Lösungen zu erstellen, erstellen Sie ein Integrationskonto. Dabei handelt es sich um eine separate Azure-Ressource, die einen sicheren, skalierbaren und verwaltungsfreundlichen Container für die Artefakte, die Sie mit Ihren Logik-App-Workflows definieren und verwenden. Artefakte umfassen beispielsweise Handelspartner, Vereinbarungen, Zuordnungen, Schemas, Zertifikate und Batchkonfigurationen.

* [Übersicht: B2B-Unternehmensintegrationslösungen mit Azure Logic Apps und dem Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Erstellen und Verwalten von Integrationskonten für die B2B-Unternehmensintegration in Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Zugreifen auf virtuelle Azure-Netzwerkressourcen

Manchmal benötigen Ihre Logik-Apps und Integrationskonten Zugriff auf gesicherte Ressourcen wie virtuelle Computer (VMs) und andere Systeme oder Dienste in einem virtuellen Azure-Netzwerk. Wenn Sie diesen Zugriff einrichten möchten, können Sie eine Integrationsdienstumgebung (Integration Service Environment, ISE) zum Erstellen und Ausführen Ihrer Logik-Apps erstellen. Eine ISE ist eine private und isolierte Instanz des Logic Apps-Diensts, die dedizierte Ressourcen wie Speicher verwendet und getrennt vom öffentlichen, „globalen“, mehrinstanzenfähigen Logic Apps-Dienst ausgeführt wird. Die Trennung Ihrer isolierten privaten Instanz von der öffentlichen globalen Instanz trägt auch dazu bei, die Auswirkungen zu verringern, die andere Azure-Mandanten auf die Leistung Ihrer Apps haben könnten. Hierbei spricht man auch vom „Noisy-Neighbor“-Problem.

* [Übersicht: Zugriff auf virtuelle Azure-Netzwerkressourcen aus Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Herstellen einer Verbindung mit virtuellen Azure-Netzwerken über Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Bereitstellen, Verwalten und Überwachen von Logik-Apps

Sie können Logik-Apps mithilfe von Visual Studio, Azure DevOps oder beliebigen anderen Tools für die Quellcodeverwaltung und automatische Builderstellung vollständig entwickeln und bereitstellen. Um die Entwicklung für Workflows und abhängige Verbindungen in einer Ressourcenvorlage zu unterstützen, verwendet Logic Apps Azure-Vorlagen für die Ressourcenbereitstellung. Visual Studio-Tools generieren diese Vorlagen automatisch, die Sie zur Versionsverwaltung in die Quellcodeverwaltung einchecken können. Für Benachrichtigungs- und Diagnoseprotokolle für den Status von Workflowausführungen stellt Azure Logic Apps ferner Überwachung und Warnungen zur Verfügung.

### <a name="deploy"></a>Bereitstellen

* [Schnellstart: Erstellen von automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Übersicht: Automatisieren der Logik-App-Bereitstellung](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Erstellen von Azure Resource Manager-Vorlagen, um die Bereitstellung für Azure Logic Apps zu automatisieren](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Bereitstellen von Azure Resource Manager-Vorlagen für Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Beispiel: Einrichten einer API Management-Aktion für Azure Logic Apps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/api-management-actions)
* [Beispiel: Orchestrieren von Azure Pipelines mithilfe von Azure Logic Apps](https://github.com/Azure-Samples/azure-logic-apps-pipeline-orchestration)
* [Beispiel: Herstellen einer Verbindung mit Azure-Speicherkonten aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/storage-account-connections)
* [Beispiel: Herstellen einer Verbindung mit Azure Service Bus-Warteschlangen aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/service-bus-connections)
* [Beispiel: Einrichten einer Azure Functions-Aktion für Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/function-app-actions)
* [Beispiel: Herstellen einer Verbindung mit einem Integrationskonto aus Azure Logic Apps und Bereitstellen mit Azure Pipelines in Azure DevOps](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/integration-account-connections)

### <a name="manage"></a>Verwalten

* [Verwalten von Logik-Apps mit Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Erstellen und Verwalten von Integrationskonten für die B2B-Unternehmensintegration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Verwalten einer Integrationsdienstumgebung (Integration Service Environment, ISE) in Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Überwachen

* [Überwachen des Ausführungsstatus, Überprüfen des Triggerverlaufs und Einrichten von Benachrichtigungen für Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Einrichten von Azure Monitor-Protokollen und Sammeln von Diagnosedaten für Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Einrichten von Azure Monitor-Protokollen und Sammeln von Diagnosedaten für B2B-Nachrichten in Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Anzeigen und Erstellen von Abfragen zur Überwachung und Nachverfolgung in Azure Monitor-Protokolle für Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Behandeln von Inhaltstypen, Konvertierungen und Transformationen

Mithilfe der vielen Funktionen in der [Workflowdefinitionssprache](./logic-apps-workflow-definition-language.md) von Azure Logic Apps können Sie auf mehrere Inhaltstypen zugreifen, sie konvertieren und transformieren. Beispielsweise können Sie mit den Workflowausdrücken `@json()` und `@xml()` zwischen einer Zeichenfolge, JSON und XML konvertieren. Die Logic Apps-Engine behält Inhaltstypen bei, um die verlustfreie Übertragung von Inhalten zwischen Diensten zu unterstützen.

* [Behandeln von Inhaltstypen in Azure Logic Apps](../logic-apps/logic-apps-content-type.md) wie `application/`, `application/octet-stream` und `multipart/formdata`
* [Referenzhandbuch für die Verwendung von Funktionen in Ausdrücken für Azure Logic Apps und Power Automate](../logic-apps/workflow-definition-language-functions-reference.md)
* [Schema der Definitionssprache für Workflows für Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Weitere Integrationen und Funktionen

Azure Logic Apps integriert sich in viele Dienste, wie etwa Azure Functions, Azure API Management, Azure App Services und benutzerdefinierte HTTP-Endpunkte, z. B. REST und SOAP.

* [Aufrufen von Azure Functions aus Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: Aufrufen oder Auslösen von Logik-Apps mithilfe von Azure Functions und Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: Erstellen eines Streaming-Customer Insights-Dashboards mit Azure Logic Apps und Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Tutorial: Erstellen einer Funktion, die in Azure Logic Apps integriert ist](../azure-functions/functions-twitter-email.md)
* [Tutorial: Erstellen eines KI-gestützten sozialen Dashboards mithilfe von Power BI und Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Tutorial: Überwachen von Änderungen an virtuellen Computern mit Azure Event Grid und Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: IoT-Remoteüberwachung und -Benachrichtigungen mit Azure Logic Apps zum Herstellen einer Verbindung zwischen Ihrem IoT Hub und Postfach](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Aufrufen von SOAP-Diensten mit Azure Logic Apps](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Komplettszenarien

* [Whitepaper: End-to-end case management integration in the utilities industry with Azure services (Integration der nahtlosen Fallverwaltung in der Versorgungswirtschaft mit Azure-Diensten)](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Kundenstimmen

Erfahren Sie, wie Azure Logic Apps sowie andere Azure-Dienste und Microsoft-Produkte [diesen Unternehmen](https://aka.ms/logic-apps-customer-stories) geholfen haben, durch Vereinfachen, Organisieren, Automatisieren und Orchestrieren komplexer Prozesse ihre Flexibilität zu verbessern und sich auf ihr Kerngeschäft zu konzentrieren.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Connectors für Logic Apps](../connectors/apis-list.md).
* Erfahren Sie mehr über [Szenarien für die B2B-Unternehmensintegration mit Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md).