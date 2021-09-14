---
title: Mehr App-Entwicklung, weniger Infrastrukturverwaltung
description: Azure Serverless unterstützt Sie dabei, sich auf die Erstellung cloudbasierter Apps zu konzentrieren, während Sie weniger für die Verwaltung der Infrastruktur ausgeben, wenn Sie Azure Logic Apps und Azure Functions verwenden.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 335a553897f2487eef317852f23cd475a92b2f81
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362678"
---
# <a name="azure-serverless-overview-create-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Übersicht zu Azure Serverless: Erstellen cloudbasierter Apps und Lösungen mit Azure Logic Apps und Azure Functions

Serverlos bedeutet zwar nicht „keine Server“, aber Azure Serverless unterstützt Sie dabei, weniger für die Verwaltung Ihrer Infrastruktur aufwenden zu müssen. Bei der herkömmlichen App-Entwicklung können Sie viel Zeit und Energie für die Diskussion und Behandlung von Hosting-, Skalierungs- und Überwachungslösungen aufwenden, um Ihre App-Anforderungen zu erfüllen. Mit serverlosen Apps und Lösungen können Sie diese Probleme einfacher als Teil der App oder Lösung behandeln. Serverless steht für weitere Vorteile wie schnellere Entwicklung, weniger Code, Einfachheit und Skalierungsflexibilität. Mit all diesen Funktionen können Sie sich mehr auf die Geschäftslogik konzentrieren. Außerdem wird Serverless in der Regel basierend auf der Nutzung abgerechnet. Wenn also keine Nutzung erfolgt, fallen keine Gebühren an. Weitere Informationen finden Sie unter [Azure – serverlos](https://azure.microsoft.com/solutions/serverless/).

In diesem Artikel werden die wichtigsten serverlosen Angebote in Azure kurz zusammengefasst, Azure Logic Apps und Azure Functions. Beide Dienste entsprechen den zuvor beschriebenen Prinzipien und unterstützen Sie beim Erstellen robuster Cloud-Apps und -Lösungen mit minimalem Code.

Weitere einführende Informationen finden Sie auf den Azure-Seiten für [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) und [Azure Functions](https://azure.microsoft.com/services/functions/). Ausführlichere Informationen finden Sie auf den Dokumentationsseiten [Was ist Azure Logic Apps?](logic-apps-overview.md) und [Einführung in Azure Functions](../azure-functions/functions-overview.md).

## <a name="azure-logic-apps"></a>Azure Logic Apps

Dieser Dienst bietet vereinfachte Möglichkeiten zum Entwerfen, Entwickeln und Orchestrieren automatisierter ereignisgesteuerter Integrationsworkflows, die in der Cloud ausgeführt und skaliert werden. Mit Azure Logic Apps können Sie einen visuellen Designer verwenden, um Geschäftsprozesse schnell als Workflows zu modellieren. Der erste Schritt eines Workflows ist immer ein Trigger. Nach dem Trigger führen eine oder mehrere Aktionen nachfolgende Vorgänge im Workflow aus. Diese Vorgänge können verschiedene Kombinationen von Aktionen einschließlich bedingter Logik und Datenkonvertierungen umfassen.

Um Ihre Workflows mit anderen Azure-Diensten, Microsoft-Diensten, cloudbasierten Umgebungen und lokalen Umgebungen zu verbinden, ohne Code schreiben zu müssen, können Sie Ihren Workflows vordefinierte Trigger und Aktionen hinzufügen, die Sie aus [Hunderten von Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors/) auswählen, die von Microsoft verwaltet werden. Jeder Connector ist tatsächlich ein Proxy oder Wrapper um eine API, mit der der zugrunde liegende Dienst mit Azure Logic Apps kommunizieren kann. Beispielsweise bietet der Office 365 Outlook-Connector einen Trigger mit Namen **Wenn eine neue E-Mail empfangen wird**. Für serverlose Apps und Lösungen können Sie Azure Logic Apps verwenden, um mehrere in Azure Functions erstellte Funktionen zu orchestrieren. Auf diese Weise können Sie ganz einfach verschiedene Funktionen als einzelnen Prozess aufrufen, insbesondere dann, wenn für den Prozess die Arbeit mit einer externen API oder einem externen System erforderlich ist.

Wenn kein Connector verfügbar ist, um Ihre Anforderungen zu erfüllen, können Sie den integrierten HTTP-Vorgang oder Anforderungstrigger verwenden, um mit einem beliebigen Dienstendpunkt zu kommunizieren. Alternativ können Sie einen eigenen Connector mithilfe einer vorhandenen API erstellen.

Basierend auf dem von Ihnen gewählten Logik-App-Ressourcentyp wird der zugeordnete Workflow entweder in mehrinstanzenfähigen Azure Logic Apps-Instanzen, Einzelmandanten-Azure Logic Apps-Instanzen oder in einer dedizierten Integrationsdienstumgebung (Integration Service Environment, ISE) ausgeführt. Ihnen sind jeweils eigene Funktionen, Vorteile und Abrechnungsmodelle zugeordnet. Die Azure-Portal ist die schnellste Möglichkeit, mit dem Erstellen von Logik-App-Workflows zu beginnen. Sie können jedoch auch andere Tools wie Visual Studio Code, Visual Studio, Azure PowerShell und andere verwenden. Weitere Informationen finden Sie unter [Was ist Azure Logic Apps?](logic-apps-overview.md).

Eine Einführung in Azure Logic Apps finden Sie unter [Schnellstart: Erstellen eines Integrationsworkflows mit Mandanten-fähigen Azure Logic Apps und dem Azure-Portal](quickstart-create-first-logic-app-workflow.md). Probieren Sie alternativ diese [Schritte zum Erstellen einer serverlosen Beispiel-App mit Azure Logic Apps und Azure Functions in Visual Studio](create-serverless-apps-visual-studio.md) aus.

Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Übersicht: Was ist Azure Logic Apps?](logic-apps-overview.md)
* [Informationen zu Connectors in Azure Logic Apps](../connectors/apis-list.md)
* [Konnektoren: Azure Logic Apps, Microsoft Power Automate, Microsoft Power Apps](/connectors/connectors)
* [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](single-tenant-overview-compare.md)
* [Modelle für Verbrauchsmessung, Abrechnung und Preise bei Azure Logic Apps](logic-apps-pricing.md)

## <a name="azure-functions"></a>Azure Functions

Dieser Dienst bietet eine vereinfachte Möglichkeit zum Schreiben und Ausführen von Codeteilen oder *Funktionen* in der Cloud. Sie können nur den Code schreiben, den Sie für das aktuelle Problem benötigen, ohne eine vollständige App oder die erforderliche Infrastruktur einzurichten. So ist die Entwicklung schneller und produktiver. Verwenden Sie die Entwicklungssprache Ihrer Wahl, z. B. C#, Java, JavaScript, PowerShell, Python und TypeScript. Sie bezahlen nur für die Dauer der Ausführung Ihres Codes. Azure skaliert flexibel nach Bedarf.

Um mit Azure Functions zu beginnen, versuchen Sie, [Ihre erste Azure-Funktion im Azure-Portal zu erstellen](../azure-functions/functions-create-function-app-portal.md).

Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Was ist Azure Functions?](../azure-functions/functions-overview.md)
* [Erste Schritte mit Azure Functions](../azure-functions/functions-get-started.md)
* [In Azure Functions unterstützte Sprachen](../azure-functions/supported-languages.md)
* [Azure Functions-Hostingoptionen](../azure-functions/functions-scale.md)
* [Azure Functions – Preise](../azure-functions/pricing.md)

## <a name="get-started-with-serverless-apps-in-azure"></a>Erste Schritte mit serverlosen Apps in Azure

Azure bietet umfangreiche Tools zum Entwickeln, Bereitstellen und Verwalten von serverlosen Apps. Sie können serverlose Apps mit Azure-Portal, Visual Studio oder [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) erstellen. Nachdem Sie Ihre App erstellt haben, können Sie [diese App mit Azure Resource Manager-Vorlagen schnell bereitstellen](logic-apps-deploy-azure-resource-manager-templates.md). Azure bietet auch Überwachung, auf die Sie direkt über das Azure-Portal, über die API oder über SDKs bzw. alternativ über integrierte Tools für Azure Monitor-Protokolle und Application Insights zugreifen können.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer serverlosen Beispiel-App mit Azure Logic Apps und Azure Functions in Visual Studio](create-serverless-apps-visual-studio.md)
* [Serverloses Erstellen eines Customer Insights-Dashboards](logic-apps-scenario-social-serverless.md)