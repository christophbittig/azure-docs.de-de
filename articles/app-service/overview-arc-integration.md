---
title: App Service in Azure Arc
description: Eine Einführung in App Service Integration in Azure Arc für Azure-Betreiber.
ms.topic: article
ms.date: 11/02/2021
ms.openlocfilehash: 74cf4063d92aa5d563df881f2210e2ca5d08543e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440353"
---
# <a name="app-service-functions-and-logic-apps-on-azure-arc-preview"></a>App Service, Funktionen und Logic Apps in Azure Arc (Vorschau)

Sie können App Service, Funktionen und Logic Apps auf einem Kubernetes-Cluster mit Azure Arc-Unterstützung ausführen. Der Kubernetes-Cluster kann lokal oder in einer Cloud eines Drittanbieters gehostet werden. Mit diesem Ansatz können App-Entwickler die Funktionen von App Service nutzen. Gleichzeitig können IT-Administratoren die Unternehmenskonformität beibehalten, indem sie die App Service-Apps in der internen Infrastruktur hosten. Außerdem können andere IT-Betreiber ihre früheren Investitionen in andere Cloud-Anbieter schützen, indem sie App Service auf bestehenden Kubernetes-Clustern ausführen.

> [!NOTE]
> Informationen zum Einrichten Ihres Kubernetes-Clusters für App Service, Funktionen und Logic Apps finden Sie unter [Erstellen einer App Service Kubernetes-Umgebung (Vorschau)](manage-create-arc-environment.md).

In den meisten Fällen müssen App-Entwickler nur wissen, wie die Bereitstellung in der richtigen Azure-Region erfolgen soll, die die bereitgestellte Kubernetes-Umgebung darstellt. Operatoren, die die Umgebung bereitstellen und die zugrunde liegende Kubernetes-Infrastruktur verwalten, müssen die folgenden Azure-Ressourcen beachten:

- Der verbundene Cluster, bei dem es sich um eine Azure-Projektion Ihrer Kubernetes-Infrastruktur handelt. Weitere Informationen finden Sie unter [Was ist Kubernetes mit Azure Arc-Unterstützung?](../azure-arc/kubernetes/overview.md).
- Eine Clustererweiterung, die eine Unterressource der verbundenen Clusterressource ist. Die App Service-Erweiterung [installiert die erforderlichen Pods in Ihrem verbundenen Cluster](#pods-created-by-the-app-service-extension). Weitere Informationen zu Clustererweiterungen finden Sie unter [Clustererweiterungen in Kubernetes mit Azure Arc-Unterstützung](../azure-arc/kubernetes/conceptual-extensions.md).
- Ein benutzerdefinierter Speicherort, der eine Gruppe von Erweiterungen bündelt und sie einem Namespace für erstellte Ressourcen zuweist. Weitere Informationen finden Sie unter [„Benutzerdefinierte Speicherorte“ auf Basis von Kubernetes mit Azure Arc-Unterstützung](../azure-arc/kubernetes/conceptual-custom-locations.md).
- Eine App Service Kubernetes-Umgebung, die die App-übergreifende Konfiguration ermöglicht, aber nicht im Zusammenhang mit Clustervorgängen steht. Konzeptionell wird sie in der benutzerdefinierten Standortressource bereitgestellt, und App-Entwickler erstellen Apps in dieser Umgebung. Dies wird in [App Service Kubernetes-Umgebung](#app-service-kubernetes-environment) ausführlicher beschrieben.

## <a name="public-preview-limitations"></a>Einschränkungen der öffentlichen Vorschauversion

Die folgenden Einschränkungen für die öffentliche Vorschau gelten für App Service Kubernetes-Umgebungen. Sie werden aktualisiert, wenn Änderungen verfügbar gemacht werden.

| Einschränkung                                              | Details                                                                               |
|---------------------------------------------------------|---------------------------------------------------------------------------------------|
| Unterstützte Azure-Regionen                                 | Ost-USA, Europa, Westen                                                                  |
| Clusternetzwerkanforderung                          | Muss den `LoadBalancer`-Diensttyp unterstützen und eine öffentlich adressierbare statische IP-Adresse bereitstellen. |
| Clusterspeicheranforderung                             | Muss über eine an den Cluster angefügte Speicherklasse verfügen, die von der Erweiterung verwendet werden kann, um die Bereitstellung und ggf. den Build von codebasierten Apps zu unterstützen.                      |
| Funktion: Netzwerk                                     | [Nicht verfügbar (angewiesen auf Clusternetzwerke)](#are-networking-features-supported)      |
| Funktion: Verwaltete Identitäten                             | [Nicht verfügbar](#are-managed-identities-supported)                                    |
| Funktion: Key Vault-Verweise                           | Nicht verfügbar (abhängig von verwalteten Identitäten)                                         |
| Funktion: Images aus ACR mit verwalteter Identität herausziehen     | Nicht verfügbar (abhängig von verwalteten Identitäten)                                         |
| Funktion: Portal-Bearbeitung für Funktionen und Logic Apps | Nicht verfügbar                                                                         |
| Funktion: FTP-Veröffentlichung                                 | Nicht verfügbar                                                                         |
| Protokolle                                                    | Log Analytics muss mit der Clustererweiterung konfiguriert werden. nicht pro Standort                 |

## <a name="pods-created-by-the-app-service-extension"></a>Pods, die von dem App Service erstellt werden

Wenn die App Service-Erweiterung auf dem Kubernetes-Cluster mit Azure Arc-Unterstützung installiert ist, sehen Sie mehrere Pods, die in dem angegebenen Releasenamespace erstellt wurden. Diese Pods ermöglichen es Ihrem Kubernetes-Cluster, eine Erweiterung des `Microsoft.Web`-Ressourcenanbieters in Azure zu sein und die Verwaltung und den Betrieb Ihrer Apps zu unterstützen. Optional können Sie festlegen, dass die Erweiterung [KEDA](https://keda.sh/) für die ereignisgesteuerte Skalierung installiert.
 <!-- You can only have one installation of KEDA on the cluster. If you have one already, you must disable this behavior during installation of the cluster extension `TODO`. -->

In der folgenden Tabelle wird die Rolle der einzelnen Pods beschrieben, die standardmäßig erstellt werden:

| Pod                                   | Beschreibung                                                                                                                       |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| `<extensionName>-k8se-app-controller` | Der Kern-Operatorpod, der Ressourcen im Cluster erstellt und den Zustand der Komponenten verwaltet.                                |
| `<extensionName>-k8se-envoy`          | Eine Front-End-Proxy-Ebene für alle Anforderungen auf Datenebene. Es leitet den eingehenden Datenverkehr an die richtigen Apps weiter.                           |
| `<extensionName>-k8se-activator`      | Ein alternatives Routing-Ziel zur Unterstützung von Apps, die auf 0 (null) skaliert wurden, während das System die erste verfügbare Instanz erhält. |
| `<extensionName>-k8se-build-service`  | Unterstützt Bereitstellungsvorgänge und stellt das [Erweiterte Tools-Funktion](resources-kudu.md) zur Verfügung.                                        |
| `<extensionName>-k8se-http-scaler`    | Überwacht das eingehende Anforderungsvolumen, um Skalierungsinformationen für [KEDA](https://keda.sh) bereitzustellen.                               |
| `<extensionName>-k8se-img-cacher`     | Zieht Platzhalter- und App-Images in einen lokalen Cache auf dem Knoten.                                                                  |
| `<extensionName>-k8se-log-processor`  | Erfasst Protokolle von Apps und anderen Komponenten und sendet sie an Log Analytics.                                                      |
| `placeholder-azure-functions-*`       | Wird verwendet, um Kaltstarts für Azure Functions zu beschleunigen.                                                                                 |

## <a name="app-service-kubernetes-environment"></a>App Service Kubernetes-Umgebung

Die App Service Kubernetes-Umgebungsressource ist erforderlich, bevor Apps erstellt werden können. Sie ermöglicht die Konfiguration, die für Apps am benutzerdefinierten Speicherort üblich ist, z. B. das DNS-Standardsuffix.

An einem benutzerdefinierten Speicherort kann nur eine Kubernetes-Umgebungsressource erstellt werden. In den meisten Fällen muss ein Entwickler, der Apps erstellt und bereitstellt, die Ressource nicht direkt kennen. Sie kann direkt aus der angegebenen benutzerdefinierten Speicherort-ID abgeleitet werden. Wenn Sie jedoch Azure Resource Manager Vorlagen definieren, muss jede Plan-Ressource direkt auf die Ressourcen-ID der Umgebung verweisen. Die benutzerdefinierten Speicherortwerte des Plans und der angegebenen Umgebung müssen übereinstimmen.

## <a name="faq-for-app-service-functions-and-logic-apps-on-azure-arc-preview"></a>FAQs zu App Service, Funktionen und Logic Apps in Azure Arc (Vorschau)

- [Wie viel kostet es?](#how-much-does-it-cost)
- [Werden Sowohl Windows- als auch Linux-Apps unterstützt?](#are-both-windows-and-linux-apps-supported)
- [Welche integrierten Anwendungsstapel werden unterstützt?](#which-built-in-application-stacks-are-supported)
- [Werden alle App-Bereitstellungstypen unterstützt?](#are-all-app-deployment-types-supported)
- [Welche App Service Funktionen werden unterstützt?](#which-app-service-features-are-supported)
- [Werden Netzwerkfunktionen unterstützt?](#are-networking-features-supported)
- [Werden verwaltete Identitäten unterstützt?](#are-managed-identities-supported)
- [Gibt es Grenzen in Bezug auf die Skalierung?](#are-there-any-scaling-limits)
- [Welche Protokolle werden gesammelt?](#what-logs-are-collected)
- [Was kann ich tun, wenn ein Anbieterregistrierungsfehler angezeigt wird?](#what-do-i-do-if-i-see-a-provider-registration-error)
- [Kann ich die Anwendungsdiensterweiterung in einem ARM64-basierten Cluster bereitstellen?](#can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster)

### <a name="how-much-does-it-cost"></a>Wie viel kostet es?

App Service auf Azure Arc ist während der öffentlichen Vorschau kostenlos.

### <a name="are-both-windows-and-linux-apps-supported"></a>Werden Sowohl Windows- als auch Linux-Apps unterstützt?

Es werden nur Linux-basierte Apps unterstützt, sowohl Code als auch benutzerdefinierte Container. Windows-Apps werden nicht unterstützt.

### <a name="which-built-in-application-stacks-are-supported"></a>Welche integrierten Anwendungsstapel werden unterstützt?

Alle integrierten Linux-Stapel werden unterstützt.

### <a name="are-all-app-deployment-types-supported"></a>Werden alle App-Bereitstellungstypen unterstützt?

Die FTP Bereitstellung wird nicht unterstützt. Zu diesem Zeitpunkt wird `az webapp up` ebenfalls nicht unterstützt. Andere Bereitstellungsmethoden werden unterstützt, einschließlich Git, ZIP, CI/CD, Visual Studio und Visual Studio Code.

### <a name="which-app-service-features-are-supported"></a>Welche App Service Funktionen werden unterstützt?

Während des Vorschauzeitraums werden bestimmte App Service Funktionen überprüft. Wenn sie unterstützt werden, werden ihre linken Navigationsoptionen im Azure-Portal aktiviert. Features, die noch nicht unterstützt werden, bleiben ausgegraut.

### <a name="are-networking-features-supported"></a>Werden Netzwerkfunktionen unterstützt?

Nein. Netzwerkfunktionen wie Hybridverbindungen, Virtual Network Integration oder IP-Einschränkungen werden nicht unterstützt. Das Netzwerk sollte direkt in den Netzwerkregeln im Kubernetes-Cluster selbst verarbeitet werden.

### <a name="are-managed-identities-supported"></a>Werden verwaltete Identitäten unterstützt?

Nein. Apps können keine verwalteten Identitäten zugewiesen werden, wenn sie in Azure Arc ausgeführt werden. Wenn Ihre App eine Identität für die Arbeit mit einer anderen Azure-Ressource benötigt, sollten Sie stattdessen einen [Anwendungsdienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) verwenden.

### <a name="are-there-any-scaling-limits"></a>Gibt es Grenzen in Bezug auf die Skalierung?

Alle mit Azure App Service in Kubernetes bereitgestellten Anwendungen mit Azure Arc lassen sich innerhalb der Grenzwerte des zugrunde liegenden Kubernetes-Clusters skalieren.  Wenn die Computeressourcen (hauptsächlich CPU und Arbeitsspeicher) des zugrunde liegenden Kubernetes-Clusters zur Neige gehen, können Anwendungen nur auf die Anzahl von Anwendungsinstanzen skaliert werden, die Kubernetes mit den verfügbaren Ressourcen planen kann.

### <a name="what-logs-are-collected"></a>Welche Protokolle werden gesammelt?

Protokolle für Systemkomponenten und Ihre Anwendungen werden in die Standardausgabe geschrieben. Beide Protokolltypen können für die Analyse mithilfe von Kubernetes-Standardtools gesammelt werden. Sie können die App Service Clustererweiterung auch mit einem [Log Analytics-Arbeitsbereich](../azure-monitor/logs/log-analytics-overview.md) konfigurieren, und alle Protokolle werden an diesen Arbeitsbereich gesendet.

Standardmäßig werden Protokolle von Systemkomponenten an das Azure-Team gesendet. Anwendungsprotokolle werden nicht gesendet. Sie können verhindern, dass diese Protokolle übertragen werden, indem Sie `logProcessor.enabled=false` als Erweiterungskonfigurationseinstellung festlegen. Dadurch wird auch die Weiterleitung der Anwendung an Ihren Log Analytics-Arbeitsbereich deaktiviert. Das Deaktivieren des Protokollprozessors kann sich auf die Zeit auswirken, die für Supportfälle benötigt wird, und Sie werden aufgefordert, Protokolle aus der Standardausgabe auf andere Weise zu erfassen.

### <a name="what-do-i-do-if-i-see-a-provider-registration-error"></a>Was kann ich tun, wenn ein Anbieterregistrierungsfehler angezeigt wird?

Beim Erstellen einer Kubernetes-Umgebungsressource wird für einige Abonnements möglicherweise der Fehler „Keinen registrierten Ressourcenanbieter gefunden“ angezeigt. Die Fehlerdetails können eine Reihe von Speicherorten und API-Versionen enthalten, die als gültig betrachtet werden. In diesem Fall muss das Abonnement möglicherweise erneut beim „Microsoft.Web“-Anbieter registriert werden. Dies ist ein Vorgang, der keine Auswirkungen auf vorhandene Anwendungen oder APIs hat. Verwenden Sie zum erneuten Registrieren die Azure CLI, um `az provider register --namespace Microsoft.Web --wait` auszuführen. Versuchen Sie dann erneut, den Kubernetes-Umgebungsbefehl auszuführen.

### <a name="can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster"></a>Kann ich die Anwendungsdiensterweiterung in einem ARM64-basierten Cluster bereitstellen?

ARM64-basierte Cluster werden derzeit nicht unterstützt.  

## <a name="extension-release-notes"></a>Versionshinweise zur Erweiterung

### <a name="application-services-extension-v-090-may-2021"></a>Anwendungsdiensterweiterung, Version 0.9.0 (Mai 2021)

- Erstes öffentliches Release der Vorschauversion der Anwendungsdiensterweiterung
- Unterstützung für code- und containerbasierte Bereitstellungen von Web-, Funktions- und Logikanwendungen.
- Runtimeunterstützung für Webanwendungen: .NET 3.1 und 5.0; Node.js 12 und 14; Python 3.6, 3.7 und 3.8; PHP 7.3 und 7.4; Ruby 2.5, 2.5.5, 2.6 und 2.6.2; Java SE 8u232, 8u242, 8u252, 11.05, 11.06 und 11.07; Tomcat 8.5, 8.5.41, 8.5.53, 8.5.57, 9.0, 9.0.20, 9.0.33, und 9.0.37.

### <a name="application-services-extension-v-0100-november-2021"></a>Anwendungsdiensterweiterung, Version 0.10.0 (November 2021)

Wenn Ihre Erweiterung in der letzten stabilen Version war und „auto-upgrade-minor-version“ auf aktiviert festgelegt ist, wird die Erweiterung automatisch aktualisiert.  Um die Erweiterung manuell auf die neueste Version zu aktualisieren, können Sie den Befehl weiter unten ausführen.

- Die Anforderung für vorab zugewiesene statisch IP-Adressen, die für die Zuweisung zum Envoy-Endpunkt erforderlich sind, wurde entfernt
- Upgrade von Keda auf Version 2.4.0
- Upgrade von Envoy auf Version 1.19.0
- Upgrade der Azure Functions-Runtime auf Version 3.3.1
- Legen Sie die Standardreplikatanzahl von App Controller und Envoy Controller auf 2 fest, um weitere Stabilität zu schaffen.

Wenn Ihre Erweiterung in der stabilen Version war und „auto-upgrade-minor-version“ auf TRUE festgelegt ist, wird die Erweiterung automatisch aktualisiert.  Um die Erweiterung manuell auf die neueste Version zu aktualisieren, können Sie den folgenden Befehl ausführen:

```azurecli-interactive
    az k8s-extension update --cluster-type connectedClusters -c <clustername> -g <resource group> -n <extension name> --release-train stable --version 0.10.0
```


## <a name="next-steps"></a>Nächste Schritte

[Erstellen in der App Service Kubernetes-Umgebung (Vorschau)](manage-create-arc-environment.md)
