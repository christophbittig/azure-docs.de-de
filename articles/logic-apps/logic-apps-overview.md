---
title: Übersicht über Azure Logic Apps
description: Bei Azure Logic Apps handelt es sich um eine Cloud-Plattform zur Automatisierung von Workflows, die Apps, Daten, Dienste und Systeme mit wenig bis gar keinem Code integrieren. Workflows können in einer Multi-Mandantenfähigen, Einzel-Mandantenfähigen oder dedizierten Umgebung ausgeführt werden.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: overview
ms.custom: mvc, contperf-fy21q4
ms.date: 08/18/2021
ms.openlocfilehash: 299585baea87ca956fafb936fe7b8b265c936abc
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122638938"
---
# <a name="what-is-azure-logic-apps"></a>Was ist Azure Logic Apps?

Bei [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps) handelt es sich um eine Cloud-Plattform zur Automatisierung von [*Workflows*](#workflow), die Apps, Daten, Dienste und Systeme mit wenig bis gar keinem Code integrieren. Mit dieser Plattform können Sie schnell hoch skalierbare Integrationslösungen für Ihr Unternehmen und Business-to-Business (B2B)-Szenarien entwickeln. Als Mitglied der [Azure Integration Services](https://azure.microsoft.com/product-categories/integration/) vereinfacht Logic Apps die Art und Weise, wie Sie Legacy-, moderne und topaktuelle Systeme über Cloud-, lokale und hybride Umgebungen hinweg verbinden.

Die folgende Liste beschreibt lediglich einige Beispielaufgaben, Geschäftsprozesse und Workloads, die Sie mit dem Logic Apps-Dienst automatisieren können:

* Planen und Senden von E-Mail-Benachrichtigungen mit Office 365, wenn ein bestimmtes Ereignis eintritt, z. B. eine neue Datei wird hochgeladen.

* Weiterleiten und Verarbeiten von Kundenbestellungen in lokalen Systemen und Clouddiensten

* Verschieben von hochgeladenen Dateien von einem SFTP- oder FTP-Server in Azure Storage

* Überwachen von Tweets, Analysieren der Stimmung und Erstellen von Warnungen oder Aufgaben für zu prüfende Elemente

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

Basierend auf dem von Ihnen ausgewählten und erstellten Logik-App-Ressourcentyp werden Ihre Logik-Apps in mehrinstanzenfähigen Azure Logic Apps-Instanzen, [Azure Logic Apps-Instanzen mit nur einem Mandanten](single-tenant-overview-compare.md) oder in einer dedizierten [Integrationsdienstumgebung](connect-virtual-network-vnet-isolated-environment-overview.md) ausgeführt, wenn Sie auf ein virtuelles Azure-Netzwerk zugreifen. Um Logik-Apps in Containern auszuführen, [erstellen Sie Logik-Apps mit nur einem Mandanten mithilfe von Logic Apps mit Azure Arc-Unterstützung](azure-arc-enabled-logic-apps-create-deploy-workflows.md). Weitere Informationen finden Sie unter [Was ist Logic Apps mit Azure Arc-Unterstützung?](azure-arc-enabled-logic-apps-overview.md) und [Unterschiede bei Ressourcentyp und Hostumgebung für Logik-Apps](#resource-environment-differences).

Um sicher auf verschiedene Datenquellen zuzugreifen und Operationen in Echtzeit auszuführen, können Sie [*verwaltete Konnektoren*](#managed-connector) aus einem [Ökosystem von über 400+ und wachsenden Azure-Konnektoren](/connectors/connector-reference/connector-reference-logicapps-connectors) auswählen, die Sie in Ihren Workflows verwenden können:

* Azure-Dienste wie Blob Storage und Service Bus

* Office 365-Dienste wie Outlook, Excel und SharePoint

* Datenbankserver wie SQL und Oracle

* Unternehmenssysteme wie SAP und IBM MQ

* Dateifreigaben wie FTP und SFTP

Um mit einem beliebigen Dienstendpunkt zu kommunizieren, Ihren eigenen Code auszuführen, Ihren Workflow zu organisieren oder Daten zu manipulieren, können Sie [*integrierte*](#built-in-operations) Auslöser und Aktionen verwenden, die nativ innerhalb des Logic Apps-Dienstes ausgeführt werden. Zu den integrierten Triggern zählen beispielsweise „Anforderung“, „HTTP“ und „Serie“. Zu den integrierten Aktionen zählen „Bedingung“, „For each“, „JavaScript-Code ausführen“ und Vorgänge, die in Azure gehostete Azure-Funktionen, Web-Apps oder API-Apps aufrufen, sowie andere Logic Apps-Workflows.

Für B2B-Integrationsszenarien enthält Logic Apps Funktionen von [BizTalk Server](/biztalk/core/introducing-biztalk-server). Um Business-to-Business (B2B) Artefakte zu definieren, erstellen Sie ein [*Integrationskonto,*](#integration-account) in welchem Sie diese Artefakte speichern. Nachdem Sie dieses Konto mit Ihrer Logik-App verknüpft haben, können Ihre Workflows diese B2B-Artefakte verwenden und Nachrichten austauschen, die den Standards für Electronic Data Interchange (EDI) und Enterprise Application Integration (EAI) entsprechen.

Weitere Informationen dazu, wie Workflows auf Apps, Daten, Dienste und Systeme zugreifen und mit ihnen arbeiten können, finden Sie in der folgenden Dokumentation:

* [Connectors für Azure Logic Apps](../connectors/apis-list.md)

* [Integrierte Trigger und Aktionen für Azure Logic Apps](../connectors/built-in.md)

* [Verwaltete Connectors für Azure Logic Apps](../connectors/managed.md)

* [B2B-Unternehmensintegrationslösungen mit Azure Logic Apps und dem Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>Schlüsselbegriffe

Die folgenden Begriffe stellen wichtige Konzepte im Dienst „Logic Apps“ dar.

### <a name="logic-app"></a>Logik-App

Eine *Logik-App* ist die Azure-Ressource, die Sie erstellen, wenn Sie einen Workflow entwickeln möchten. Es gibt [verschiedene Logik-App-Ressourcentypen, die in verschiedenen Umgebungen ausgeführt werden](#resource-environment-differences).

### <a name="workflow"></a>Workflow

Ein *Workflow* ist eine Abfolge von Schritten, die einen Task oder Prozess definieren. Jeder Workflow beginnt mit einem einzelnen Trigger, nach dem Sie mindestens eine Aktion hinzufügen müssen.

### <a name="trigger"></a>Trigger

Ein *Trigger* ist immer der erste Schritt in einem Workflow und gibt die Bedingung für die Ausführung weiterer Schritte in diesem Workflow an. Ein Triggerereignis kann beispielsweise der Eingang einer E-Mail in Ihrem Posteingang oder die Erkennung einer neuen Datei in einem Speicherkonto sein.

### <a name="action"></a>Action

Bei *Aktionen* handelt es sich um die einzelnen Schritte, die in einem Workflow auf den Trigger folgen. Jede Aktion führt einen Vorgang in einem Workflow aus.

### <a name="built-in-operations"></a>Integrierte Vorgänge

*Integrierte* Trigger oder Aktionen sind Vorgänge, die nativ in Azure Logic Apps ausgeführt werden. Integrierte Vorgänge bieten Ihnen beispielsweise die Möglichkeit, den Zeitplan oder die Struktur Ihres Workflows zu steuern, Ihren eigenen Code auszuführen, Daten zu verwalten und zu bearbeiten, Anforderungen an einen Endpunkt zu senden oder davon zu empfangen und andere Tasks in Ihrem Workflow auszuführen.

Die meisten integrierten Vorgänge sind keinem Dienst oder System zugeordnet. Einige integrierte Vorgänge sind für bestimmte Dienste verfügbar, z. B. für Azure Functions oder Azure App Service. Bei vielen ist es auch nicht erforderlich, zuerst eine Verbindung in Ihrem Workflow zu erstellen und Ihre Identität zu authentifizieren. Weitere Informationen und Beispiele finden Sie unter [Integrierte Vorgänge für Azure Logic Apps](../connectors/built-in.md).

Mithilfe des Triggers „Serie“ lässt sich beispielsweise fast jeder Workflow basierend auf einem Zeitplan starten. Sie können aber auch den Trigger „Anforderung“ verwenden, damit Ihr Workflow erst gestartet wird, wenn er aufgerufen wird.

### <a name="managed-connector"></a>Verwalteter Connector

Ein *verwalteter Connector* ist ein vordefinierter Proxy oder Wrapper einer REST-API, mit dem Sie auf bestimmte Apps, Daten, Dienste oder Systeme zugreifen können. Bevor Sie die meisten verwalteten Konnektoren verwenden können, müssen Sie zunächst eine Verbindung von Ihrem Workflow aus erstellen und Ihre Identität authentifizieren. Verwaltete Connectors werden von Microsoft bereitgestellt, gehostet und gewartet. Weitere Informationen finden Sie unter [Verwaltete Connectors für Azure Logic Apps](../connectors/managed.md).

Sie können Ihren Workflow zum Beispiel mit einem Trigger starten oder eine Aktion ausführen, die mit einem Dienst wie Office 365, Salesforce oder Dateiservern funktioniert.

### <a name="integration-account"></a>Integrationskonto

*Integrationskonten* sind die Azure-Ressourcen, die Sie erstellen, wenn Sie B2B-Artefakte zur Verwendung in Ihren Workflows definieren und speichern möchten. Nachdem Sie [ein Integrationskonto erstellt und mit Ihrer Logik-App verknüpft haben](logic-apps-enterprise-integration-create-integration-account.md), können Ihre Workflows diese B2B-Artefakte verwenden. Ihre Workflows können auch Nachrichten austauschen, die die Standards „Electronic Data Interchange“ (EDI) und „Enterprise Application Integration“ (EAI) erfüllen.

Sie können zum Beispiel Handelspartner, Vereinbarungen, Schemata, Maps und andere B2B-Artefakte definieren. Sie können Workflows erstellen, die diese Artefakte verwenden und Nachrichten über Protokolle wie AS2, EDIFACT, X12 und RosettaNet austauschen.

<a name="how-do-logic-apps-work"></a>

## <a name="how-logic-apps-work"></a>Funktionsweise von Logik-Apps

In einer Logik-App beginnt jeder Workflow immer mit einem einzelnen [Trigger](#trigger). Ein Trigger wird ausgelöst, wenn eine Bedingung erfüllt ist – also beispielsweise, wenn ein bestimmtes Ereignis eintritt oder wenn Daten bestimmte Kriterien erfüllen. Viele Trigger verfügen über [Planungsfunktionen](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md), mit denen die Häufigkeit der Workflowausführung gesteuert wird. Auf den Trigger folgt mindestens eine [Aktion](#action) zum Ausführen von Vorgängen, durch die beispielsweise Daten, die den Workflow durchlaufen, verarbeitet, behandelt oder konvertiert werden oder die den nächsten Schritt des Workflows einleiten.

Der folgende Screenshot zeigt einen Teil eines Beispielworkflows für Unternehmen. Dieser Workflow verwendet Bedingungen und Optionen, um die nächste Aktion zu bestimmen. Angenommen, Sie verfügen über ein Bestellsystem, und Ihr Workflow verarbeitet eingehende Bestellungen. Sie möchten Aufträge, die eine bestimmte Kostengrenze übersteigen, manuell überprüfen. Ihr Workflow verfügt bereits über vorherige Schritte, in denen bestimmt wird, wie hoch die Kosten eines eingehenden Auftrags sind. Daher erstellen Sie eine Anfangsbedingung auf Basis dieses Kostenwerts. Beispiel:

* Wenn die Bestellung unter einem bestimmten Betrag liegt, ist die Bedingung FALSE. Daher verarbeitet der Workflow die Bestellung.

* Wenn die Bedingung TRUE ist, sendet der Workflow eine E-Mail zur manuellen Überprüfung. Eine Option bestimmt den nächsten Schritt.

  * Wenn der Prüfer die Genehmigung erteilt, setzt der Workflow die Verarbeitung des Auftrags fort.

  * Wenn der Prüfer die Angelegenheit eskaliert, sendet der Workflow eine Eskalations-E-Mail, um weitere Informationen zum Auftrag zu erhalten.

    * Wenn die Eskalationsanforderungen erfüllt sind, ist die Antwortbedingung TRUE. Daher wird die Bestellung verarbeitet.

    * Lautet die Antwortbedingung FALSE, wird eine E-Mail bezüglich des Problems gesendet.

:::image type="content" source="./media/logic-apps-overview/example-enterprise-workflow.png" alt-text="Screenshot, der den Workflow-Designer und einen Beispielworkflow für Unternehmen zeigt, der Optionen und Bedingungen verwendet" lightbox="./media/logic-apps-overview/example-enterprise-workflow.png":::

Workflows können visuell mithilfe des Designers für Logik-Apps im Azure-Portal, in Visual Studio Code oder in Visual Studio erstellt werden. Jeder Workflow verfügt auch über eine zugrunde liegende Definition, die mithilfe von JavaScript Object Notation (JSON) beschrieben wird. Wenn Sie möchten, können Sie Workflows bearbeiten, indem Sie diese JSON-Definition ändern. Für einige Erstellungs- und Verwaltungsaufgaben werden von Logic Apps auch Azure PowerShell- und Azure CLI-Befehle unterstützt. Für die automatisierte Bereitstellung werden von Logic Apps Azure Resource Manager-Vorlagen unterstützt.

<a name="resource-environment-differences"></a>

## <a name="resource-type-and-host-environment-differences"></a>Unterschiede zwischen Ressourcentyp und Hostumgebung

Um Logic-App-Workflows zu erstellen, wählen Sie den **Logic App**-Ressourcentyp basierend auf Ihrem Szenario, den Lösungsanforderungen, den gewünschten Funktionen und der Umgebung, in der Sie Ihre Workflows ausführen möchten.

Die folgende Tabelle fasst die Unterschiede zwischen dem ursprünglichen Ressourcentyp **Logic App (Consumption)** und dem Ressourcentyp **Logic App (Standard)** kurz zusammen. Außerdem erfahren Sie, wie das *Einzel-Mandanten*-Modell im Vergleich zu den *Multi-Mandanten*- und *Integration Service Environment (ISE)* -Modellen für die Bereitstellung, das Hosting und die Ausführung Ihrer Logik-App-Workflows funktioniert.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

## <a name="why-use-logic-apps"></a>Gründe für die Verwendung von Logic Apps

Die Logic Apps-Integrationsplattform bietet vordefinierte, von Microsoft verwaltete API-Connectors sowie integrierte Vorgänge, sodass Sie Apps, Daten, Dienste und Systeme einfacher und schneller verbinden und integrieren können. Sie können sich stärker auf das Entwerfen und Implementieren der Geschäftslogik und -funktionalität Ihrer Lösung konzentrieren, anstatt sich damit zu beschäftigen, wie der Zugriff auf Ihre Ressourcen erfolgen kann.

Normalerweise müssen Sie keinerlei Code schreiben. Wenn Sie jedoch Code schreiben müssen, können Sie mithilfe von [Azure Functions](../azure-functions/functions-overview.md) Codeausschnitte erstellen und diesen Code aus Ihrem Workflow ausführen. Sie können auch Codeausschnitte erstellen, die in Ihrem Workflow ausgeführt werden, indem Sie die [**Inlinecode**-Aktion](logic-apps-add-run-inline-code.md) verwenden. Wenn Ihr Workflow mit Ereignissen von Azure-Diensten, benutzerdefinierten Apps oder anderen Lösungen interagieren muss, können Sie Ereignisse mithilfe von [Azure Event Grid](../event-grid/overview.md) überwachen, weiterleiten und veröffentlichen.

Logic Apps wird vollständig von Microsoft Azure verwaltet, sodass Sie sich keine Gedanken über das Hosten, Skalieren, Verwalten, Überwachen und Pflegen Ihrer mit diesen Diensten erstellten Lösungen machen müssen. Wenn Sie diese Funktionen verwenden, um [„serverlose“" Apps und Lösungen](../logic-apps/logic-apps-serverless-overview.md) zu erstellen, können Sie sich ganz auf die Geschäftslogik und -funktionalität konzentrieren. Für diese Dienste wird eine automatische Skalierung durchgeführt, um Ihre Anforderungen zu erfüllen, Integrationen zu beschleunigen und Sie beim Erstellen von stabilen Cloud-Apps mit wenig bis gar keinem Code zu unterstützen.

Lesen Sie diese [Kundenberichte](https://aka.ms/logic-apps-customer-stories), um zu erfahren, wie andere Unternehmen ihre Flexibilität verbessert haben und sich stärker auf ihr Kerngeschäft konzentrieren können, indem sie Logic Apps mit anderen Azure-Diensten und Microsoft-Produkten kombiniert haben.

Die folgenden Abschnitte enthalten weitere Informationen zu den Funktionen und Vorteilen in Logic Apps:

#### <a name="visually-create-and-edit-workflows-with-easy-to-use-tools"></a>Visuelles Erstellen und Bearbeiten von Workflows mit benutzerfreundlichen Tools

Einsparen von Zeit und Vereinfachen komplexer Prozesse durch Verwendung der visuellen Entwurfstools in Logic Apps. Erstellen Ihrer Workflows von Anfang bis Ende durch Verwendung des Designers für Logik-Apps im Azure-Portal, in Visual Studio Code oder in Visual Studio. Starten Sie Ihren Workflow einfach mit einem Trigger, und fügen Sie eine beliebige Anzahl von Aktionen über den [Katalog mit Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) hinzu.

Wenn Sie eine Multi-Mandanten-basierende Logik-App erstellen, können Sie schneller beginnen, wenn Sie einen [Workflow aus der Vorlagengalerie erstellen](../logic-apps/logic-apps-create-logic-apps-from-templates.md). Diese Vorlagen sind für gängige Workflowmuster verfügbar und reichen von einfacher Konnektivität für SaaS-Apps (Software-as-a-Service) bis zu erweiterten B2B-Lösungen und „Just-for-fun“-Vorlagen.

#### <a name="connect-different-systems-across-various-environments"></a>Verbinden von unterschiedlichen Systemen über verschiedene Umgebungen hinweg

Einige Muster und Prozesse sind leicht zu beschreiben, lassen sich im Code aber nur schwer implementieren. Die Logic Apps-Plattform unterstützt Sie dabei, unterschiedliche Systeme nahtlos über Cloud-, lokale und Hybridumgebungen hinweg zu verbinden. Beispielsweise können Sie eine Cloudmarketinglösung mit einem lokalen Abrechnungssystem verbinden oder das Messaging für APIs und Systeme mittels Azure Service Bus zentralisieren. Logic Apps stellt eine schnelle, zuverlässige und einheitliche Möglichkeit zum Bereitstellen von wiederverwendbaren und rekonfigurierbaren Lösungen für diese Szenarien dar.

#### <a name="write-once-reuse-often"></a>Einmal schreiben, häufig wiederverwenden

Erstellen Sie Ihre Logik-Apps als Azure Resource Manager-Vorlagen, sodass Sie Bereitstellungen über mehrere Umgebungen und Regionen hinweg [einrichten und automatisieren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) können.

#### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>Erstklassige Unterstützung für Unternehmensintegration und B2B-Szenarien

Unternehmen und Organisationen kommunizieren auf elektronischem Wege über Nachrichtenprotokolle und -formate miteinander, die zwar alle dem Branchenstandard entsprechen, aber unterschiedlicher Art sein können, z.B. EDIFACT, AS2, X12 und RosettaNet. Mithilfe der von Logic Apps unterstützten [Unternehmensintegrationsfunktionen](../logic-apps/logic-apps-enterprise-integration-overview.md) können Sie Workflows erstellen, die von Handelspartnern verwendete Nachrichtenformate in Formate transformieren, die die Systeme Ihrer Organisation interpretieren und verarbeiten können. Mit Logic Apps wird dieser Nachrichtenaustausch reibungslos und sicher durchgeführt, indem Verschlüsselungen und digitale Signaturen verwendet werden.

Sie können mit Ihren aktuellen Systemen und Diensten klein beginnen und dann Schritt für Schritt in Ihrem persönlichen Tempo wachsen. Wenn Sie bereit sind, hilft Ihnen die Logic Apps-Plattform beim Implementieren und Hochskalieren auf anspruchsvollere Integrationsszenarien, indem diese und weitere Funktionen genutzt werden:

* Integrieren und Erstellen mit [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server), [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), [Azure Functions](../azure-functions/functions-overview.md), [Azure API Management](../api-management/api-management-key-concepts.md) und mehr.

* Austauschen von Nachrichten mithilfe der Protokolle [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md), [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](../logic-apps/logic-apps-enterprise-integration-x12.md) und [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* Verarbeiten von [XML-Nachrichten](../logic-apps/logic-apps-enterprise-integration-xml.md) und [Flatfiles](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Erstellen eines [Integrationskontos](./logic-apps-enterprise-integration-create-integration-account.md), um B2B-Artefakte wie [Handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md), [Vereinbarungen](../logic-apps/logic-apps-enterprise-integration-agreements.md), [Transformationszuordnungen](../logic-apps/logic-apps-enterprise-integration-maps.md), [Validierungsschemas](../logic-apps/logic-apps-enterprise-integration-schemas.md) und mehr zu speichern und zu verwalten.

Wenn Sie beispielsweise Microsoft BizTalk Server nutzen, können Ihre Workflows über den [BizTalk Server-Connector](../connectors/managed.md#on-premises-connectors) mit Ihrer BizTalk Server-Instanz kommunizieren. Danach können Sie dann mithilfe von [Integrationskonto-Connectors](../connectors/managed.md#integration-account-connectors) BizTalk-ähnliche Vorgänge in Ihrem Workflow erweitern oder ausführen. In der anderen Richtung kann BizTalk Server unter Verwendung des [Microsoft BizTalk Server Adapter für Logic Apps](https://www.microsoft.com/download/details.aspx?id=54287) mit Ihren Workflows kommunizieren. Informieren Sie sich darüber, wie Sie für Ihre BizTalk Server-Instanz den [BizTalk Server Adapter einrichten und nutzen](/biztalk/core/logic-app-adapter).

#### <a name="built-in-extensibility"></a>Integrierte Erweiterbarkeit

Wenn kein geeigneter Connector zum Ausführen des gewünschten Codes verfügbar ist, können Sie Ihre eigenen Codeausschnitte erstellen und aus Ihrem Workflow aufrufen, indem Sie [Azure Functions](../azure-functions/functions-overview.md) verwenden. Alternativ können Sie Ihre eigenen [APIs](../logic-apps/logic-apps-create-api-app.md) und [benutzerdefinierten Connectors](../logic-apps/custom-connector-overview.md) erstellen, die Sie aus Ihren Workflows aufrufen können.

#### <a name="access-resources-inside-azure-virtual-networks"></a>Zugreifen auf Ressourcen in virtuellen Azure-Netzwerken

Workflows von Logik-Apps können auf geschützte Ressourcen zugreifen, z. B. virtuelle Computer (VMs) und andere Systeme oder Dienste, die sich in einem [virtuellen Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) befinden, wenn Sie eine [*Integrationsdienstumgebung* (Integration Service Environment, ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erstellen. Bei einer ISE handelt es sich um eine dedizierte Instanz des Azure Logic Apps-Dienstes, die dedizierte Ressourcen nutzt und getrennt vom globalen Multi-Mandantenfähigen Azure Logic Apps-Dienst ausgeführt wird.

Das Ausführen von Logik-Apps als eigene dedizierte Instanz trägt dazu bei, mögliche Auswirkungen anderer Azure-Mandanten auf die Leistung von Apps zu verringern. Dies ist auch als [„Noisy Neighbors“-Problem](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) bekannt. Eine ISE bietet darüber hinaus die folgenden Vorteile:

* Ihre eigenen statischen IP-Adressen. Diese sind gesondert von den statischen IP-Adressen, die von den Logik-Apps im mehrinstanzenfähigen Dienst gemeinsam verwendet werden. Sie können auch eine einzelne öffentliche, statische und vorhersagbare ausgehende IP-Adresse für die Kommunikation mit Zielsystemen einrichten. Auf diese Weise müssen Sie nicht für jede ISE an den Zielsystemen extra Firewallzugänge einrichten.

* Höhere Grenzwerte für Ausführungsdauer, Speicheraufbewahrung, Durchsatz, Zeitlimits für HTTP-Anforderungen und -Antworten, Nachrichtengröße und benutzerdefinierte Connectoranforderungen. Weitere Informationen finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

Wenn Sie eine ISE erstellen, *injiziert* Azure diese ISE bzw. stellt sie in Ihrem virtuellen Azure-Netzwerk bereit. Anschließend können Sie diese ISE als Speicherort für Logik-Apps und Integrationskonten verwenden, die Zugriff benötigen. Weitere Informationen zum Erstellen einer ISE finden Sie unter [Verbinden mit Azure Virtual Networks über Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

#### <a name="pricing-options"></a>Preisoptionen

Jeder Logik-App-Typ, der sich hinsichtlich der Funktionen und wo er ausgeführt wird (mehrinstanzenfähig, einzelinstanzenfähig, Integrationsdienstumgebung) unterscheidet, verfügt über ein anderes [Preismodell](../logic-apps/logic-apps-pricing.md). Zum Beispiel verwenden Logik-Apps auf Multi-Mandanten-Basis Verbrauchspreise, während Logik-Apps in einer Integrationsdienstumgebung Festpreise verwenden. Erfahren Sie mehr über [Preise und Verbrauchseinheiten](../logic-apps/logic-apps-pricing.md) für Logic Apps.

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>Wie unterscheidet sich Logic Apps von Functions, WebJobs und Power Automate?

All diese Diensten helfen Ihnen, unterschiedliche Systeme zu verbinden und zusammenzuführen. Jeder Dienst hat bestimmte Vorteile, sodass eine Kombination der jeweiligen Funktionen die beste Möglichkeit darstellt, schnell ein skalierbares, Integrationssystem mit vollem Funktionsumfang zu erstellen. Weitere Informationen finden Sie unter [Wählen zwischen Logic Apps, Functions, WebJobs und Power Automate](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="get-started"></a>Erste Schritte

Bevor Sie mit Azure Logic Apps beginnen können, benötigen Sie ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

Wenn Sie bereit sind, probieren Sie eine oder mehrere der folgenden Schnellstartanleitungen für Logic Apps aus. Dort erfahren Sie, wie Sie einen grundlegenden Workflow erstellen, der einen RSS-Feed überwacht und eine E-Mail für neue Inhalte sendet.

* [Erstellen einer Logik-App mit mehreren Mandanten im Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* [Erstellen einer Logik-App mit mehreren Mandanten in Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)

* [Erstellen einer Logik-App mit mehreren Mandanten in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)

Gegebenenfalls sollten Sie auch andere Schnellstartanleitungen für Logic Apps lesen:

* [Erstellen einer Logik-App mit mehreren Mandanten mithilfe einer ARM-Vorlage](quickstart-create-deploy-azure-resource-manager-template.md)

* [Erstellen einer Logik-App mit mehreren Mandanten mithilfe der Azure CLI](quickstart-create-deploy-azure-resource-manager-template.md)

## <a name="other-resources"></a>Weitere Ressourcen

Informieren Sie sich in diesen Einführungsvideos eingehender über die Logic Apps-Plattform:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Go-serverless-Enterprise-integration-with-Azure-Logic-Apps/player]
>
> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Connect-and-extend-your-mainframe-to-the-cloud-with-Logic-Apps/player]

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen Ihres ersten Logik-App-Workflows](../logic-apps/quickstart-create-first-logic-app-workflow.md)
