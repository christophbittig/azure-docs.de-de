---
title: Bereitstellen eines selbstgehosteten Gateways für Kubernetes
description: Erfahren Sie, wie Sie eine selbstgehostete Gatewaykomponente von Azure API Management für Kubernetes bereitstellen.
author: dlepow
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: danlep
ms.date: 05/25/2021
ms.openlocfilehash: 5ab029b5080ae06276119d733f081974ba42ff9b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708449"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Bereitstellen eines selbstgehosteten Gateways für Kubernetes

In diesem Artikel werden die Schritte für die Bereitstellung der selbstgehosteten Gatewaykomponente von Azure API Management für einen Kubernetes-Cluster beschrieben.

> [!NOTE]
> Sie können auch das selbstgehostete Gateway in einem [Kubernetes-Cluster mit Azure Arc-Unterstützung](how-to-deploy-self-hosted-gateway-azure-arc.md) als [Clustererweiterung](../azure-arc/kubernetes/extensions.md) bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
- Erstellen Sie einen Kubernetes-Cluster.
   > [!TIP]
   > [Cluster mit einem einzelnen Knoten](https://kubernetes.io/docs/setup/#learning-environment) eignen sich gut für Entwicklungs- und Evaluierungszwecke. Verwenden Sie für Produktionsworkloads einen [für Kubernetes zertifizierten](https://kubernetes.io/partners/#conformance) Cluster mit mehreren Knoten, entweder lokal oder in der Cloud.
- [Stellen Sie eine selbstgehostete Gatewayressource in Ihrer API Management-Instanz bereit](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Bereitstellen in Kubernetes

1. Wählen Sie **Gateways** unter **Bereitstellung und Infrastruktur** aus.
2. Wählen Sie die selbstgehostete Gatewayressource aus, die Sie bereitstellen möchten.
3. Wählen Sie **Bereitstellung** aus.
4. Im Textfeld **Token** wurde auf Grundlage der Standardwerte **Ablauf** und **Geheimer Schlüssel** automatisch ein Zugriffstoken für Sie generiert. Wählen Sie bei Bedarf Werte in einem oder beiden Steuerelementen aus, um ein neues Token zu generieren.
5. Wählen Sie unter **Bereitstellungsskripts** die Registerkarte **Kubernetes** aus.
6. Wählen Sie den Link zur Datei **\<gateway-name\>.yml** aus, und laden Sie die YAML-Datei herunter.
7. Wählen Sie rechts unten im Textfeld **Bereitstellen** das Symbol **Kopieren** aus, um die `kubectl`-Befehle in der Zwischenablage zu speichern.
8. Fügen Sie die Befehle in das Terminalfenster (oder Befehlsfenster) ein. Mit dem ersten Befehl wird ein Kubernetes-Geheimnis erstellt, welches das in Schritt 4 generierte Zugriffstoken enthält. Der zweite Befehl wendet die in Schritt 6 heruntergeladene Konfigurationsdatei auf den Kubernetes-Cluster an und erwartet, dass sich die Datei im aktuellen Verzeichnis befindet.
9. Führen Sie die Befehle aus, um die erforderlichen Kubernetes-Objekte im [Standardnamespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) zu erstellen. Starten Sie die selbstgehosteten Gatewaypods aus dem [Containerimage](https://aka.ms/apim/sputnik/dhub), das aus der Microsoft Container Registry heruntergeladen wurde.
10. Führen Sie den folgenden Befehl aus, um zu prüfen, ob die Bereitstellung erfolgreich war. Beachten Sie, dass es einige Zeit dauern kann, bis alle Objekte erstellt sind und die Pods initialisiert wurden.

    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Dienst erfolgreich erstellt wurde. Beachten Sie, dass sich die IP-Adressen und Ports Ihrer Dienste unterscheiden.

    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
1. Kehren Sie zurück zum Azure-Portal, und wählen Sie **Übersicht** aus.
1. Vergewissern Sie sich, dass in **Status** ein grünes Häkchen angezeigt wird, gefolgt von einer Knotenanzahl, die der Anzahl der in der YAML-Datei angegebenen Replikate entspricht. Dieser Status bedeutet, dass die bereitgestellten selbstgehosteten Gatewaypods erfolgreich mit dem API Management-Dienst kommunizieren und einen regelmäßigen „Takt“ aufweisen.

    ![Gatewaystatus](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Führen Sie den Befehl `kubectl logs deployment/<gateway-name>` aus, um Protokolle von einem zufällig ausgewählten Pod anzuzeigen, falls es mehrere gibt.
> Führen Sie `kubectl logs -h` aus, um eine vollständige Übersicht über alle Befehlsoptionen zu erhalten, wie z. B. Protokolle für einen bestimmten Pod oder Container.

## <a name="production-deployment-considerations"></a>Überlegungen zur Bereitstellung in der Produktion

### <a name="access-token"></a>Zugriffstoken
Ohne ein gültiges Zugriffstoken kann ein selbstgehostetes Gateway nicht am Endpunkt des zugeordneten API Management-Diensts auf Konfigurationsdaten zugreifen und diese herunterzuladen. Das Zugriffstoken ist maximal 30 Tage gültig. Es muss neu generiert und der Cluster entweder manuell oder mittels Automatisierung mit einem neuen Token konfiguriert werden, bevor es abläuft.

Wenn Sie die Tokenaktualisierung automatisieren, generieren Sie über diesen [Vorgang](/rest/api/apimanagement/2020-12-01/gateway/generate-token) der Verwaltungs-API ein neues Token. Informationen zur Verwaltung von Kubernetes-Geheimnissen finden Sie auf der [Kubernetes-Website](https://kubernetes.io/docs/concepts/configuration/secret).

### <a name="namespace"></a>Namespace
Kubernetes-[Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) helfen bei der Aufteilung eines einzelnen Clusters unter mehreren Teams, Projekten oder Anwendungen. Namespaces stellen einen Bereich für Ressourcen und Namen bereit. Sie können einem Ressourcenkontingent und Zugriffssteuerungsrichtlinien zugeordnet werden.

Der Azure-Portal bietet Befehle zum Erstellen von selbstgehosteten Gatewayressourcen im **Standard**-Namespace. Dieser Namespace wird automatisch erstellt, ist in jedem Cluster vorhanden und kann nicht gelöscht werden.
Erwägen Sie das[Erstellen und Bereitstellen](https://www.kubernetesbyexample.com/) eines selbstgehosteten Gateways in einem separaten Namespace in der Produktion.

### <a name="number-of-replicas"></a>Anzahl von Replikaten
Die Mindestanzahl der für die Produktion geeigneten Replikate ist zwei.

Standardmäßig wird ein selbstgehostetes Gateway mithilfe einer [Bereitstellungsstrategie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy) des Typs **RollingUpdate** bereitgestellt. Überprüfen Sie die Standardwerte, und erwägen Sie das explizite Festlegen der Felder [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) und [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge), insbesondere bei Verwendung einer hohen Anzahl von Replikaten.

### <a name="container-resources"></a>Containerressourcen
Standardmäßig gibt die im Azure-Portal bereitgestellte YAML-Datei keine Anforderungen von Containerressourcen an.

Es ist unmöglich, die Menge an CPU- und Arbeitsspeicherressourcen pro Container und die Anzahl der Replikate, die zur Unterstützung einer bestimmten Workload erforderlich sind, zuverlässig vorherzusagen und zu empfehlen. Viele Faktoren spielen eine Rolle, z. B.:

- Spezifische Hardware, auf der der Cluster ausgeführt wird
- Vorhandensein und Typ der Virtualisierung
- Anzahl und Rate gleichzeitiger Clientverbindungen
- Anforderungsrate
- Art und Anzahl der konfigurierten Richtlinien
- Nutzlastgröße und ob Nutzlasten gepuffert oder gestreamt werden
- Wartezeit des Back-End-Diensts

Wir empfehlen, Ressourcenanforderungen auf zwei Kerne und 2 GiB als Ausgangspunkt festzulegen. Führen Sie einen Auslastungstest durch, und skalieren Sie basierend auf den Ergebnissen hoch/auf bzw. herunter/ab.

### <a name="container-image-tag"></a>Tag für Containerimage
Die im Azure-Portal bereitgestellte YAML-Datei verwendet das Tag **latest**. Dieses Tag verweist stets auf die neueste Version des Containerimages des selbstgehosteten Gateways.

Erwägen Sie die Verwendung eines speziellen Versionstags in der Produktion, um eine unbeabsichtigte Aktualisierung auf eine neuere Version zu vermeiden.

Sie können [eine vollständige Liste der verfügbaren Tags herunterladen](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>DNS-Richtlinie
Die DNS-Namensauflösung spielt eine entscheidende Rolle bei der Fähigkeit des selbstgehosteten Gateways, eine Verbindung mit Abhängigkeiten in Azure herzustellen und API-Aufrufe an Back-End-Dienste zu senden.

Die im Azure-Portal bereitgestellte YAML-Datei wendet die Standardrichtlinie [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) an. Diese Richtlinie bewirkt, dass vom Cluster-DNS nicht aufgelöste Namensauflösungsanforderungen an den vom Knoten geerbten vorgelagerten DNS-Server weitergeleitet werden.

Weitere Informationen zur Namensauflösung in Kubernetes finden Sie auf der [Kubernetes-Website](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service). Erwägen Sie, die [DNS-Richtlinie](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) oder [DNS-Konfiguration](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) entsprechend Ihrer Einrichtung anzupassen.

### <a name="external-traffic-policy"></a>Richtlinie für externen Datenverkehr
Die im Azure-Portal bereitgestellte YAML-Datei legt das Feld `externalTrafficPolicy` des [Service](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core)-Objekts auf `Local` fest. Hierdurch bleibt die IP-Adresse des Aufrufers erhalten (Zugriffsmöglichkeit darauf im [Anforderungskontext](api-management-policy-expressions.md#ContextVariables)), und der knotenübergreifende Lastenausgleich wird deaktiviert, sodass dadurch verursachte Netzwerkhops beseitigt werden. Beachten Sie, dass diese Einstellung eine asymmetrische Verteilung des Datenverkehrs in Bereitstellungen mit ungleicher Anzahl von Gatewaypods pro Knoten verursachen kann.

### <a name="custom-domain-names-and-ssl-certificates"></a>Benutzerdefinierte Domänennamen und SSL-Zertifikate

Wenn Sie benutzerdefinierte Domänennamen für die API Management-Endpunkte und insbesondere einen benutzerdefinierten Domänennamen für den Verwaltungsendpunkt verwenden, müssen Sie unter Umständen den Wert `config.service.endpoint` in der Datei **\<gateway-name\>.yaml** aktualisieren, um den Standarddomänennamen durch den benutzerdefinierten Domänennamen zu ersetzen. Vergewissern Sie sich, dass auf den Verwaltungsendpunkt über den Pod des selbstgehosteten Gateways im Kubernetes-Cluster zugegriffen werden kann.

Wenn das vom Verwaltungsendpunkt verwaltete SSL-Zertifikat nicht von einem bekannten Zertifizierungsstellenzertifikat signiert ist, müssen Sie in diesem Szenario sicherstellen, dass das Zertifizierungsstellenzertifikat vom Pod des selbstgehosteten Gateways als vertrauenswürdig eingestuft wird.

### <a name="configuration-backup"></a>Sicherung der Konfiguration
Weitere Informationen zum Verhalten selbstgehosteter Gateways bei einem vorübergehenden Ausfall der Verbindung mit Azure finden Sie unter [Selbstgehostetes Gateway – Übersicht](self-hosted-gateway-overview.md#connectivity-to-azure).

Konfigurieren Sie ein lokales Speichervolume für den selbstgehosteten Gatewaycontainer, damit dieser eine Sicherungskopie der zuletzt heruntergeladenen Konfiguration speichert. Bei Ausfall der Verbindung kann das Speichervolume die Sicherungskopie nach einem Neustart verwenden. Der Einbindungspfad des Volumes muss <code>/apim/config</code> sein. Ein Beispiel finden Sie auf [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Informationen zu Speicher in Kubernetes finden Sie auf der [Kubernetes-Website](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Lokale Protokolle und Metriken
Das selbstgehostete Gateway sendet Telemetriedaten an [Azure Monitor](api-management-howto-use-azure-monitor.md) und [Azure Application Insights](api-management-howto-app-insights.md) gemäß den Konfigurationseinstellungen im zugehörigen API Management-Dienst.
Wenn die [Verbindung mit Azure](self-hosted-gateway-overview.md#connectivity-to-azure) vorübergehend unterbrochen wird, wird der Fluss der Telemetriedaten in Azure unterbrochen, sodass die Daten für die Dauer des Ausfalls verloren gehen.
Erwägen Sie das [Einrichten einer lokalen Überwachung](how-to-configure-local-metrics-logs.md), um die Fähigkeit zur Beobachtung des API-Datenverkehrs sicherzustellen und den Verlust von Telemetriedaten bei Ausfall der Verbindung mit Azure zu verhindern.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum selbstgehosteten Gateway finden Sie unter [Selbstgehostetes Gateway – Übersicht](self-hosted-gateway-overview.md).
* Hier erfahren Sie, wie Sie [das selbstgehostete API Management-Gateway für Kubernetes-Cluster mit Azure Arc-Unterstützung bereitstellen](how-to-deploy-self-hosted-gateway-azure-arc.md).
