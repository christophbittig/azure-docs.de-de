---
title: Integrierte Trigger und Aktionen für Azure Logic Apps
description: Verwenden Sie integrierte Trigger und Aktionen, um automatisierte Workflows zu erstellen, die Apps, Daten, Dienste und Systeme integrieren, Workflows zu steuern sowie Daten mithilfe von Azure Logic Apps zu verwalten.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: c98263500651447ddc4a9962f2e1ac10d9927502
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664933"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Integrierte Trigger und Aktionen für Azure Logic Apps

Mithilfe von [integrierten Triggern und Aktionen](apis-list.md) können Sie den [Zeitplan und die Struktur Ihres Workflows steuern](#control-workflow), [eigenen Code ausführen](#run-code-from-workflows), [Daten verwalten oder bearbeiten](#manage-or-manipulate-data) und andere Aufgaben in Ihren Workflows ausführen. Anders als [verwaltete Connectors](managed.md) sind viele integrierte Vorgänge nicht an einen bestimmten Dienst, ein System oder ein Protokoll gebunden. Beispielsweise können Sie mithilfe des Triggers „Serie“ fast jeden Workflow basierend auf einem Zeitplan starten. Sie können auch den Trigger „Anforderung“ verwenden, damit Ihr Workflow erst gestartet wird, wenn er aufgerufen wird. Alle integrierten Vorgänge werden nativ im Logic Apps-Dienst ausgeführt, und die meisten dieser Vorgänge erfordern nicht, dass Sie vor ihrer Verwendung eine Verbindung erstellen.

Für eine kleinere Anzahl von Diensten, Systemen und Protokollen, wie z. B. Azure Service Bus, Azure Functions, Azure Blob, Azure App Services, Azure API Management, SQL, AS2 usw. bietet Logic Apps auch integrierte Vorgänge. Die Anzahl und der Bereich variieren abhängig davon, ob Sie eine Logik-App mit mehreren oder einem einzigen Mandanten erstellen. In einigen Fällen sind sowohl eine integrierte Version als auch eine verwaltete Connectorversion verfügbar. In den meisten Fällen bietet die integrierte Version eine höhere Leistung, bessere Funktionen, niedrigere Preise usw. Zum [Austauschen von B2B-Nachrichten mithilfe des AS2-Protokolls](../logic-apps/logic-apps-enterprise-integration-as2.md) sollten Sie beispielsweise die integrierte Version auswählen, es sei denn, Sie benötigen Nachverfolgungsfunktionen, die nur in der (veralteten) verwalteten Connectorversion verfügbar sind.

Die Anzahl und der Bereich variieren abhängig davon, ob Sie eine Logik-App mit mehreren oder einem einzigen Mandanten erstellen. In einigen Fällen sind sowohl eine integrierte Version als auch eine verwaltete Connectorversion verfügbar. In den meisten Fällen bietet die integrierte Version eine höhere Leistung, bessere Funktionen, niedrigere Preise usw. Zum [Austauschen von B2B-Nachrichten mithilfe des AS2-Protokolls](../logic-apps/logic-apps-enterprise-integration-as2.md) sollten Sie beispielsweise die integrierte Version auswählen, es sei denn, Sie benötigen Nachverfolgungsfunktionen, die nur in der (veralteten) verwalteten Connectorversion verfügbar sind.


Wenn Sie beispielsweise eine Logik-App mit nur einem Mandanten erstellen, stehen integrierte Vorgänge für Azure Service Bus, Azure Event Hubs, SQL Server und MQ zur Verfügung. In einigen Fällen sind sowohl eine integrierte Version als auch eine verwaltete Connectorversion verfügbar. In den meisten Fällen bietet die integrierte Version eine höhere Leistung, bessere Funktionen, niedrigere Preise usw. Zum [Austauschen von B2B-Nachrichten mithilfe des AS2-Protokolls](../logic-apps/logic-apps-enterprise-integration-as2.md) sollten Sie beispielsweise die integrierte Version auswählen, es sei denn, Sie benötigen Nachverfolgungsfunktionen, die nur in der (veralteten) verwalteten Connectorversion verfügbar sind.

In der folgenden Liste werden nur einige der Aufgaben beschrieben, die Sie mit [integrierten Triggern und Aktionen](#understand-triggers-and-actions) ausführen können:

- Ausführen von Workflows mit benutzerdefinierten und erweiterten Zeitplänen. Weitere Informationen zur Planung finden Sie im [Abschnitt zum Wiederholungsverhalten in der Übersicht über Logic Apps-Connectors](apis-list.md#recurrence-behavior).
- Organisieren und Steuern der Struktur des Workflows, z. B. mit Schleifen und Bedingungen.
- Verwenden von Variablen, Datumsangaben, Datenvorgängen, Inhaltstransformationen und Batchvorgängen.
- Kommunizieren mit anderen Endpunkten über HTTP-Trigger und -Aktionen.
- Empfangen von und Reagieren auf Anforderungen.
- Aufrufen eigener Funktionen (Azure Functions), Web-Apps (Azure App Services), APIs (Azure API Management) sowie anderer Logic Apps-Workflows, die Anforderungen empfangen können usw.

## <a name="understand-triggers-and-actions"></a>Grundlegendes zu Triggern und Aktionen

Logic Apps bietet die folgenden integrierten Trigger und Aktionen:

:::row:::
    :::column:::
        [![Symbol „Zeitplan“ in Logic Apps][schedule-icon]][schedule-doc]
        \
        \
        [**Zeitplan**][schedule-doc]
        \
        \
        [**Serie**][schedule-recurrence-doc]: Auslösen eines Workflows auf Grundlage der angegebenen Serie.
        \
        \
        [**Gleitendes Fenster**][schedule-sliding-window-doc]: Auslösen eines Workflows, der Daten in kontinuierlichen Blöcken verarbeiten muss.
        \
        \
        [**Verzögerung**][schedule-delay-doc]: Anhalten des Workflows für die angegebene Dauer.
        \
        \
        [**Verzögern bis**][schedule-delay-until-doc]: Anhalten des Workflows bis zur angegebenen Zeit (Datum und Uhrzeit).
    :::column-end:::
    :::column:::
        [![Symbol „HTTP“ in Logic Apps][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Aufrufen eines HTTP- oder HTTPS-Endpunkts mit dem HTTP-Trigger oder der HTTP-Aktion. 
        \
        \
        Zudem können Sie die folgenden anderen integrierten HTTP-Trigger und -Aktionen verwenden: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Symbol „Anforderung“][http-request-icon]][http-request-doc]
        \
        \
        [**Anforderung**][http-request-doc]
        \
        \
        [**Beim Empfang einer HTTP-Anforderung**][http-request-doc]: Warten auf eine Anforderung von einem anderen Workflow, einer anderen App oder einem anderen Dienst. Mit diesem Trigger kann Ihr Workflow aufgerufen werden, ohne basierend auf einem Zeitplan überprüft oder abgefragt werden zu müssen.
        \
        \
        [**Antwort**][http-request-doc]: Antworten auf eine Anforderung, die vom Trigger **Beim Empfang einer HTTP-Anforderung** im gleichen Workflow empfangen wurde.
    :::column-end:::
    :::column:::
        [![Symbol „Batch“ in Logic Apps][batch-icon]][batch-doc]
        \
        \
        [**Batch**][batch-doc]
        \
        \
        [**Batchnachrichten**][batch-doc]: Auslösen eines Workflows, der Nachrichten in Batches verarbeitet.
        \
        \
        [**Nachrichten an Batch senden**][batch-doc]: Aufrufen eines vorhandenen Workflows, der aktuell mit einem Trigger **Batchnachrichten** gestartet wird.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Symbol „Azure API Management“ in Logic Apps][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        Aufrufen eigener Trigger und Aktionen in APIs, die Sie mit [Azure API Management](../api-management/api-management-key-concepts.md) definieren, verwalten und veröffentlichen. <p><p>**Hinweis:** Wird bei Verwendung des [Tarifs „Consumption“ für API Management](../api-management/api-management-features.md) nicht unterstützt.
    :::column-end:::
    :::column:::
        [![Symbol „Azure App Services“ in Logic Apps][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        Aufrufen von Apps, die Sie in [Azure App Service](../app-service/overview.md) erstellen und hosten, z. B. API-Apps und Web-Apps.
        \
        \
        Bei der Verwendung von Swagger werden die in diesen Apps definierten Trigger und Aktionen wie alle anderen erstklassigen Trigger und Aktionen in Azure Logic Apps angezeigt.
    :::column-end:::
    :::column:::
        [![Symbol „Azure Logic Apps“ in Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Aufrufen anderer Workflows, die mit dem Anforderungstrigger **Beim Empfang einer HTTP-Anforderung** gestartet werden.
    :::column-end:::
    :::column:::
        [![SQL Server-Symbol in Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc] <br>(*nur Einzelmandanten*)     \
        \
        Herstellen einer Verbindung mit Ihrer lokalen SQL Server-Instanz oder einer Azure SQL-Datenbank in der Cloud, sodass Sie Datensätze verwalten, gespeicherte Prozeduren ausführen oder Abfragen ausführen können. <p>**Hinweis**: Azure Logic Apps für Einzelmandanten bietet sowohl SQL-integrierte Vorgänge als auch Vorgänge des verwalteten Connectors, während mehrinstanzenfähiges Azure Logic Apps nur Vorgänge des verwalteten Connectors bietet. <p>Weitere Informationen finden Sie unter [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](../logic-apps/single-tenant-overview-compare.md).
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Ausführen von Code über Workflows

Logic Apps bietet integrierte Aktionen, mit denen Sie Ihren eigenen Code im Workflow ausführen können:

:::row:::
    :::column:::
        [![Symbol „Azure Functions“ in Logic Apps][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Aufrufen von [in Azure gehosteten Funktionen](../azure-functions/functions-overview.md), um eigene *Codeausschnitte* (C# oder Node.js) im Workflow auszuführen.
    :::column-end:::
    :::column:::
        [![Symbol „Inlinecode“ in Logic Apps][inline-code-icon]][inline-code-doc]
        \
        \
        [**Inlinecode**][inline-code-doc]
        \
        \
        [**JavaScript-Code ausführen**][inline-code-doc]: Hinzufügen und Ausführen eigener JavaScript-*Inlinecodeausschnitte* im Workflow.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Steuern des Workflows

Logic Apps bietet integrierte Aktionen zum Strukturieren und Steuern der Aktionen in Ihrem Workflow:

:::row:::
    :::column:::
        [![Aktionssymbol „Bedingung“ in Logic Apps][condition-icon]][condition-doc]
        \
        \
        [**Bedingung**][condition-doc]
        \
        \
        Auswerten einer Bedingung und Ausführen unterschiedlicher Aktionen abhängig davon, ob die Bedingung TRUE oder FALSE ist.
    :::column-end:::
    :::column:::
        [![Aktionssymbol „For Each“ in Logic Apps][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        Ausführen der gleichen Aktionen für jedes Element in einem Array.
    :::column-end:::
    :::column:::
        [![Aktionssymbol „Bereich“ in Logic Apps][scope-icon]][scope-doc]
        \
        \
        [**Name**][scope-doc]
        \
        \
        Gruppieren von Aktionen in *Bereiche*, die einen eigenen Status erhalten, nachdem die Ausführung der Aktionen im Bereich beendet ist.
    :::column-end:::
    :::column:::
        [![Aktionssymbol „Umschalten“ in Logic Apps][switch-icon]][switch-doc]
        \
        \
        [**Umschalten**][switch-doc]
        \
        \
        Gruppieren von Aktionen in *Fälle*, denen mit Ausnahme des Standardfalls eindeutige Werte zugewiesen werden. Es wird nur der Fall ausgeführt, dessen zugewiesener Wert mit dem Ergebnis eines Ausdrucks, Objekts oder Token übereinstimmt. Wenn keine Übereinstimmungen vorhanden sind, wird der Standardfall ausgeführt.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Aktionssymbol „Beenden“ in Logic Apps][terminate-icon]][terminate-doc]
        \
        \
        [**Beenden**][terminate-doc]
        \
        \
        Beenden des aktiv ausgeführten Workflows einer Logik-App. 
    :::column-end:::
    :::column:::
        [![Aktionssymbol „Bis“ in Logic Apps][until-icon]][until-doc]
        \
        \
        [**Bis**][until-doc]
        \
        \
        Wiederholen von Aktionen, bis die angegebene Bedingung TRUE ist oder sich ein Status geändert hat.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Verwalten oder Bearbeiten von Daten

Logic Apps bietet integrierte Aktionen zur Verwendung von Datenausgaben mit den zugehörigen Formaten:

:::row:::
    :::column:::
        [![Aktionssymbol „Datenvorgänge“ in Logic Apps][data-operations-icon]][data-operations-doc]
        \
        \
        [**Datenvorgänge**][data-operations-doc]
        \
        \
        Ausführen von Vorgängen mit Daten. 
        \
        \
        **Erstellen**: Erstellen einer einzelnen Ausgabe aus mehreren Eingaben mit verschiedenen Typen. 
        \
        \
        **CSV-Tabelle erstellen**: Erstellen einer Tabelle mit durch Komma getrennten Werten (CSV-Tabelle) aus einem Array mit JSON-Objekten. 
        \
        \
        **HTML-Tabelle erstellen**: Erstellen einer HTML-Tabelle aus einem Array mit JSON-Objekten. 
        \
        \
        **Array filtern**: Erstellen eines Arrays aus Elementen in einem anderen Array, die Ihren Kriterien entsprechen. 
        \
        \
        **Join**: Erstellen einer Zeichenfolge aus allen Elementen in einem Array und Trennen dieser Elemente mit dem angegebenen Trennzeichen. 
        \
        \
        **JSON analysieren**: Erstellen von benutzerfreundlichen Token aus Eigenschaften und den zugehörigen Werten in JSON-Inhalten, sodass Sie diese Eigenschaften in Ihrem Workflow verwenden können. 
        \
        \
        **Select**: Erstellen eines Arrays mit JSON-Objekten durch Transformieren von Elementen oder Werten in einem anderen Array und Zuordnen dieser Elemente zu angegebenen Eigenschaften.
    :::column-end:::
    :::column:::
        ![Aktionssymbol „Datum/Uhrzeit“ in Logic Apps][date-time-icon]
        \
        \
        **Datum/Uhrzeit**
        \
        \
        Ausführen von Vorgängen mit Zeitstempeln.
        \
        \
        **Zu Zeit hinzufügen**: Hinzufügen der angegebenen Anzahl von Einheiten zu einem Zeitstempel. 
        \
        \
        **Zeitzone konvertieren**: Konvertiert einen Zeitstempel von der Quellzeitzone in die Zielzeitzone. 
        \
        \
        **Aktuelle Zeit**: Gibt den aktuellen Zeitstempel als Zeichenfolge zurück. 
        \
        \
        **Zukünftige Zeit abrufen**: Gibt den aktuellen Zeitstempel plus der angegebenen Zeiteinheiten zurück. 
        \
        \
        **Vergangene Zeit abrufen**: Gibt den aktuellen Zeitstempel abzüglich der angegebenen Zeiteinheiten zurück. 
        \
        \
        **Von Uhrzeit abziehen**: Subtrahiert eine Anzahl von Zeiteinheiten von einem Zeitstempel.
    :::column-end:::
    :::column:::
        [![Aktionssymbol „Variablen“ in Logic Apps][variables-icon]][variables-doc]
        \
        \
        [**Variablen**][variables-doc]
        \
        \
        Ausführen von Vorgängen mit Variablen.
        \
        \
        **An Arrayvariable anfügen**: Einfügen eines Werts als letztes Element in einem Array, das in einer Variablen gespeichert ist. 
        \
        \
        **An Zeichenfolgenvariable anfügen**: Einfügen eines Werts als letztes Element in einer Zeichenfolge, die in einer Variablen gespeichert ist.
        \
        \
        **Variablenwert verringern**: Verringern einer Variablen um einen konstanten Wert.
        \
        \
        **Variable schrittweise erhöhen**: Erhöhen einer Variablen um einen konstanten Wert. 
        \
        \
        **Variable initialisieren**: Erstellen einer Variablen und Deklarieren ihres Datentyps und Anfangswert. 
        \
        \
        **Variable festlegen**: Zuweisen eines anderen Werts zu einer vorhandenen Variable. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen benutzerdefinierter APIs, die über Azure Logic Apps aufgerufen werden können](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-in icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[sql-server-icon]: ./media/apis-list/sql.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Erstellen einer Azure API Management-Dienstinstanz zum Verwalten und Veröffentlichen von APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integration von Logik-Apps in App Service-API-Apps"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Verwalten von Dateien in Ihrem Blobcontainer mit Azure Blob Storage-Connector"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integration von Logik-Apps in Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Verarbeiten von Nachrichten in Gruppen oder als Batches"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Auswerten einer Bedingung und Ausführen unterschiedlicher Aktionen abhängig davon, ob die Bedingung TRUE oder FALSE ist"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Ausführen von Datenvorgängen, z.B. das Filtern von Arrays oder das Erstellen von CSV- und HTML-Tabellen"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Ausführen der gleichen Aktionen für jedes Element in einem Array"
[http-doc]: ./connectors-native-http.md "Aufrufen von HTTP- oder HTTPS-Endpunkten aus Ihren Logik-Apps"
[http-request-doc]: ./connectors-native-reqres.md "Empfangen von HTTP-Anforderungen in Ihren Logik-Apps"
[http-response-doc]: ./connectors-native-reqres.md "Antworten auf HTTP-Anforderungen aus Ihren Logik-Apps"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Aufrufen von REST-Endpunkten aus Ihren Logik-Apps"
[http-webhook-doc]: ./connectors-native-webhook.md "Warten auf bestimmte Ereignisse von HTTP- oder HTTPS-Endpunkten"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Hinzufügen und Ausführen von JavaScript-Codeausschnitten in Ihren Logik-Apps"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrieren von Logik-Apps in geschachtelte Workflows"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Auswählen und Filtern von Arrays mit der Abfrageaktion"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Ausführen von Logik-Apps, basierend auf einem Zeitplan"
[schedule-delay-doc]: ./connectors-native-delay.md "Verzögern der Ausführung der nächsten Aktion"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Verzögern der Ausführung der nächsten Aktion"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Ausführen von Logik-Apps nach einem sich wiederholenden Zeitplan"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Ausführen von Logik-Apps, die Daten in zusammenhängenden Blöcken verarbeiten müssen"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organisieren von Aktionen in Gruppen, die einen eigenen Status erhalten, nachdem die Ausführung der Aktionen in der Gruppe beendet ist"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Stellen Sie eine Verbindung mit Azure SQL-Datenbank oder SQL Server her. Erstellten, Aktualisieren, Abrufen und Löschen von Einträgen in einer SQL-Datenbanktabelle"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organisieren von Aktionen in Fälle, denen eindeutige Werte zugewiesen werden. Es wird nur der Fall ausgeführt, dessen Wert mit dem Ergebnis eines Ausdrucks, Objekts oder Token übereinstimmt. Wenn keine Übereinstimmungen vorhanden sind, wird der Standardfall ausgeführt."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Beenden oder Abbrechen eines aktiv ausgeführten Workflows für Ihre Logik-App"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Wiederholen von Aktionen, bis die angegebene Bedingung TRUE ist oder sich ein Status geändert hat"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Ausführen von Vorgängen mit Variablen, z.B. Initialisieren, Festlegen, Erhöhen oder Verringern in einzelnen Schritten sowie Anfügen an Arrayvariable oder Zeichenfolgenvariable"
