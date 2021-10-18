---
title: Überwachen von Azure-Diensten und -Anwendungen mit Grafana
description: Es wird beschrieben, wie Sie Azure Monitor- und Application Insights-Daten weiterleiten, um sie in Grafana anzuzeigen.
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 65bd221bd2f5574db33e6c164f75bb0760dabd3b
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812029"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Überwachen Ihrer Azure-Dienste in Grafana
Sie können Azure-Dienste und -Anwendungen mit [Grafana](https://grafana.com/) und dem darin enthaltenen [Azure Monitor-Datenquellen-Plug-In](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/) überwachen. Das Plug-In ruft Daten aus drei Azure-Diensten ab:
- Azure Monitor-Metriken für numerische Zeitreihendaten aus Daten aus Azure-Ressourcen. 
- Azure Monitor-Protokolle für Protokoll- und Leistungsdaten aus Azure-Ressourcen, mit denen Sie Abfragen mithilfe von Kusto Query Language (KQL) ausführen können.
- Azure Resource Graph, um Azure-Ressourcen abonnementübergreifend schnell abzufragen und zu identifizieren.

Anschließend können Sie diese Leistungs- und Verfügbarkeitsdaten in Ihren Grafana-Dashboards anzeigen.

Verwenden Sie die folgenden Schritte, um einen Grafana-Server einzurichten, sofort einsatzbereite Azure Monitor Dashboards zu verwenden und benutzerdefinierte Dashboards mit Metriken und Protokollen aus Azure Monitor zu erstellen.

## <a name="set-up-a-grafana-server"></a>Einrichten eines Grafana-Servers

### <a name="set-up-grafana-locally"></a>Lokales Einrichten von Grafana
Um einen lokalen Grafana-Server einzurichten, müssen Sie [Grafana herunterladen und in Ihrer lokalen Umgebung installieren](https://grafana.com/grafana/download).

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Einrichten von Grafana in Azure über den Azure Marketplace
1. Navigieren Sie zum Azure Marketplace, und wählen Sie Grafana von Grafana Labs aus.

2. Geben Sie Namen und Details an. Erstellen Sie eine neue Ressourcengruppe. Bewahren Sie die Informationen auf, die Sie für den VM-Benutzernamen, das VM-Kennwort und das Grafana-Serveradministratorkennwort angeben.  

3. Wählen Sie die VM-Größe und ein Speicherkonto aus.

4. Konfigurieren Sie die Einstellungen für die Netzwerkkonfiguration.

5. Zeigen Sie die Zusammenfassung an, und wählen Sie **Erstellen**, nachdem Sie die Nutzungsbedingungen akzeptiert haben.

6. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**. Eine Liste mit den neu erstellten Ressourcen wird angezeigt.

    ![Grafana-Ressourcengruppenobjekte](media/grafana-plugin/grafana-resource-group.png)

    Wenn Sie die Netzwerksicherheitsgruppe auswählen (hier *grafana-nsg*), sehen Sie, dass Port 3000 zum Zugreifen auf den Grafana-Server verwendet wird.

7. Rufen Sie die öffentliche IP-Adresse Ihres Grafana-Servers ab: Gehen Sie zurück zur Ressourcenliste, und wählen Sie **öffentliche IP-Adresse** aus.

## <a name="sign-in-to-grafana"></a>Anmelden an Grafana

> [!IMPORTANT]
> Internet Explorer und ältere Microsoft Edge-Browser sind mit Grafana nicht kompatibel. Sie müssen einen Chromium-basierten Browser einschließlich der aktuellen Version von Microsoft Edge verwenden. Weitere Informationen finden Sie unter [Unterstützte Browser für Grafana](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers).

1. Verwenden Sie die IP-Adresse Ihres Servers, und öffnen Sie die Anmeldeseite unter *http://\<IP address\>:3000* oder *\<DNSName>\:3000* in Ihrem Browser. 3000 ist zwar der Standardport, aber beachten Sie, dass Sie bei der Einrichtung möglicherweise einen anderen Port ausgewählt haben. Es sollte eine Anmeldeseite für den zuvor erstellten Grafana-Server angezeigt werden.

    ![Grafana-Anmeldebildschirm](./media/grafana-plugin/login-screen.png)

2. Melden Sie sich mit dem Benutzernamen *admin* und dem zuvor erstellten Grafana-Serveradministratorkennwort an. Falls Sie eine lokale Einrichtung verwenden, lautet das Standardkennwort *admin*. Bei Ihrer ersten Anmeldung werden Sie aufgefordert, es zu ändern.

## <a name="configure-data-source-plugin"></a>Konfigurieren des Datenquellen-Plug-Ins

Nachdem Sie sich erfolgreich angemeldet haben, sollte die Option zum Hinzufügen Ihrer ersten Datenquelle angezeigt werden.

![Datenquelle hinzufügen](./media/grafana-plugin/add-data-source.png)

1. Wählen Sie **Datenquelle hinzufügen** aus, filtern Sie nach dem Namen *Azure*, und wählen Sie die Datenquelle **Azure Monitor** aus.

![Azure Monitor-Datenquelle](./media/grafana-plugin/azure-monitor-data-source-list.png)

2. Wählen Sie einen Namen für die Datenquelle aus, und wählen Sie für Authentifizierung zwischen verwalteter Identität oder App-Registrierung aus.

Wenn Ihre Grafana-Instanz auf einer Azure-VM mit aktivierter verwalteter Identität gehostet wird, können Sie diesen Ansatz für Authentifizierung verwenden. Wenn Ihre Grafana-Instanz jedoch nicht in Azure gehostet wird oder die verwaltete Identität nicht aktiviert ist, müssen Sie die App-Registrierung mit einem Azure-Dienstprinzipal verwenden, um Authentifizierung einzurichten.

### <a name="use-managed-identity"></a>Verwenden der verwalteten Identität

1. Aktivieren Sie die verwaltete Identität auf Ihrer VM, und ändern Sie die Unterstützungseinstellung für die verwaltete Identität des Grafana-Servers in TRUE.
    * Der verwalteten Identität Ihrer Host-VM muss die Rolle [Überwachungsleser](/azure/azure-monitor/roles-permissions-security) für das Abonnement, die Ressourcengruppe oder die Ressourcen zugewiesen sein, die Sie in Grafana visualisieren.
    * Darüber hinaus müssen Sie die Einstellung „managed_identity_enabled = true“ in der Grafana-Serverkonfiguration aktualisieren. Weitere Informationen finden Sie unter [Grafana-Konfiguration](https://grafana.com/docs/grafana/latest/administration/configuration/). Sobald beide Schritte abgeschlossen sind, können Sie die Einstellungen speichern und den Zugriff testen.

2. Wählen Sie **Speichern und testen** aus, und Grafana testet die Anmeldeinformationen. Es wird in etwa folgende Meldung angezeigt:  
    
   ![Grafana data source managed identity config approved (Konfiguration der verwalteten Identität der Grafana-Datenquelle genehmigt)](./media/grafana-plugin/managed-identity.png)

### <a name="or-use-app-registration"></a>Alternativ können Sie die App-Registrierung verwenden.

1. Erstellen Sie einen Dienstprinzipal: Grafana verwendet zum Herstellen einer Verbindung mit Azure Monitor-APIs und zum Sammeln von Daten einen Azure Active Directory-Dienstprinzipal. Sie müssen einen Dienstprinzipal erstellen (oder einen vorhandenen Dienstprinzipal verwenden), um den Zugriff auf Ihre Azure-Ressourcen zu verwalten.
    * Informationen zum Erstellen eines Dienstprinzipals finden Sie in [dieser Anleitung](/azure/active-directory/develop/howto-create-service-principal-portal). Kopieren und speichern Sie Ihre Mandanten-ID (Verzeichnis-ID), Client-ID (Anwendungs-ID) und den geheimen Clientschlüssel (Wert des Anwendungsschlüssels).
    * Weitere Informationen finden Sie unter [Zuweisen einer Anwendung zu einer Rolle](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application), um der Azure Active Directory-Anwendung für das Abonnement, die Ressourcengruppe oder die Ressource, die Sie überwachen möchten, die Rolle [Überwachungsleser](/azure/azure-monitor/roles-permissions-security) zuzuweisen.
  
2. Geben Sie die Verbindungsdetails an, die Sie verwenden möchten.
    * Bei der Konfiguration des Plug-Ins können Sie angeben, welche Azure-Cloud (öffentlich, Azure US Government, Azure Deutschland oder Azure China) das Plug-In überwachen soll.
        > [!NOTE]
        > Einige Datenquellenfelder sind anders benannt als die entsprechenden Azure-Einstellungen:
        > * Die Mandanten-ID ist die Azure-Verzeichnis-ID
        > * Die Client-ID ist die Azure Active Directory-Anwendungs-ID
        > * Der geheime Clientschlüssel ist der Wert des Azure Active Directory-Anwendungsschlüssels

3. Wählen Sie **Speichern und testen** aus, und Grafana testet die Anmeldeinformationen. Es wird in etwa folgende Meldung angezeigt:  
    
   ![Grafana data source app registration config approved (Konfiguration der Grafana-Datenquellen-App-Registrierung genehmigt)](./media/grafana-plugin/app-registration.png)

## <a name="use-azure-monitor-data-source-dashboards"></a>Verwenden von Azure Monitor-Datenquellendashboards

Das Azure Monitor-Plug-In enthält mehrere sofort einsatzbereite Dashboards, die Sie für den Einstieg importieren können.

1. Klicken Sie auf die Registerkarte **Dashboards** des Azure Monitor-Plug-Ins, um eine Liste der verfügbaren Dashboards anzuzeigen.

   ![Azure Monitor-Datenquellendashboards](./media/grafana-plugin/azure-data-source-dashboards.png)

2. Klicken Sie auf **Importieren**, um ein Dashboard herunterzuladen.

3. Klicken Sie auf den Namen des importierten Dashboards, um es zu öffnen.

4. Verwenden Sie die Dropdownauswahl am oberen Rand des Dashboards, um das gewünschte Abonnement, die Ressourcengruppe und die Ressource auszuwählen.

   ![Storage Insights-Dashboards](./media/grafana-plugin/storage-insights-dashboard.png)

## <a name="build-grafana-dashboards"></a>Erstellen von Grafana-Dashboards

1. Navigieren Sie zur Grafana-Homepage, und wählen Sie **Create your first dashboard** (Erstes Dashboard erstellen) aus.

2. Wählen Sie im neuen Dashboard die Option **Add an empty panel** (Leeren Bereich hinzufügen) aus.

3. Auf Ihrem Dashboard wird ein leerer *Zeitreihenbereich* und darunter ein Abfrage-Editor angezeigt. Wählen Sie die Azure Monitor-Datenquelle aus, die Sie konfiguriert haben.
   * Erfassen von Azure Monitor-Metriken: Wählen Sie **Metriken** in der Dropdownliste der Dienste aus. Es wird eine Liste der Selektoren angezeigt wird, in der Sie die Ressourcen und die Metrik auswählen können, die in diesem Diagramm überwacht werden sollen. Verwenden Sie den Namespace **Microsoft.Compute/VirtualMachines**, um Metriken von einem virtuellen Computer zu sammeln. Nachdem Sie die virtuellen Computer und die Metriken ausgewählt haben, können Sie damit beginnen, die Daten im Dashboard anzuzeigen.
     ![Grafana-Diagrammkonfiguration für Azure Monitor-Metriken](./media/grafana-plugin/metrics-config.png)
   * Erfassen von Azure Monitor-Protokolldaten: Wählen Sie **Protokolle** in der Dropdownliste der Dienste aus. Wählen Sie die Ressource oder den Log Analytics-Arbeitsbereich aus, die bzw. den Sie abfragen möchten, und legen Sie den Text der Abfrage fest. Beachten Sie, dass Sie mit dem Azure Monitor-Plug-In die Protokolle nach bestimmten Ressourcen oder aus einem Log Analytics-Arbeitsbereich abfragen können. Im Abfrage-Editor unten können Sie jede Protokollabfrage kopieren, über die Sie bereits verfügen, oder eine neue Abfrage erstellen. Während Sie die Abfrage eingeben, wird IntelliSense aktiv und schlägt Optionen zur automatischen Vervollständigung vor. Wählen Sie schließlich den Visualisierungstyp **Zeitreihe** aus, und führen Sie die Abfrage aus.
    
     > [!NOTE]
     >
     > Die Standardabfrage, die im Lieferumfang des Plug-Ins enthalten ist, verwendet zwei Makros: $__timeFilter() und $__interval. 
     > Anhand dieser Makros kann Grafana dynamisch den Zeitbereich und das Aggregationsintervall berechnen, wenn Sie einen Teil des Diagramms vergrößern. Sie können diese Makros entfernen und einen Standardzeitfilter wie *TimeGenerated > ago(1h)* verwenden, was allerdings bedeutet, dass die Vergrößerungsfunktion im Diagramm nicht unterstützt wird.
    
     Das folgende Beispiel zeigt eine Abfrage, die für eine Application Insights-Ressource ausgeführt wird, um die durchschnittliche Antwortzeit für alle Anforderungen zu ermitteln.

     ![Grafana-Diagrammkonfiguration für Azure Log Analytics](./media/grafana-plugin/logs-config.png)

    * Zusätzlich zu den oben gezeigten Metrik- und Protokollabfragen unterstützt das Azure Monitor-Plug-In [Azure Resource Graph](/azure/governance/resource-graph/concepts/explore-resources)-Abfragen.

## <a name="advanced-grafana-features"></a>Erweiterte Features von Grafana

### <a name="variables"></a>Variables
Einige Ressourcen- und Abfragewerte können vom Dashboardbenutzer über Dropdownlisten der Benutzeroberfläche ausgewählt und in der Ressource oder der Abfrage aktualisiert werden.
Die folgende Abfrage ist ein Beispiel für die Verwendung eines Log Analytics-Arbeitsbereichs:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Sie können eine Variable konfigurieren, die alle verfügbaren **Arbeitsbereiche** auflistet und dann die Ressource aktualisiert, die auf der Grundlage einer Benutzerauswahl abgefragt wird.
Klicken Sie zum Erstellen einer neuen Variable auf dem Dashboard oben rechts auf die Schaltfläche „Einstellungen“, wählen Sie **Variablen** und dann **Neu** aus.
Definieren Sie auf der Seite mit den Variablen die Datenquelle und die Abfrage, die zum Abrufen der Werteliste ausgeführt werden soll.

![Definieren einer Grafana-Variable](./media/grafana-plugin/define-variable.png)

Ändern Sie nach der Erstellung die Ressource für die Abfrage so, dass die ausgewählten Werte verwendet werden. Ihre Diagramme zeigen eine entsprechende Reaktion:

![Abfrage mit Variable](./media/grafana-plugin/query-with-variable.png)

Sehen Sie sich die vollständige Liste der [Vorlagenvariablen](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/) an, die im Azure Monitor-Plug-In verfügbar sind.

### <a name="create-dashboard-playlists"></a>Erstellen von Dashboard-Wiedergabelisten

Eines der vielen nützlichen Features von Grafana ist die Dashboard-Wiedergabeliste. Sie können mehrere Dashboards erstellen und einer Wiedergabeliste hinzufügen und dann ein Intervall für die Anzeige jedes Dashboards konfigurieren. Navigieren Sie zum Menüelement „Dashboards“, und wählen Sie **Wiedergabelisten** aus, um eine Wiedergabeliste vorhandener Dashboards zu erstellen, die durchlaufen werden sollen. Sie können sie beispielsweise auf einem großen Monitor an der Wand als „Statusübersicht“ für Ihre Gruppe anzeigen.

![Grafana-Beispiel für eine Wiedergabeliste](./media/grafana-plugin/playlist.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine Grafana-Umgebung in Azure eingerichtet haben, werden Ihnen für ausgeführte virtuelle Computer (unabhängig davon, ob Sie diese nutzen oder nicht) Gebühren berechnet. Bereinigen Sie die in diesem Artikel erstellten Ressourcen, um zu verhindern, dass zusätzliche Gebühren anfallen.

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen** und dann auf **Grafana**.
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld **Grafana** ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte
* [Vergleich von Azure Monitor-Metriken und -Protokollen](../data-platform.md)
