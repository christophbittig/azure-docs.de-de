---
title: DevOps-Bereitstellung für Azure Logic Apps-Instanzen mit nur einem Mandanten
description: Erfahren Sie mehr über die DevOps-Bereitstellung für Azure Logic Apps-Instanzen mit nur einem Mandanten.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: a94abb3b2c640dbd0bcd372e83844d660af514cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548190"
---
# <a name="devops-deployment-for-single-tenant-azure-logic-apps"></a>DevOps-Bereitstellung für Azure Logic Apps-Instanzen mit nur einem Mandanten

Im Zuge des Trends zu verteilten und nativen Cloud-Apps haben Organisationen mit mehr verteilten Komponenten in mehr Umgebungen zu tun. Um die Kontrolle und Konsistenz zu gewährleisten, können Sie mithilfe von DevOps-Tools und -Prozessen Ihre Umgebungen automatisieren und schneller und sicherer mehr Komponenten bereitstellen.

Dieser Artikel bietet eine Einführung und eine Übersicht zur aktuellen CI/CD-Benutzeroberfläche (Continuous Integration und Continuous Deployment) für Azure Logic Apps-Instanzen mit nur einem Mandanten.

<a name="single-tenant-versus-multi-tenant"></a>

## <a name="single-tenant-versus-multi-tenant"></a>Mit nur einem Mandanten im Vergleich zu mehrinstanzenfähig

In *mehrinstanzenfähigen* Azure Logic Apps-Instanzen hängt die Ressourcenbereitstellung von ARM-Vorlagen (Azure Resource Manager) ab, die die Ressourcenbereitstellung für Logik-Apps und die Infrastruktur kombinieren und verarbeiten. In Azure Logic Apps-Instanzen *mit nur einem Mandanten* wird die Bereitstellung einfacher, da Sie die Ressourcenbereitstellung zwischen Apps und der Infrastruktur trennen können.

Wenn Sie Logik-Apps mithilfe des Ressourcentyps **Logik-App (Standard)** erstellen, werden Ihre Workflows von der neu gestalteten Azure Logic Apps-Runtime mit nur einem Mandanten unterstützt. Diese Runtime wendet die Erweiterbarkeit mithilfe des [Azure Functions-Erweiterbarkeitsmodells](../azure-functions/functions-bindings-register.md) an und wird [als Erweiterung der Azure Functions-Runtime gehostet](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564). Dieser Entwurf bietet Portabilität, Flexibilität und eine höhere Leistung für Ihre Logik-Apps sowie weitere Funktionen und Vorteile der Azure Functions-Plattform und des Azure App Service-Ökosystems.

Beispielsweise können Sie die neu gestaltete Containerruntime und die Workflows als Teil Ihrer Logik-App zusammen verpacken. Sie können mit allgemeinen Schritten oder Aufgaben Ihre Logik-App-Ressourcen erstellen, zusammenstellen und in bereitstellungsbereite Artefakte zippen. Kopieren Sie zum Bereitstellen Ihrer Apps die Artefakte in die Hostumgebung. Starten Sie anschließend die Apps, um Ihre Workflows auszuführen. Alternativ können Sie Ihre Artefakte mithilfe der Tools und Prozesse, die Sie bereits kennen und verwenden, in Bereitstellungspipelines integrieren. Wenn Ihr Szenario beispielsweise Container erfordert, können Sie Ihre Logik-Apps containerisieren und in Ihre vorhandenen Pipelines integrieren.

Zum Einrichten und Bereitstellen Ihrer Infrastrukturressourcen, z. B. virtuelle Netzwerke und Konnektivität, können Sie weiterhin ARM-Vorlagen verwenden und diese Ressourcen zusammen mit anderen Prozessen und Pipelines, die Sie für diese Zwecke verwenden, separat bereitstellen.

Mit Verwendung von Standardoptionen für Build und Bereitstellung können Sie sich getrennt von der Infrastrukturbereitstellung auf die App-Entwicklung konzentrieren. So erhalten Sie ein allgemeineres Projektmodell, in dem Sie viele ähnliche oder identische Bereitstellungsoptionen wie für eine generische App anwenden können. Außerdem profitieren Sie von einer konsistenteren Umgebung zum Erstellen von Bereitstellungspipelines für Ihre App-Projekte und zum Ausführen der erforderlichen Tests und Überprüfungen vor der Veröffentlichung in der Produktion. Welchen Technologiestapel Sie auch verwenden, Sie können Logik-Apps mit den Tools Ihrer Wahl bereitstellen.

<a name="devops-deployment-features"></a>

## <a name="devops-deployment-capabilities"></a>DevOps-Bereitstellungsfunktionen

Azure Logic Apps-Instanzen mit nur einem Mandanten erben viele Funktionen und Vorteile von der Azure Functions-Plattform und dem Azure App Service-Ökosystem. Diese Updates bieten ein völlig neues Bereitstellungsmodell und weitere Möglichkeiten, DevOps für Ihre Logik-App-Workflows zu verwenden.

<a name="local-development-testing"></a>

### <a name="local-development-and-testing"></a>Lokale Entwicklung und Tests

Wenn Sie Visual Studio Code mit der Erweiterung Azure Logic Apps (Standard) verwenden, können Sie auf einem einzelnen Mandanten basierende Logik-App-Workflows lokal in Ihrer Entwicklungsumgebung entwickeln, erstellen und ausführen, ohne eine Bereitstellung in Azure durchführen zu müssen. Sie können Ihre Workflows überall dort ausführen, wo auch Azure Functions ausgeführt werden kann. Erfordert Ihr Szenario z. B. Container, können Sie Logik-Apps containerisieren und als Container bereitstellen.

Verglichen mit dem mehrinstanzenfähigen Modell, bei dem Sie für eine vorhandene, in Azure ausgeführte Ressource entwickeln müssen, bietet diese Funktion eine erhebliche Verbesserung und einen entscheidenden Vorteil.

<a name="separate-concerns"></a>

### <a name="separate-concerns"></a>Separate Aspekte

Das Modell mit nur einem Mandanten bietet Ihnen die Möglichkeit, die Aspekte der App und der zugrunde liegenden Infrastruktur zu trennen. Beispielsweise können Sie Ihre App separat als unveränderliches Artefakt in verschiedenen Umgebungen entwickeln, erstellen, zippen und bereitstellen. Logik-App-Workflows verfügen in der Regel über „Anwendungscode“, den Sie häufiger aktualisieren als die zugrunde liegende Infrastruktur. Indem Sie diese Ebenen trennen, können Sie sich mehr auf die Erstellung des Workflows Ihrer Logik-App konzentrieren und müssen weniger Arbeit in die Bereitstellung der erforderlichen Ressourcen in mehreren Umgebungen investieren.

![Konzeptionelles Diagramm mit separaten Bereitstellungspipelines für Apps und Infrastruktur.](./media/devops-deployment-single-tenant/deployment-pipelines-logic-apps.png)

<a name="resource-structure"></a>

### <a name="logic-app-resource-structure"></a>Ressourcenstruktur einer Logik-App

[!INCLUDE [Visual Studio Code - logic app resource structure](../../includes/logic-apps-single-tenant-resource-structure.md)]

### <a name="logic-app-project-structure"></a>Projektstruktur einer Logik-App

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="deployment-containers"></a>

### <a name="container-deployment"></a>Containerbereitstellung

Azure Logic Apps-Instanzen mit nur einem Mandanten unterstützen die Bereitstellung in Containern. Dies bedeutet, dass Sie Ihre Logik-App-Workflows containerisieren und überall dort ausführen können, wo Container ausgeführt werden können. Nach dem Containerisieren Ihrer App funktioniert die Bereitstellung größtenteils wie jeder andere Container, den Sie bereitstellen und verwalten.

Beispiele mit Azure DevOps finden Sie unter [CI/CD für Container](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/).

<a name="app-settings-parameters"></a>

### <a name="app-settings-and-parameters"></a>App-Einstellungen und -Parameter

In mehrinstanzenfähigen Azure Logic Apps-Instanzen stellen ARM-Vorlagen eine Herausforderung dar, wenn Sie Umgebungsvariablen für Logik-Apps in verschiedenen Entwicklungs-, Test- und Produktionsumgebungen verwalten müssen. Alles in einer ARM-Vorlage wird bei der Bereitstellung definiert. Wenn Sie nur eine einzelne Variable ändern müssen, müssen Sie alles erneut bereitstellen.

In Azure Logic Apps-Instanzen mit nur einem Mandanten können Sie Ihre Umgebungsvariablen zur Runtime aufrufen und darauf verweisen, indem Sie App-Einstellungen und -Parameter verwenden, sodass Sie nicht so oft erneut bereitstellen müssen.

<a name="managed-connectors-built-in-operations"></a>

## <a name="managed-connectors-and-built-in-operations"></a>Verwaltete Connectors und integrierte Vorgänge

Das Azure Logic Apps-Ökosystem bietet [Hunderte von Microsoft verwaltete Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) und integrierte Vorgänge als Teil einer ständig wachsenden Sammlung, die Sie in Azure Logic Apps-Instanzen mit nur einem Mandanten verwenden können. Die Art und Weise, wie Microsoft diese Connectors und integrierten Vorgänge verwaltet, bleibt in Azure Logic Apps-Instanzen mit nur einem Mandanten größtenteils gleich.

Die wichtigste Verbesserung ist, dass der Einzelmandantendienst beliebte verwaltete Connectors auch als integrierte Vorgänge zur Verfügung stellt. So können Sie etwa integrierte Vorgänge für Azure Service Bus, Azure Event Hubs, SQL usw. nutzen. In der Zwischenzeit sind die verwalteten Connectorversionen weiterhin verfügbar und funktionieren.

Die Verbindungen, die Sie mit integrierten Vorgängen erstellen, werden als integrierte Verbindungen oder *Dienstanbieterverbindungen* bezeichnet. Integrierte Vorgänge und ihre Verbindungen sowie Ihre Workflows werden lokal in demselben Prozess ausgeführt. Beide werden auf der neu gestalteten Logic Apps-Runtime gehostet. Im Gegensatz dazu werden verwaltete Verbindungen oder API-Verbindungen separat als Azure-Ressourcen erstellt und ausgeführt, die Sie mithilfe von ARM-Vorlagen bereitstellen. Aufgrund der Nähe zu Ihren Workflows bieten integrierte Vorgänge und deren Verbindungen eine bessere Leistung. Diese Methode ist auch für Bereitstellungspipelines geeignet, da die Dienstanbieterverbindungen in dasselbe Buildartefakt gepackt werden.

Wenn Sie in Visual Studio Code mit dem Designer Ihre Workflows entwickeln oder Änderungen an ihnen vornehmen, generiert die Logic Apps-Engine automatisch alle erforderlichen Verbindungsmetadaten in der Datei **connections.json** Ihres Projekts. In den folgenden Abschnitten werden die drei Arten von Verbindungen beschrieben, die Sie in Ihren Workflows erstellen können. Jeder Verbindungstyp hat eine andere JSON-Struktur. Es ist wichtig, dies zu verstehen, da Endpunkte sich ändern, wenn Sie zwischen Umgebungen wechseln.

<a name="service-provider-connections"></a>

### <a name="service-provider-connections"></a>Dienstanbieterverbindungen

Wenn Sie einen integrierten Vorgang für einen Dienst wie Azure Service Bus oder Azure Event Hubs in Azure Logic Apps-Instanzen mit nur einem Mandanten verwenden, erstellen Sie eine Dienstanbieterverbindung, die im selben Prozess wie Ihr Workflow ausgeführt wird. Diese Verbindungsinfrastruktur wird als Teil Ihrer Logik-App-Ressource gehostet und verwaltet, und Ihre App-Einstellungen speichern die Verbindungszeichenfolgen für jeden integrierten Vorgang auf Dienstanbieterbasis, den Ihre Workflows verwenden.

In Ihrem Logik-App-Projekt verfügt jeder Workflow über eine workflow.json-Datei, die die zugrunde liegende JSON-Definition des Workflows enthält. Diese Workflowdefinition verweist dann auf die erforderlichen Verbindungszeichenfolgen in der Datei „connections.json“ des Projekts.

Das folgende Beispiel zeigt, wie die Dienstanbieterverbindung für einen integrierten Service Bus-Vorgang in der connections.json-Datei Ihres Projekts aussieht:

```json
"serviceProviderConnections": {
   "{service-bus-connection-name}": {
      "parameterValues": {
         "connectionString": "@appsetting('servicebus_connectionString')"
      },
      "serviceProvider": {
         "id": "/serviceProviders/serviceBus"
      },
      "displayName": "{service-bus-connection-name}"
   },
   <...>
}
```

<a name="managed-connections"></a>

### <a name="managed-connections"></a>Verwaltete Verbindungen

Wenn Sie einen verwalteten Connector zum ersten Mal in Ihrem Workflow verwenden, werden Sie aufgefordert, eine verwaltete API-Verbindung für den Zieldienst oder das Zielsystem zu erstellen und Ihre Identität zu authentifizieren. Diese Connectors werden vom Ökosystem für freigegebene Connectors in Azure verwaltet. Die API-Verbindungen sind vorhanden und werden als separate Ressourcen in Azure ausgeführt.

In Visual Studio Code erstellt die Logic Apps-Engine automatisch für die verwalteten Connectors in Ihrem Workflow die erforderlichen Ressourcen in Azure, während Sie Ihren Workflow weiterhin mit dem Designer erstellen und entwickeln. Die Engine fügt diese Verbindungsressourcen automatisch der Azure-Ressourcengruppe hinzu, die Sie für Ihre Logik-App entworfen haben.

Das folgende Beispiel zeigt, wie eine API-Verbindung für den verwalteten Service Bus-Connector in der connections.json-Datei Ihres Projekts aussieht:

```json
"managedApiConnections": {
   "{service-bus-connection-name}": { 
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/servicebus"
      },
      "connection": { 
         "id": "/subscriptions/{subscription-ID}/resourcegroups/{resource-group-name}/providers/Microsoft.Web/connections/servicebus"
      }, 
      "connectionRuntimeUrl": "{connection-runtime-URL}",
      "authentication": { 
         "type": "Raw",
         "scheme": "Key",
         "parameter": "@appsetting('servicebus_1-connectionKey')"
      },
   },
   <...>
}
```

<a name="azure-functions-connections"></a>

### <a name="azure-functions-connections"></a>Azure Functions-Verbindungen

Zum Aufrufen in Azure Functions erstellter und gehosteter Funktionen verwenden Sie den integrierten Azure Functions-Vorgang. Verbindungsmetadaten für Azure Functions-Aufrufe unterscheiden sich von denen anderer integrierter Verbindungen. Diese Metadaten werden in der connections.json-Datei Ihres Logik-App-Projekts gespeichert, sehen jedoch anders aus:

```json
"functionConnections": {
   "{function-operation-name}": {
      "function": { 
         "id": "/subscriptions/{subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{function-app-name}/functions/{function-name}"
      },
      "triggerUrl": "{function-url}",
      "authentication": {
        "type": "QueryString",
         "name": "Code",
         "value": "@appsetting('azureFunctionOperation_functionAppKey')"
      }, 
      "displayName": "{functions-connection-display-name}"
   },
   <...>
}
```

## <a name="authentication"></a>Authentifizierung

In Azure Logic Apps-Instanzen mit nur einem Mandanten ist das Hostingmodell für Logik-App-Workflows ein einzelner Mandant, bei dem Ihre Workloads von einer größeren Isolation als beim mehrinstanzenfähigen Modell profitieren. Darüber hinaus ist die Runtime für Azure Logic Apps-Instanzen mit nur einem Mandanten portabel. Das bedeutet, dass Sie Ihre Workflows überall dort ausführen können, wo Azure Functions ausgeführt werden kann. Dennoch erfordert dieser Entwurf, dass Logik-Apps eine Möglichkeit erhalten, ihre Identität zu authentifizieren, damit sie auf das Ökosystem der verwalteten Connectors in Azure zugreifen können. Ihre Apps benötigen auch die richtigen Berechtigungen zum Ausführen von Vorgängen, wenn verwaltete Verbindungen verwendet werden.

Standardmäßig verfügt jede auf einem einzelnen Mandanten basierende Logik-App über eine automatisch aktivierte systemseitig zugewiesene verwaltete Identität. Diese Identität unterscheidet sich von den Anmeldeinformationen für die Authentifizierung oder der Verbindungszeichenfolge, die Sie verwenden, wenn Sie eine Verbindung herstellen. Zur Runtime verwendet Ihre Logik-App diese Identität, um ihre Verbindungen über Azure-Zugriffsrichtlinien zu authentifizieren. Wenn Sie diese Identität deaktivieren, funktionieren Verbindungen zur Laufzeit nicht.

Die folgenden Abschnitte enthalten weitere Informationen zu den Authentifizierungstypen, die Sie zum Authentifizieren verwalteter Verbindungen verwenden können, je nachdem, wo Ihre Logik-App ausgeführt wird. Für jede verwaltete Verbindung verfügt die connections.json-Datei Ihres Logik-App-Projekts über ein `authentication`-Objekt, das den Authentifizierungstyp angibt, den Ihre Logik-App zum Authentifizieren dieser verwalteten Verbindung verwenden kann.

### <a name="managed-identity"></a>Verwaltete Identität

Für eine in Azure gehostete und ausgeführte Logik-App ist eine [verwaltete Identität](create-managed-service-identity.md) der standardmäßige und empfohlene Authentifizierungstyp zur Authentifizierung in Azure gehosteter und ausgeführter verwalteter Verbindungen. In der connections.json-Datei Ihres Logik-App-Projekts verfügt die verwaltete Verbindung über ein `authentication`-Objekt, das `ManagedServiceIdentity` als Authentifizierungstyp angibt:

```json
"authentication": {
   "type": "ManagedServiceIdentity"
}
```

### <a name="raw"></a>Raw

Für in Ihrer lokalen Entwicklungsumgebung mithilfe von Visual Studio Code ausgeführte Logik-Apps werden unformatierte Authentifizierungsschlüssel zur Authentifizierung in Azure gehosteter und ausgeführter verwalteter Verbindungen verwendet. Diese Schlüssel sind nur für die Entwicklung und nicht für die Produktion konzipiert und laufen nach sieben Tagen ab. In der connections.json-Datei Ihres Logik-App-Projekts verfügt die verwaltete Verbindung über ein `authentication`-Objekt, das folgende Authentifizierungsinformationen angibt:

```json
"authentication": {
   "type": "Raw", 
   "scheme": "Key", 
   "parameter": "@appsetting('connectionKey')"
 }
```

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten der DevOps-Bereitstellung für Azure Logic Apps-Instanzen mit nur einem Mandanten](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
