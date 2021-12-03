---
title: Integrierte Trigger und Aktionen
description: Verwenden Sie integrierte Trigger und Aktionen, um automatisierte Workflows zu erstellen, die Apps, Daten, Dienste und Systeme integrieren, Workflows zu steuern sowie Daten mithilfe von Azure Logic Apps zu verwalten.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 305f23d802324f5d974caaaf49afc072ff9d54f6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071019"
---
# <a name="built-in-triggers-and-actions-in-azure-logic-apps"></a>Integrierte Trigger und Aktionen in Azure Logic Apps

Mithilfe von [integrierten Triggern und Aktionen](apis-list.md) können Sie den [Zeitplan und die Struktur Ihres Workflows steuern](#control-workflow), [eigenen Code ausführen](#run-code-from-workflows), [Daten verwalten oder bearbeiten](#manage-or-manipulate-data) und andere Aufgaben in Ihren Workflows ausführen. Anders als [verwaltete Connectors](managed.md) sind viele integrierte Vorgänge nicht an einen bestimmten Dienst, ein System oder ein Protokoll gebunden. Beispielsweise können Sie mithilfe des Triggers „Serie“ fast jeden Workflow basierend auf einem Zeitplan starten. Sie können auch den Trigger „Anforderung“ verwenden, damit Ihr Workflow erst gestartet wird, wenn er aufgerufen wird. Alle integrierten Vorgänge werden nativ in Azure Logic Apps ausgeführt, und die meisten dieser Vorgänge erfordern nicht, dass Sie vor ihrer Verwendung eine Verbindung erstellen.

Für eine kleinere Anzahl von Diensten, Systemen und Protokollen bietet Azure Logic Apps integrierte Vorgänge wie Azure API Management, Azure App Services und Azure Functions sowie zum Aufrufen weiterer Logik-App-Workflows in Azure Logic Apps. Die Anzahl und der verfügbare Bereich variieren je nachdem, ob Sie eine auf einem Verbrauchsplan basierende Logik-App-Ressource erstellen, die in Azure Logic Apps mit mehreren Mandanten ausgeführt wird, oder eine auf einem Standardplan basierende Logik-App-Ressource, die in Azure Logic Apps mit einem Mandanten ausgeführt wird. Weitere Informationen finden Sie unter [Einzelmandanten versus Mehrmandanten und Integration Service Environment (ISE)](../logic-apps/single-tenant-overview-compare.md). In den meisten Fällen bietet die integrierte Version eine höhere Leistung, bessere Funktionen, niedrigere Preise usw.

Wenn Sie beispielsweise eine Logik-App mit nur einem Mandanten erstellen, sind sowohl integrierte Vorgänge als auch [Vorgänge für verwaltete Connectors](managed.md) für einige Dienste verfügbar, insbesondere Azure Blob, Azure Event Hubs, Azure Cosmos DB, Azure Service Bus, DB2, MQ und SQL Server. In bestimmten Fällen sind einige integrierte Vorgänge nur für einen Logik-App-Ressourcentyp verfügbar. Batch-Vorgänge sind beispielsweise derzeit nur für Workflows der Logik-App „Verbrauch“ verfügbar. In den meisten Fällen bietet die integrierte Version eine höhere Leistung, bessere Funktionen, niedrigere Preise usw.

In der folgenden Liste werden nur einige der Aufgaben beschrieben, die Sie mit [integrierten Triggern und Aktionen](#general-built-in-triggers-and-actions) ausführen können:

- Ausführen von Workflows mit benutzerdefinierten und erweiterten Zeitplänen. Weitere Informationen zur Planung finden Sie im [Abschnitt zum Wiederholungsverhalten in der Connectorübersicht für Azure Logic Apps](apis-list.md#recurrence-behavior).

- Organisieren und Steuern der Struktur des Workflows, z. B. mit Schleifen und Bedingungen.

- Verwenden von Variablen, Datumsangaben, Datenvorgängen, Inhaltstransformationen und Batchvorgängen.

- Kommunizieren mit anderen Endpunkten über HTTP-Trigger und -Aktionen.

- Empfangen von und Reagieren auf Anforderungen.

- Aufrufen eigener Funktionen (Azure Functions), Web-Apps (Azure App Services), APIs (Azure API Management) sowie anderer Azure Logic Apps-Workflows, die Anforderungen empfangen können usw.

## <a name="general-built-in-triggers-and-actions"></a>Allgemeine integrierte Trigger und Aktionen

Azure Logic Apps bietet die folgenden integrierten Trigger und Aktionen:

:::row:::
    :::column:::
        [![Zeitplansymbol][schedule-icon]][schedule-doc]
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
        [![HTTP-Trigger und -Aktionssymbol][http-icon]][http-doc]
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
        [![Anforderungstriggersymbol][http-request-icon]][http-request-doc]
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
        [![Batchsymbol][batch-icon]][batch-doc]<br>(*Nur Logik-App „Verbrauch“* )     \
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
        [![STFP-SSH Symbol][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]<br>(*Nur Logik-App „Standard“* )     \
        \
        Herstellen einer Verbindung mit SFTP-Servern, auf die Sie über das Internet mittels SSH zugreifen können, sodass Sie Ihre Dateien und Ordner verwenden können.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="service-based-built-in-trigger-and-actions"></a>Dienstbasierte integrierte Trigger und Aktionen

Azure Logic Apps stellt die folgenden integrierten Aktionen für die folgenden Dienste zur Verfügung:

:::row:::
    :::column:::
        [![API Management-Symbol][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        Aufrufen eigener Trigger und Aktionen in APIs, die Sie mit [Azure API Management](../api-management/api-management-key-concepts.md) definieren, verwalten und veröffentlichen. <p><p>**Hinweis:** Wird bei Verwendung des [Tarifs „Consumption“ für API Management](../api-management/api-management-features.md) nicht unterstützt.
    :::column-end:::
    :::column:::
        [![Azure App Services-Symbol][azure-app-services-icon]][azure-app-services-doc]
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
        [![Symbol „Azure-Blob“][azure-blob-storage-icon]][azure-app-services-doc]
        \
        \
        [**Azure Blob**][azure-blob-storage-doc]<br>(*Nur Logik-App „Standard“* )     \
        \
        Herstellen einer Verbindung mit Ihrem Azure Storage-Konto, damit Sie Blobinhalte erstellen und verwalten können.
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB-Symbol][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB**][azure-cosmos-db-doc]<br>(*Nur Logik-App „Standard“* )     \
        \
        Herstellen einer Verbindung mit Azure Cosmos DB, damit Sie auf Azure Cosmos DB-Dokumente zugreifen und diese verwalten können.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Functions-Symbol][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Aufrufen von [in Azure gehosteten Funktionen](../azure-functions/functions-overview.md), um eigene *Codeausschnitte* (C# oder Node.js) im Workflow auszuführen.
    :::column-end:::
    :::column:::
        [![Azure Logic Apps-Symbol][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Aufrufen anderer Workflows, die mit dem Anforderungstrigger **Beim Empfang einer HTTP-Anforderung** gestartet werden.
    :::column-end:::
    :::column:::
        [![Azure Service Bus-Symbol][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]<br>(*Nur Logik-App „Standard“* )     \
        \
        Verwalten von asynchronen Nachrichten, Warteschlangen, Sitzungen, Themen und Themenabonnements.
    :::column-end:::
    :::column:::
        [![IBM DB2 Symbol][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**DB2**][ibm-db2-doc]<br>(*Nur Logik-App „Standard“* )     \
        \
        Stellen Sie eine Verbindung mit IBM DB2 in der Cloud oder lokal her. Aktualisieren einer Zeile, Abrufen einer Tabelle und mehr.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs Symbol][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Event Hubs**][azure-event-hubs-doc]<br>(*Nur Logik-App „Standard“* )     \
        \
        Nutzen und Veröffentlichen von Ereignissen über eine Event Hub-Instanz. Sie können beispielsweise mithilfe von Event Hubs eine Ausgabe Ihrer Logik-App abrufen und die Ausgabe anschließend an einen Echtzeitanalyseanbieter senden.
    :::column-end:::
    :::column:::
        [![IBM MQ Symbol][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**MQ**][ibm-mq-doc]<br>(*Nur Logik-App „Standard“* )     \
        \
        Herstellen einer Verbindung mit IBM MQ (lokal oder in Azure) zum Senden und Empfangen von Nachrichten.
    :::column-end:::
    :::column:::
        [![SQL Server Symbol][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]<br>(*Nur Logik-App „Standard“* )     \
        \
        Herstellen einer Verbindung mit Ihrer lokalen SQL Server-Instanz oder einer Azure SQL-Datenbank in der Cloud, sodass Sie Datensätze verwalten, gespeicherte Prozeduren ausführen oder Abfragen ausführen können. <p>**Hinweis**: Azure Logic Apps für Einzelmandanten bietet sowohl SQL-integrierte Vorgänge als auch Vorgänge des verwalteten Connectors, während mehrinstanzenfähiges Azure Logic Apps nur Vorgänge des verwalteten Connectors bietet. <p>Weitere Informationen finden Sie unter [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](../logic-apps/single-tenant-overview-compare.md).
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Ausführen von Code über Workflows

Azure Logic Apps bietet die folgenden integrierten Aktionen, mit denen Sie Ihren eigenen Code im Workflow ausführen können:

:::row:::
    :::column:::
        [![Azure Functions-Symbol][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Aufrufen von [in Azure gehosteten Funktionen](../azure-functions/functions-overview.md), um eigene *Codeausschnitte* (C# oder Node.js) im Workflow auszuführen.
    :::column-end:::
    :::column:::
        [![Inlinecode-Aktionssymbol][inline-code-icon]][inline-code-doc]
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

Azure Logic Apps bietet die folgenden integrierten Aktionen zum Strukturieren und Steuern der Aktionen in Ihrem Workflow:

:::row:::
    :::column:::
        [![Condition-Aktionssymbol (Bedingung)][condition-icon]][condition-doc]
        \
        \
        [**Bedingung**][condition-doc]
        \
        \
        Auswerten einer Bedingung und Ausführen unterschiedlicher Aktionen abhängig davon, ob die Bedingung TRUE oder FALSE ist.
    :::column-end:::
    :::column:::
        [![„For Each“-Aktionssymbol][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        Ausführen der gleichen Aktionen für jedes Element in einem Array.
    :::column-end:::
    :::column:::
        [![Scope-Aktionssymbol (Bereich)][scope-icon]][scope-doc]
        \
        \
        [**Name**][scope-doc]
        \
        \
        Gruppieren von Aktionen in *Bereiche*, die einen eigenen Status erhalten, nachdem die Ausführung der Aktionen im Bereich beendet ist.
    :::column-end:::
    :::column:::
        [![Switch-Aktionssymbol (Wechseln)][switch-icon]][switch-doc]
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
        [![Terminate-Aktionssymbol (Beenden)][terminate-icon]][terminate-doc]
        \
        \
        [**Beenden**][terminate-doc]
        \
        \
        Beenden des aktiv ausgeführten Workflows einer Logik-App.
    :::column-end:::
    :::column:::
        [![Until-Aktionssymbol (Bis)][until-icon]][until-doc]
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

Azure Logic Apps bietet die folgenden integrierten Aktionen zur Verwendung von Datenausgaben mit den zugehörigen Formaten:

:::row:::
    :::column:::
        [![Datenvorgänge][data-operations-icon]][data-operations-doc]
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
        ![„Datum/Uhrzeit“-Aktionssymbol][date-time-icon]
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
        [![Variablen-Aktionssymbol][variables-icon]][variables-doc]
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

## <a name="integration-account-built-in-actions"></a>Integrierte Aktionen für Integrationskonten

Azure Logic Apps bietet die folgenden integrierten Aktionen, die entweder ein Integrationskonto erfordern, wenn mehrinstanzenfähige Mandanten oder verbrauchsplanbasierte Azure Logic Apps verwendet werden, oder kein Integrationskonto erfordern, wenn standardplanbasierte Azure Logic Apps mit nur einem Mandanten verwendet werden:

> [!NOTE]
> Bevor Sie die Integrationskontoaktion in mehrinstanzenfähigen, verbrauchsplanbasierten Azure Logic Apps verwenden können, müssen Sie [Ihre Logic-App-Ressource mit einem Integrationskonto verknüpfen](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Bei standardplanbasierten Azure Logic Apps mit nur einem Mandanten ist für einige Integrationskontovorgänge jedoch keine Verknüpfung Ihrer Logik-App-Ressource mit einem Integrationskonto erforderlich, z. B. Liquid-Vorgänge und XML-Vorgänge. Um diese Aktionen zu verwenden, benötigen Sie Liquid-Zuordnungen, XML-Zuordnungen oder XML-Schemas, die Sie über die entsprechenden Aktionen im Azure-Portal hochladen können. Sie können sie auch mithilfe der entsprechenden Ordner für **Zuordnungen** und **Schemas** im Ordner für **Artefakte** des Visual Studio Code-Projekts hinzufügen.

:::row:::
    :::column:::
        [![Flatfile-Decodierung][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Flatfile-Decodierung**][flat-file-decode-doc]
        \
        \
        Codieren Sie XML, bevor Sie den Inhalt an einen Handelspartner senden.
    :::column-end:::
    :::column:::
        [![Flatfile-Decodierungssymbol][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Flatfile-Codierung**][flat-file-encode-doc]
        \
        \
        Decodieren Sie XML nach dem Empfang des Inhalts von einem Handelspartner.
    :::column-end:::
    :::column:::
        [![Integrationskontosymbol][integration-account-icon]][integration-account-doc]
        \
        \
        [**Artefaktsuche für Integrationskonto**<br>(*nur mehrinstanzenfähig*)][integration-account-doc]
        \
        \
        Abrufen benutzerdefinierter Metadaten für Artefakte wie Handelspartner, Vereinbarungen, Schemas usw. in Ihr Integrationskonto.
    :::column-end:::
    :::column:::
        [![Liquid-Vorgängesymbol][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Liquid-Vorgänge**][json-liquid-transform-doc]
        \
        \
        Konvertieren Sie die folgenden Formate mithilfe von Liquid-Vorlagen: <p><p>– JSON in JSON <br>– JSON in TEXT <br>– XML in JSON <br>– XML in TEXT
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![„XML transformieren“-Symbol][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**XML transformieren**][xml-transform-doc]
        \
        \
        Das XML-Quellformat in ein anderes XML-Format konvertieren.
    :::column-end:::
    :::column:::
        [![XML-Überprüfungssymbol][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML-Überprüfung**][xml-validate-doc]
        \
        \
        XML-Dokumente anhand des angegebenen Schemas überprüfen.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen benutzerdefinierter APIs, die über Azure Logic Apps aufgerufen werden können](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-in icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
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
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sql-server-icon]: ./media/apis-list/sql.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Built-in integration account connector icons -->
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[xml-transform-icon]: ./media/apis-list/xml-transform.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Erstellen einer Azure API Management-Dienstinstanz zum Verwalten und Veröffentlichen von APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integration von Logik-Apps in App Service-API-Apps"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Verwalten von Dateien in Ihrem Blobcontainer mit Azure Blob Storage-Connector"
[azure-cosmos-db-doc]: ./connectors-create-api-cosmos-db.md "Herstellen einer Verbindung mit Azure Cosmos DB, damit Sie auf Azure Cosmos DB-Dokumente zugreifen und diese verwalten können"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Herstellen einer Verbindung mit Azure Event Hubs, sodass Sie Ereignisse zwischen Logik-Apps und Event Hubs empfangen und senden können"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integration von Logik-Apps in Azure Functions"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Verwalten von Nachrichten aus Service Bus-Warteschlangen, -Themen -und Themenabonnements"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Verarbeiten von Nachrichten in Gruppen oder als Batches"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Auswerten einer Bedingung und Ausführen unterschiedlicher Aktionen abhängig davon, ob die Bedingung TRUE oder FALSE ist"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Ausführen von Datenvorgängen, z.B. das Filtern von Arrays oder das Erstellen von CSV- und HTML-Tabellen"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Ausführen der gleichen Aktionen für jedes Element in einem Array"
[http-doc]: ./connectors-native-http.md "Aufrufen von HTTP- oder HTTPS-Endpunkten aus Ihren Logik-Apps"
[http-request-doc]: ./connectors-native-reqres.md "Empfangen von HTTP-Anforderungen in Ihren Logik-Apps"
[http-response-doc]: ./connectors-native-reqres.md "Antworten auf HTTP-Anforderungen aus Ihren Logik-Apps"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Aufrufen von REST-Endpunkten aus Ihren Logik-Apps"
[http-webhook-doc]: ./connectors-native-webhook.md "Warten auf bestimmte Ereignisse von HTTP- oder HTTPS-Endpunkten"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Stellen Sie eine Verbindung mit IBM DB2 in der Cloud oder lokal her. Sie können eine Zeile aktualisieren, eine Tabelle abrufen und vieles mehr."
[ibm-mq-doc]: ./connectors-create-api-mq.md "Herstellen einer Verbindung mit IBM MQ (lokal oder in Azure) und Senden und Empfangen von Nachrichten"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Hinzufügen und Ausführen von JavaScript-Codeausschnitten in Ihren Logik-Apps"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrieren von Logik-Apps in geschachtelte Workflows"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Auswählen und Filtern von Arrays mit der Abfrageaktion"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Ausführen von Logik-Apps, basierend auf einem Zeitplan"
[schedule-delay-doc]: ./connectors-native-delay.md "Verzögern der Ausführung der nächsten Aktion"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Verzögern der Ausführung der nächsten Aktion"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Ausführen von Logik-Apps nach einem sich wiederholenden Zeitplan"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Ausführen von Logik-Apps, die Daten in zusammenhängenden Blöcken verarbeiten müssen"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organisieren von Aktionen in Gruppen, die einen eigenen Status erhalten, nachdem die Ausführung der Aktionen in der Gruppe beendet ist"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Herstellen einer Verbindung mit Ihrem SFTP-Konto mittels SSH. Sie können Dateien hochladen, abrufen, löschen und vieles mehr."
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Stellen Sie eine Verbindung mit Azure SQL-Datenbank oder SQL Server her. Erstellten, Aktualisieren, Abrufen und Löschen von Einträgen in einer SQL-Datenbanktabelle"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organisieren von Aktionen in Fälle, denen eindeutige Werte zugewiesen werden. Es wird nur der Fall ausgeführt, dessen Wert mit dem Ergebnis eines Ausdrucks, Objekts oder Token übereinstimmt. Wenn keine Übereinstimmungen vorhanden sind, wird der Standardfall ausgeführt."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Beenden oder Abbrechen eines aktiv ausgeführten Workflows für Ihre Logik-App"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Wiederholen von Aktionen, bis die angegebene Bedingung TRUE ist oder sich ein Status geändert hat"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Ausführen von Vorgängen mit Variablen, z.B. Initialisieren, Festlegen, Erhöhen oder Verringern in einzelnen Schritten sowie Anfügen an Arrayvariable oder Zeichenfolgenvariable"

<!--Built-in integration account doc links-->
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informationen zu Enterprise Integration (Flatfile)"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informationen zu Enterprise Integration (Flatfile)"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Verwalten von Metadaten für Integrationskontoartefakte"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformieren von JSON mithilfe einer Liquid-Vorlage"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformieren von XML-Nachrichten"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Überprüfen von XML-Nachrichten"
