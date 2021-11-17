---
title: Datenvisualisierung in Echtzeit von Daten aus Azure IoT Hub – Power BI
description: Verwenden Sie Power BI, um Temperatur- und Luftfeuchtigkeitsdaten visuell darzustellen, die der Sensor gesammelt und an Ihren Azure IoT Hub gesendet hat.
author: eross-msft
keywords: Datenvisualisierung in Echtzeit, Visualisierung von Livedaten, Visualisierung von Sensordaten
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/23/2021
ms.author: lizross
ms.openlocfilehash: 15e297f65aad93cd3999ae44953a3410c7281788
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555048"
---
# <a name="tutorial-visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Tutorial: Visualisieren von Sensordaten aus Azure IoT Hub in Echtzeit mithilfe von Power BI

Sie können mithilfe von Microsoft Power BI aus Azure IoT Hub empfangene Sensordaten in Echtzeit visualisieren. Dazu konfigurieren Sie einen Azure Stream Analytics-Auftrag, mit dem die Daten aus IoT Hub verarbeitet und an ein Dataset in Power BI weitergeleitet werden. 

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png" alt-text="Lückenloses Diagramm" border="false":::

 [Microsoft Power BI](https://powerbi.microsoft.com/) ist ein Tool zur Datenvisualisierung für Self-Service und Business Intelligence (BI) für umfangreiche Datasets. [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) ist ein vollständig verwalteter Echtzeitanalysedienst zum Analysieren und Verarbeiten schneller Datenströme sowie zum Abrufen von Erkenntnissen, Erstellen von Berichten oder Auslösen von Warnungen und Aktionen. 

In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> * Erstellen einer Consumergruppe für Ihren IoT-Hub
> * Erstellen und Konfigurieren eines Azure Stream Analytics-Auftrags, um Temperaturtelemetriedaten aus Ihrer Consumergruppe zu lesen und an Power BI zu senden
> * Erstellen eines Berichts mit den Temperaturdaten in Power BI und Freigeben des Berichts im Web

## <a name="prerequisites"></a>Voraussetzungen

* Arbeiten Sie den Schnellstart zum [Senden von Telemetriedaten](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) in der von Ihnen bevorzugten Entwicklungssprache durch. Alternativ können Sie eine beliebige Geräte-App verwenden, die Temperaturtelemetriedaten sendet. Siehe hierzu beispielsweise die Anleitung zum [Raspberry Pi-Onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md) oder einen der Schnellstarts zu [eingebetteten Geräten](../iot-develop/quickstart-devkit-mxchip-az3166.md). In diesen Artikeln werden folgende Anforderungen beschrieben:
  
  * Ein aktives Azure-Abonnement.
  * Eine Azure IoT Hub-Instanz in Ihrem Abonnement
  * Eine Client-App, die Nachrichten an Ihren Azure IoT-Hub sendet.

* Ein Power BI-Konto. ([Power BI kostenlos testen](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Erstellen, Konfigurieren und Ausführen eines Stream Analytics-Auftrags

Als Erstes erstellen wir einen Stream Analytics-Auftrag. Nachdem Sie den Auftrag erstellt haben, definieren Sie die Eingaben, Ausgaben und die Abfrage zum Abrufen der Daten.

### <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Ressource erstellen**. Geben Sie im Suchfeld den Suchbegriff *Stream Analytics-Auftrag* ein, und wählen Sie ihn aus der Dropdownliste aus. Wählen Sie auf der Übersichtsseite **Stream Analytics-Auftrag** die Option **Erstellen** aus.

2. Geben Sie die folgenden Informationen für den Auftrag ein.

   **Auftragsname**: Der Name des Auftrags. Der Name muss global eindeutig sein.

   **Ressourcengruppe**: Verwenden Sie dieselbe Ressourcengruppe wie für Ihren IoT Hub.

   **Standort**: Verwenden Sie denselben Speicherort wie für Ihre Ressourcengruppe.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png" alt-text="Erstellen eines Stream Analytics-Auftrags in Azure":::

3. Klicken Sie auf **Erstellen**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Hinzufügen einer Eingabe zum Stream Analytics-Auftrag

1. Öffnen Sie den Stream Analytics-Auftrag.

2. Wählen Sie unter **Auftragstopologie** die Option **Eingaben** aus.

3. Wählen Sie im Bereich **Eingaben** die Option **Datenstromeingabe hinzufügen** und dann **IoT Hub** aus der Dropdownliste aus. Geben Sie im neuen Eingabebereich die folgenden Informationen ein:

   **Eingabealias**: Geben Sie einen eindeutigen Alias für die Eingabe ein.

   **IoT Hub aus Ihrem Abonnement auswählen**: Wählen Sie dieses Optionsfeld aus.

   **Abonnement**: Wählen Sie das Azure-Abonnement aus, das Sie für dieses Tutorial verwenden.

   **IoT Hub**: Wählen Sie den IoT Hub aus, den Sie für dieses Tutorial verwenden.

   **Endpunkt**: Wählen Sie **Messaging** aus.

   **Name der SAS-Richtlinie**: Wählen Sie den Namen der SAS-Richtlinie aus, die vom Stream Analytics-Auftrag für Ihren IoT Hub verwendet werden soll. Für dieses Tutorial können Sie *service* auswählen. Die Richtlinie *service* wird standardmäßig auf neuen IoT Hubs erstellt und erteilt die Berechtigung zum Senden und Empfangen an cloudseitigen Endpunkten, die vom IoT Hub verfügbar gemacht werden. Weitere Informationen finden Sie unter [Access Control und Berechtigungen](iot-hub-dev-guide-sas.md#access-control-and-permissions).

   **Schlüssel für SAS-Richtlinie**: Dieses Feld wird auf Grundlage des von Ihnen ausgewählten Namens der SAS-Richtlinie automatisch ausgefüllt.

   **Consumergruppe**: Wählen Sie die Consumergruppe aus, die Sie zuvor erstellt haben.

   Behalten Sie bei allen anderen Feldern die Standardwerte bei.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png" alt-text="Hinzufügen einer Eingabe zum Stream Analytics-Auftrag in Azure":::

4. Wählen Sie **Speichern** aus.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag

1. Wählen Sie unter **Auftragstopologie** die Option **Ausgaben** aus.

2. Wählen Sie im Bereich **Ausgaben** die Option **Hinzufügen** und dann **Power BI** aus der Dropdownliste aus.

3. Wählen Sie im Bereich **Power BI – Neue Ausgabe** die Option **Autorisieren** aus, und folgen Sie den Anweisungen zum Anmelden bei Ihrem Power BI-Konto.

4. Nachdem Sie sich bei Power BI angemeldet haben, geben Sie die folgenden Informationen ein:

   **Ausgabealias**: Ein eindeutiger Alias für die Ausgabe.

   **Gruppenarbeitsbereich**: Wählen Sie den Arbeitsbereich der Zielgruppe aus.

   **Datasetname**: Geben Sie einen Datasetnamen ein.

   **Tabellenname**: Geben Sie einen Tabellennamen ein.

   **Authentifizierungsmodus**: Übernehmen Sie den Standardwert.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png" alt-text="Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag in Azure":::

5. Wählen Sie **Speichern** aus.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurieren der Abfrage des Stream Analytics-Auftrags

1. Wählen Sie unter **Auftragstopologie** die Option **Abfrage** aus.

2. Ersetzen Sie `[YourInputAlias]` durch den Eingabealias des Auftrags.

3. Ersetzen Sie `[YourOutputAlias]` durch den Ausgabealias des Auftrags.

1. Fügen Sie als letzte Zeile der Abfrage die folgende `WHERE`-Klausel ein. Diese Zeile stellt sicher, dass nur Nachrichten mit der Eigenschaft **temperature** an Power BI weitergeleitet werden.

    ```sql
    WHERE temperature IS NOT NULL
    ```
1. Ihre Abfrage sollte in etwa dem folgenden Screenshot entsprechen. Wählen Sie **Abfrage speichern** aus.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png" alt-text="Hinzufügen einer Abfrage zu einem Stream Analytics-Auftrag":::

### <a name="run-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

Wählen Sie im Stream Analytics-Auftrag die Option **Übersicht** und dann **Starten** > **Jetzt** > **Starten** aus. Sobald der Auftrag erfolgreich gestartet wurde, ändert sich der Status des Auftrags von **Beendet** in **Wird ausgeführt**.

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png" alt-text="Ausführen eines Stream Analytics-Auftrags in Azure":::

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Erstellen und Veröffentlichen eines Power BI-Berichts zum Visualisieren der Daten

In den folgenden Schritten erfahren Sie, wie Sie einen Bericht mithilfe des Power BI-Diensts erstellen und veröffentlichen. Wenn Sie das „neue Design“ in Power BI verwenden möchten, können Sie diese Schritte mit einigen Änderungen ausführen. Informationen zu den Unterschieden und zum Navigieren im „neuen Design“ finden Sie unter [The „new look“ of the Power BI service](/power-bi/fundamentals/desktop-latest-update) (Das neue Design des Power BI-Diensts).

1. Stellen Sie sicher, dass die Client-App auf Ihrem Gerät ausgeführt wird.

2. Melden Sie sich bei Ihrem [Power BI](https://powerbi.microsoft.com/)-Konto an, und wählen Sie im Menü oben die Option **Power BI-Dienst** aus.

3. Wählen Sie den von Ihnen verwendeten Arbeitsbereich aus dem Seitenmenü, **Mein Arbeitsbereich**, aus.

4. Unterhalb der Registerkarte **Alle** oder auf der Registerkarte **Datasets + Dataflows** sollte das Dataset angezeigt werden, das Sie beim Erstellen der Ausgabe für den Stream Analytics-Auftrag angegeben haben.

5. Zeigen Sie auf das von Ihnen erstellte Dataset, klicken Sie auf das Menü **Weitere Optionen** (die drei Punkte rechts neben dem Datasetnamen), und wählen Sie dann **Bericht erstellen** aus.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png" alt-text="Erstellen eines Microsoft Power BI-Berichts":::

6. Erstellen Sie ein Liniendiagramm, um die Temperatur in Echtzeit im Zeitverlauf anzuzeigen.

   1. Klicken Sie auf der Seite zur Berichterstellung im Bereich **Visualisierungen** auf das Symbol für Liniendiagramme, um ein Liniendiagramm hinzuzufügen. Verwenden Sie die Führungslinien an den Seiten und Ecken des Diagramms, um dessen Größe und Position anzupassen.

   2. Erweitern Sie im Bereich **Felder** die Tabelle, die Sie beim Erstellen der Ausgabe für den Stream Analytics-Auftrag angegeben haben.

   3. Ziehen Sie **EventEnqueuedUtcTime** im Bereich **Visualisierungen** auf **Achse**.

   4. Ziehen Sie **Temperatur** auf **Werte**.

      Ein Liniendiagramm wird erstellt. Die x-Achse zeigt Datum und Uhrzeit in der Zeitzone UTC. Die y-Achse zeigt vom Sensor empfangene Temperatur an.

      :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png" alt-text="Hinzufügen eines Liniendiagramms für Temperatur zu einem Microsoft Power BI-Bericht":::

     > [!NOTE]
     > Abhängig vom Gerät oder simulierten Gerät, das Sie zum Senden von Telemetriedaten verwenden, wird möglicherweise eine etwas andere Liste mit Feldern angezeigt.
     >

8. Wählen Sie **Speichern**, um den Bericht zu speichern. Geben Sie bei Aufforderung einen Namen für Ihren Bericht ein. Wenn Sie zur Eingabe einer Vertraulichkeitsbezeichnung aufgefordert werden, können Sie **Öffentlich** und dann **Speichern** auswählen.

10. Es wird weiterhin das Berichtsfenster angezeigt. Wählen Sie **Datei** > **Bericht einbetten** > **Website oder Portal** aus.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-embed-report.png" alt-text="Auswählen der Website zum Einbetten des Microsoft Power BI-Berichts":::

    > [!NOTE]
    > Wenn eine Benachrichtigung angezeigt wird, dass Sie sich an Ihren Administrator wenden müssen, um die Erstellung von Einbindungscode zu aktivieren, dann müssen Sie das möglicherweise wirklich tun. Die Erstellung von Einbindungscode muss aktiviert werden, damit Sie diesen Schritt ausführen können.
    >
    > :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png" alt-text="Benachrichtigung „Wenden Sie sich an Ihren Administrator“":::


11. Sie erhalten einen Berichtslink, den Sie mit anderen Benutzern teilen können, um ihnen Zugriff auf den Bericht zu geben. Außerdem wird ein Codeausschnitt angezeigt, mit dem Sie den Bericht in Ihren Blog oder Ihre Website integrieren können. Kopieren Sie den Link im Fenster **Sicherer Einbettungscode**, und schließen Sie dann das Fenster.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/copy-secure-embed-code.png" alt-text="Kopieren des Links zum Einbetten des Berichts":::

12. Öffnen Sie einen Webbrowser, und fügen Sie den Link in die Adressleiste ein.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png" alt-text="Veröffentlichen eines Microsoft Power BI-Berichts":::

Microsoft bietet auch die [mobilen Power BI-Apps](https://powerbi.microsoft.com/documentation/powerbi-power-bi-apps-for-mobile-devices/) für die Anzeige von und Interaktion mit Ihren Power BI-Dashboards und -Berichten auf Ihrem mobilen Gerät.

## <a name="cleanup-resources"></a>Bereinigen von Ressourcen

In diesem Tutorial haben Sie eine Ressourcengruppe, einen IoT-Hub, einen Stream Analytics-Auftrag und ein Dataset in Power BI erstellt. 

Wenn Sie weitere Tutorials bearbeiten möchten, können Sie die Ressourcengruppe und den IoT-Hub beibehalten und später wiederverwenden. 

Wenn Sie den IoT-Hub oder die weiteren erstellten Ressourcen nicht mehr benötigen, können Sie die Ressourcengruppe im Portal löschen. Wählen Sie dazu die Ressourcengruppe aus, und klicken Sie auf **Ressourcengruppe löschen**. Wenn Sie den IoT-Hub beibehalten möchten, können Sie andere Ressourcen im Bereich **Übersicht** der Ressourcengruppe löschen. Klicken Sie hierzu mit der rechten Maustaste auf die Ressource, wählen Sie im Kontextmenü **Löschen** aus, und befolgen Sie die Anweisungen. 

### <a name="use-the-azure-cli-to-clean-up-azure-resources"></a>Verwenden der Azure CLI zum Bereinigen von Azure-Ressourcen

Verwenden Sie den Befehl [az group delete](/cli/azure/group#az_group_delete), um die Ressourcengruppe und alle zugeordneten Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name {your resource group}
```

### <a name="clean-up-power-bi-resources"></a>Bereinigen von Power BI-Ressourcen

Sie haben ein Dataset namens **PowerBiVisualizationDataSet** in Power BI erstellt. Um es zu entfernen, melden Sie sich bei Ihrem [Power BI](https://powerbi.microsoft.com/)-Konto an. Wählen im Menü auf der linken Seite unter **Arbeitsbereiche** den Eintrag **Mein Arbeitsbereich** aus. Zeigen Sie unter der Registerkarte **Datasets + Dataflows** in der Liste der Datasets auf das Dataset **PowerBiVisualizationDataSet**. Klicken Sie auf die drei Punkte rechts neben dem Datasetnamen, um das Menü **Weitere Optionen** zu öffnen. Klicken Sie dann auf **Löschen**, und folgen Sie den Anweisungen. Wenn Sie das Dataset entfernen, wird auch der Bericht entfernt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie mithilfe von Power BI Sensordaten aus Ihrem Azure IoT-Hub in Echtzeit visualisieren, indem Sie die folgenden Aufgaben ausführen:

> [!div class="checklist"]
> * Erstellen einer Consumergruppe für Ihren IoT-Hub
> * Erstellen und Konfigurieren eines Azure Stream Analytics-Auftrags, um Temperaturtelemetriedaten aus Ihrer Consumergruppe zu lesen und an Power BI zu senden
> * Konfigurieren eines Berichts für die Temperaturdaten in Power BI und Freigeben des Berichts im Web

Eine weitere Möglichkeit zum Visualisieren von Daten aus Azure IoT Hub wird im folgenden Artikel beschrieben.

> [!div class="nextstepaction"]
> [Verwenden einer Web-App zum Visualisieren von Sensordaten aus Azure IoT Hub in Echtzeit](iot-hub-live-data-visualization-in-web-apps.md)