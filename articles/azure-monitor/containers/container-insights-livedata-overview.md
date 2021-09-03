---
title: Anzeigen von Livedaten mit Container Insights | Microsoft-Dokumentation
description: In diesem Artikel wird die Echtzeitansicht von Kubernetes-Protokollen, -Ereignissen und -Podmetriken in Container Insights ohne Verwendung von kubectl beschrieben.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: references_regions
ms.openlocfilehash: 9c1cbaf09d8f0b0cfe23f7014c1b5046efc5aab9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346727"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Anzeigen von Kubernetes-Protokollen, -Ereignissen und -Podmetriken in Echtzeit

Container Insights enthält ein Feature für Livedaten, bei dem es sich um ein erweitertes Diagnosefeature handelt, das Ihnen Direktzugriff auf Ihre Azure Kubernetes Service-Containerprotokolle (stdout/stderror), -Ereignisse und -Podmetriken ermöglicht. Sie bietet direkten Zugriff auf `kubectl logs -c`, `kubectl get`-Ereignisse und `kubectl top pods`. In einem Konsolenbereich werden die von der Container-Engine generierten Protokolle, Ereignisse und Metriken angezeigt, die weitere Unterstützung bei der Behandlung von Problemen in Echtzeit bieten.

Dieser Artikel bietet eine ausführliche Übersicht und hilft Ihnen, die Verwendung dieser Funktion zu verstehen.

Hilfe bei der Einrichtung oder Problembehandlung der Funktion für Livedaten finden Sie in unserem [Einrichtungsleitfaden](container-insights-livedata-setup.md). Informationen zum Direktzugriff auf die Kubernetes-API über diese Funktion sowie weitere Informationen zum Authentifizierungsmodell finden Sie [hier](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

## <a name="view-aks-resource-live-logs"></a>Anzeigen von Liveprotokollen zu AKS-Ressourcen
Verwenden Sie das folgende Verfahren, um die Liveprotokolle für Pods, Bereitstellungen und Replikatgruppen mit oder ohne Container Insights über die AKS-Ressourcenansicht anzuzeigen.

1. Navigieren Sie im Azure-Portal zur Ressourcengruppe des AKS-Clusters, und wählen Sie die AKS-Ressource aus.

2. Wählen Sie im Abschnitt **Kubernetes-Ressourcen** das Menüelement **Workloads** aus.

3. Wählen Sie auf der entsprechenden Registerkarte einen Pod, eine Bereitstellung oder eine Replikatgruppe aus.

4. Wählen Sie im Ressourcenmenü **Liveprotokolle** aus.

5. Wählen Sie einen Pod aus, um die Sammlung der Livedaten zu starten.

    [![Liveprotokolle zu Bereitstellungen](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>Anzeigen von Protokollen

Sie können Echtzeitprotokolldaten anzeigen, da diese von der Container-Engine aus den Ansichten **Knoten**, **Controller** und **Container** generiert werden. Führen Sie zum Anzeigen von Protokolldaten die folgenden Schritte aus.

1. Navigieren Sie im Azure-Portal zur Ressourcengruppe des AKS-Clusters, und wählen Sie die AKS-Ressource aus.

2. Wählen Sie im Dashboard des AKS-Clusters links unter **Überwachung** die Option **Erkenntnisse** aus.

3. Wählen Sie entweder die Register **Knoten**, **Controller** oder **Container** aus.

4. Wählen Sie ein Objekt aus dem Leistungsraster aus, und wählen Sie dann im Eigenschaftenbereich auf der rechten Seite die Option **Livedaten anzeigen** aus. Wenn der AKS-Cluster mit einmaligem Anmelden über Azure AD konfiguriert ist, werden Sie bei der ersten Verwendung während dieser Browsersitzung zur Authentifizierung aufgefordert. Wählen Sie Ihr Konto aus und führen Sie die Authentifizierung mit Azure durch.

    >[!NOTE]
    >Wenn Sie die Daten aus Ihrem Log Analytics-Arbeitsbereich anzeigen, indem Sie im Eigenschaftenbereich die Option **In Analytics anzeigen** auswählen, können in den Ergebnissen der Protokollsuche **Knoten**, **Daemon-Sätze**, **Replikatsätze**, **Aufträge**, **Cron-Aufträge**, **Pods** und **Container** angezeigt werden, die möglicherweise nicht mehr vorhanden sind. Der Versuch, Protokolle für einen Container zu durchsuchen, der in `kubectl` nicht verfügbar ist, schlägt hier ebenfalls fehl. Weitere Informationen zum Anzeigen von Verlaufsprotokollen, Ereignissen und Metriken finden Sie unter der Funktion [Abfragen von Protokollen von Container Insights](container-insights-log-query.md).

Nach erfolgreicher Authentifizierung wird der Konsolenbereich für Livedaten unterhalb des Rasters mit Leistungsdaten angezeigt. Hier können Sie Protokolldaten in einem kontinuierlichen Stream sehen. Wenn in der Abrufstatusanzeige ein grünes Häkchen angezeigt wird, das sich ganz rechts im Bereich befindet, bedeutet das, dass Daten abgerufen werden können und in die Konsole gestreamt werden.

![Option zum Anzeigen von Daten im Eigenschaftenbereich für Knoten](./media/container-insights-livedata-overview/node-properties-pane.png)

Der Titel des Bereichs zeigt den Namen des Pods an, mit dem der Container gruppiert ist.

## <a name="view-events"></a>Anzeigen von Ereignissen

Sie können Echtzeitereignisdaten anzeigen, wenn sie von der Container-Engine bei Auswahl eines Containers, Pods, Knotens, ReplicaSets, DaemonSets, Auftrags, Cron-Auftrags oder einer Bereitstellung aus den Ansichten **Knoten**, **Controller**, **Container** und **Bereitstellungen** generiert werden. Führen Sie zum Anzeigen von Ereignissen die folgenden Schritte aus.

1. Navigieren Sie im Azure-Portal zur Ressourcengruppe des AKS-Clusters, und wählen Sie die AKS-Ressource aus.

2. Wählen Sie im Dashboard des AKS-Clusters links unter **Überwachung** die Option **Erkenntnisse** aus.

3. Wählen Sie entweder die Registerkarte **Knoten**, **Controller**, **Container** oder **Bereitstellungen** aus.

4. Wählen Sie ein Objekt aus dem Leistungsraster aus, und wählen Sie dann im Eigenschaftenbereich auf der rechten Seite die Option **Livedaten anzeigen** aus. Wenn der AKS-Cluster mit einmaligem Anmelden über Azure AD konfiguriert ist, werden Sie bei der ersten Verwendung während dieser Browsersitzung zur Authentifizierung aufgefordert. Wählen Sie Ihr Konto aus und führen Sie die Authentifizierung mit Azure durch.

    >[!NOTE]
    >Wenn Sie die Daten aus Ihrem Log Analytics-Arbeitsbereich anzeigen, indem Sie im Eigenschaftenbereich die Option **In Analytics anzeigen** auswählen, können in den Ergebnissen der Protokollsuche **Knoten**, **Daemon-Sätze**, **Replikatsätze**, **Aufträge**, **Cron-Aufträge**, **Pods** und **Container** angezeigt werden, die möglicherweise nicht mehr vorhanden sind. Der Versuch, Protokolle für einen Container zu durchsuchen, der in `kubectl` nicht verfügbar ist, schlägt hier ebenfalls fehl. Weitere Informationen zum Anzeigen von Verlaufsprotokollen, Ereignissen und Metriken finden Sie unter der Funktion [Abfragen von Protokollen von Container Insights](container-insights-log-query.md).

Nach erfolgreicher Authentifizierung wird der Konsolenbereich für Livedaten unterhalb des Rasters mit Leistungsdaten angezeigt. Wenn in der Abrufstatusanzeige ein grünes Häkchen angezeigt wird, das sich ganz rechts im Bereich befindet, bedeutet das, dass Daten abgerufen werden können und in die Konsole gestreamt werden.

Wenn das ausgewählte Objekt ein Container ist, wählen Sie die Option **Ereignisse** im Bereich aus. Wenn Sie einen Knoten, Pod oder Controller ausgewählt haben, ist das Anzeigen von Ereignissen automatisch ausgewählt.

![Anzeigen von Ereignissen im Bereich mit Controllereigenschaften](./media/container-insights-livedata-overview/controller-properties-live-event.png)

Der Titel des Bereichs zeigt den Namen des Pods an, mit dem der Container gruppiert ist.

### <a name="filter-events"></a>Filtern von Ereignissen

Beim Anzeigen von Ereignissen können Sie außerdem mit dem **Filter** rechts neben der Suchleiste die Ergebnisse einschränken. Je nachdem, welche Ressource Sie ausgewählt haben, stehen im Filterfeld Pods, Namespaces oder Cluster zur Auswahl.

## <a name="view-metrics"></a>Anzeigen von Metriken

Sie können Echtzeitmetrikdaten anzeigen, da diese von der Container-Engine bei Auswahl eines **Pods** aus der Ansicht **Knoten** oder **Controller** generiert werden. Führen Sie zum Anzeigen von Metriken die folgenden Schritte aus.

1. Navigieren Sie im Azure-Portal zur Ressourcengruppe des AKS-Clusters, und wählen Sie die AKS-Ressource aus.

2. Wählen Sie im Dashboard des AKS-Clusters links unter **Überwachung** die Option **Erkenntnisse** aus.

3. Wählen Sie entweder die Register **Knoten** oder **Controller** aus.

4. Wählen Sie ein **Pod**-Objekt aus dem Leistungsraster aus, und wählen Sie dann im Eigenschaftenbereich auf der rechten Seite die Option **Livedaten anzeigen** aus. Wenn der AKS-Cluster mit einmaligem Anmelden über Azure AD konfiguriert ist, werden Sie bei der ersten Verwendung während dieser Browsersitzung zur Authentifizierung aufgefordert. Wählen Sie Ihr Konto aus und führen Sie die Authentifizierung mit Azure durch.

    >[!NOTE]
    >Wenn Sie die Daten aus Ihrem Log Analytics-Arbeitsbereich anzeigen, indem Sie im Eigenschaftenbereich die Option **In Analytics anzeigen** auswählen, können in den Ergebnissen der Protokollsuche **Knoten**, **Daemon-Sätze**, **Replikatsätze**, **Aufträge**, **Cron-Aufträge**, **Pods** und **Container** angezeigt werden, die möglicherweise nicht mehr vorhanden sind. Der Versuch, Protokolle für einen Container zu durchsuchen, der in `kubectl` nicht verfügbar ist, schlägt hier ebenfalls fehl. Weitere Informationen zum Anzeigen von Verlaufsprotokollen, Ereignissen und Metriken finden Sie unter [Abfragen von Protokollen von Container Insights](container-insights-log-query.md).

Nach erfolgreicher Authentifizierung wird der Konsolenbereich für Livedaten unterhalb des Rasters mit Leistungsdaten angezeigt. Metrikdaten werden abgerufen und zur Darstellung in den beiden Diagrammen an Ihre Konsole gestreamt. Der Titel des Bereichs zeigt den Namen des Pods an, mit dem der Container gruppiert ist.

![Beispiel für das Anzeigen von Podmetriken](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>Verwenden der Ansichten von Livedaten
In den folgenden Abschnitten werden Funktionen beschrieben, die Sie in den verschiedenen Livedatenansichten verwenden können.

### <a name="search"></a>Suchen,
Die Funktion für Livedaten bietet Suchfunktionalität. Im Feld **Suchen** können Sie Ergebnisse filtern, indem Sie ein Schlüsselwort oder einen Begriff eingeben, und alle übereinstimmenden Ergebnisse werden zur schnellen Überprüfung hervorgehoben. Beim Anzeigen von Ereignissen können Sie außerdem mit dem **Filter** rechts neben der Suchleiste die Ergebnisse einschränken. Je nachdem, welche Ressource Sie ausgewählt haben, stehen im Filterfeld Pods, Namespaces oder Cluster zur Auswahl.

![Filterbeispiel für den Livedaten-Konsolenbereich](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![Filterbeispiel für den Livedaten-Konsolenbereich einer Bereitstellung](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>Scrollsperre und Anhalten

Mithilfe der Option **Scrollen** können Sie den automatischen Bildlauf unterbrechen und das Verhalten des Bereichs steuern, sodass Sie manuell durch die gelesenen neuen Daten scrollen können. Wählen Sie einfach erneut die Option **Scrollen** aus, um den automatischen Bildlauf wieder zu aktivieren. Sie können den Abruf von Protokoll- oder Ereignisdaten auch anhalten, indem Sie die Option **Anhalten** auswählen. Wenn Sie bereit sind, den Vorgang fortzusetzen, wählen Sie einfach **Wiedergeben** aus.

![Anhalten der Liveansicht im Livedaten-Konsolenbereich](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![Anhalten der Liveansicht im Livedaten-Konsolenbereich einer Bereitstellung](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>Es wird empfohlen, den automatischen Bildlauf bei der Problembehandlung nur für kurze Zeit anzuhalten. Diese Anforderungen können sich auf die Verfügbarkeit und Drosselung der Kubernetes-API in Ihrem Cluster auswirken.

>[!IMPORTANT]
>Während der Ausführung dieser Funktion werden keine Daten dauerhaft gespeichert. Alle während der Sitzung erfassten Informationen werden gelöscht, wenn Sie den Browser schließen oder verlassen. Daten stehen nur innerhalb des Fünf-Minuten-Zeitfensters der Metrikfunktion zur Visualisierung bereit. Alle Metriken, die älter als fünf Minuten sind, werden ebenfalls gelöscht. Die Funktion für Livedaten puffert Abfragen innerhalb angemessener Grenzwerte für die Arbeitsspeichernutzung.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verwendung von Azure Monitor und zum Überwachen anderer Aspekte Ihres AKS-Clusters finden Sie unter [Anzeigen der Azure Kubernetes Service-Integrität](container-insights-analyze.md).

- Unter [Abfragen von Protokollen von Container Insights](container-insights-log-query.md) finden Sie vordefinierte Abfragen und Beispiele zum Erstellen von Warnungen und Visualisierungen. Darüber hinaus können Sie eine weitergehende Analyse des Clusters durchführen.
